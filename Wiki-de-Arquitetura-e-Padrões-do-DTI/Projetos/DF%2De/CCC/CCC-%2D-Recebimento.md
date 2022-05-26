**Recebimento**
 
### 1) Thread TaskSendRequest:
 
- Lê valor NSUCCCInicial do banco CCC.CCC.Configuracao, chave NSUCCC_INICIAL
 
- Monta XML request com valor acima, mas como nó <ultNSUCCCMovto>. Nó <ultNSUCCC> não é enviado (e estranhamente não dá erro de schema)
 
- Envia request e valida resposta
  - Se cStat 9121 (consulta com resultado) ou 9122 (consulta com resultado, existe continuacao)
    - descomprime o nó CCCZip
    - Chama procedure [CCC].[cccRecebimento].[usp_CCC_LogXML_Create_And_UpdateNSUCCC_INICIAL], sem indicação de erro e com o valor do NSUCCCMovto (próximo a ser requisitado)
  - Outros valors de cStat
    - geram interrupção, que acabam chamando a procedure [CCC].[cccRecebimento].[usp_CCC_LogXML_Create_And_UpdateNSUCCC_INICIAL], com indicação de erro e sem o valor do NSUCCCMovto

- Procedure [CCC].[cccRecebimento].[usp_CCC_LogXML_Create_And_UpdateNSUCCC_INICIAL]

  - Insere XML-Request, XML-Response, nro de itens, indicação de erro e timestamp na tabela [CCC].[cccRecebimento].[LogXML]
  - Se não houve indicação de erro, insere novo registro na tabela [CCC].[cccRecebimento].[LogXML_Queue], somente com pKey do registro inserido na tabela [CCC].[cccRecebimento].[LogXML] e dataModificacao (sem preencher flag_read e UID_execution) 
  - Se tiver sido passado NSUCCC_Inicial, atualiza a tabela [CCC].[CCC].[Configuracao]

- Detalhe importante: na resposta do request vem vários contribuintes. Mas apenas um registro é marcado para cada request na LogXML e LogXML_Quque

### 2) Thread TaskGravaFilaEmMemoria:
 
- Baseado no conceito de Producer / Consumer (lista de CCCRecebimentoXMLEntity), 1 thread de Produce e 4 (configurável) threads de Consumers
 
- Producer

  - Chama Procedure [CCC].[cccRecebimento].[usp_CCC_Obter_LogXML_Queue]
    - Atualiza primeiro registro da tabela [CCC].[cccRecebimento].[LogXML_queue]: novo uid_execution, flag-read = 1 quando registro era ainda não lido (flag-read = 0) ou antigo
  - Seleciona registro da tabela [CCC].[cccRecebimento].[LogXML] baseado na pkey da tabela atualizada no item acima
  - Descomprime nó CCCZip e faz parse do XML
  - Para cada nó CCCCompl, cria elemento CCCRecebimentoXMLEntity e coloca em um fila "Queue". Nota: somente contribuintes de outras UFs são adicionados, mas a resposta pode conter contribuintes de SP
  - Marca "Queue" como não podendo mais receber registros.

- Consumer

  - Enquanto a "Queue" não estiver vazia (ou não for maracada como não podendo receber registros), para cada registro lido dela:
    - Verifica se tem todas as tags obrigatórias
    - Extrai todos os campos do XML em um objeto "CccEntity"
    - Chama procedure [CCC].[cccRecebimento].[usp_CCC_Contribuinte_Insert]
      - Se havia registro em [CCC].[CCC].[Contribuinte] com mesmo NSUCCC e com NSUCCCMovto mais antigo, atualiza registro nessa tabela
      - Caso contrário, adiciona outro registro em [CCC].[CCC].[Contribuinte]
      - Insere registro na tabela [CCC].[DW].[Queue]

- Aguarda todas as threads terminarem e grava resultado da execucao

  - Chama Procedure [CCC].[cccRecebimento].[usp_CCC_Grava_Resultado_Execucao]
    - Apaga registro da tabela [CCC].[cccRecebimento].[LogXML_Queue]
    - Se não houve erro de processamento, apaga registro da tabela [CCC].[cccRecebimento].[LogXML]
    - Caso contrário, se houve erro de processamento
      - Insere registros na tabela [CCC].[cccRecebimento].[RegistrosLoteErro] com os os documentos problemáticos, se tiverem sido passados
    - Atualiza coluna "Erro" da tabela [CCC].[cccRecebimento].[LogXML]
 
### 3) Thread TaskCFGMonitor: 
- Lê a flag FORCE_RESTART_FLAG com serviço "CCCRecebimento" = no tabela [CCC].[CCC].[Configuracao] , passando o nome do serviço
- Se for true, restarta o serviço.
