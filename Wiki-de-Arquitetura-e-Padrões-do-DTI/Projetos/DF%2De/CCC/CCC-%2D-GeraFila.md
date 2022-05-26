Efeito Fluxo Alternativo SAVE:
- habilita a task TaskGravaFilaCADESPEmMemoria
- ao escrever na ContribuinteAtualizacoesQueue, escreve também na FILA_FLUXO_ALTERNATIVO (banco do CADESP)
- ligado em produção, desligado em homologação

Efeito Fluxo Alternativo READ = TRUE (homologação):
- lê/apaga da FILA_FLUXO_ALTERNATIVO 
- grava registros na ContribuinteAtualizacoesQueue ​
 
Efeito Fluxo Alternativo READ = FALSE (produção):
- pega registros em memória e grava na ContribuinteAtualizacoesQueue ​
- insere registros na fila alternativa FILA_FLUXO_ALTERNATIVO (depente de Fluxo Alternativo SAVE)
- apaga fila CADESP

**GeraFila**
 
### 1) Thread TaskGravaFilaCADESPEmMemoria (Somente Produção (homologação não é acessível), Fluxo Alternativo SAVE = true)

-- Lê registro da tabela [CADESP].[CCC].[FILA] fazendo join com outras tabelas do CADESP
   - Procedure [CADESP].[CCC].[usp_CCC_Obter_Contribuinte_Fila]
     - Seleciona primeiro registro da tabela [CCC].[Fila]
     - Se voltou "ID_ENDERECO" não nulo, significa que mudou de endereço (indAtuaEndereco)
     - Procedure [CADESP].[CCC].[usp_CCC_Obter_Indicador_Atualizacao], que retorna se houve atualização (indAtua) e a IE anterior (IEAtual) ???
       - Compara dados das tabelas "normais" com tabelas de log (TB_ESTABELECIMENTO_LOG, TB_EMPRESA_LOG)
       - indAtua:
         - 0 -> sem alteração
         - 1 -> se houve atualização de CNPJ, IE. IndST, Id situação, ocorrencia cadastral, início validade situação
         - 2 -> atualização de nome, RegApur, nome fantasia, CNAE, início atividade fiscal, fim atividade, ie-~unica
         - 9 -> exclusão (ocorrência cadastral = 93, 59, 63, 62, 55)
       - IE anterior -> retorna se for diferente da IE atual
     - Procedure [CADESP].[CCC].[usp_CCC_Obter_DadosEndereco], retorna ID_ENDERECO mais os dados do endereço (UF, logradouro, número, complemento, bairro, município, cep)
       - Pega dados dos endereços da TB_ENDERECO
       - Mas se IE começa com 8, indST=0 e UF != "SP", repassa endereço do procurador se existir (cnae em uma lista pré-determinada, elétrica e comunicação)
     - Se o ID_ENDERECO retornado for nulo, remove da [CADESP].[CCC].[FILA] e insere na [CADESP].[CCC].[FILA_LOG] com a frase "Não envia indST=0 e UFEnder!=SP e sem procurador ou CNAE não exceção"
     - Retorna dados a partir das tabelas:
       - [CADESP].[CCC].[FILA]:
         - ID_FILA,
         - ID_EMPRESA,
         - ID_ESTABELECIMENTO,
         - ID_PRODUTOR_RURAL
       - [CADESP].[dbo].[TB_ESTABELECIMENTO] :
         - CNPJ (NR_CNPJ)
         - IE (se não for IE-única , NR_IE)
         - dSit (DT_INICIO_VALIDADE_SITUACAO)
         - cSitCADESP (ID_SITUACAO_ESTABELECIMENTO + ID_OCORRENCIA_CADASTRAL)
         - indST (IN_SUBST_TRIBUTARIO)
         - xFant (NM_FANTASIA)
         - dIniAtiv (DT_INICIO_ATIVIDADE)
         - dBaixa (DT_FIM_ATIVIDADE)
         - indOperNaoContrib (IN_OP_NAO_CONTRIBUINTES)
       - [CADESP].[dbo].[TB_EMPRESA] :
         - IE (se for IE única, NR_IE_UNICA_ESTABELECIMENTOS),
         - xNome (NM_EMPRESARIAL)
         - indIEUnica (IN_IE_UNICA_ESTABELECIMENTOS)
         - IEUnica (NR_IE_UNICA_ESTABELECIMENTOS)
         - IEAtual (IE anterior retornado por usp_CCC_Obter_Indicador_Atualizacao)
       - [CADESP].[dbo].[TB_DOM_REGIME_APURACAO] :
         - xRegApur (DS_REGIME_APURACAO)
       - [CADESP].[dbo].[TB_PRODUTOR_RURAL]:
         - xNome (NM_EMPRESARIAL)
       - [CADESP].[CCC].[usp_CCC_Obter_DadosEndereco]:
         - end_xLgr
         - end_nro
         - end_xCpl
         - end_xBairro
         - end_cMun
         - end_xMun
         - end_CEP
         - ID_ENDERECO
         - UFEnder
       - [CADESP].[CCC].[UFC_OBTEM_SITUACAO_CONTRIBUINTE_CCC]: cSit
         - (se for IE única, pega da id do estabelecimento com essa IE). Aí pega o ID_SITUACAO_ESTABELECIMENTO dessa empresa
         - se ID_SITUACAO_ESTABELECIMENTO=1, cSit=1
         - se ID_SITUACAO_ESTABELECIMENTO=2
           - se ID_CORRENCIA_CADASTRAL em (41, 38, 39)  , cSit = 1
           - senão cSit = 0
         - se ID_SITUACAO_ESTABELECIMENTO=3, cSit = 0
         - se ID_SITUACAO_ESTABELECIMENTO=4, cSit = 0
         - se ID_SITUACAO_ESTABELECIMENTO=5, cSit = 0
         - senão cSit = -1
       - [CADESP].[dbo].[TB_DOM_CNAE_FISCAL]
         - CNAE (CD_CNAE_FISCAL)
       - cUF=35, indAtuaEndereco
- se o CNPJ da empresa for inválido, despreza o contribuinte e acrescenta um log em [CADESP].[CCC].[FILA_LOG]

- se o CNPJ da empresa for válido, coloca registro CCCGeraFilaEntity em fila em memória (_pumperQueue)

 
### 2) Thread TaskGeraFilaCccApagaFilaCADESP
 
_Homologação_ (Fluxo Alternativo READ = true)

- Procedure  Procedure [CADESP].[CCC].[usp_CCC_Obter_Contribuinte_Fila_Fluxo_Alternativo]

  - Le um registro da tabela [CADESP].[CCC].[FILA_FLUXO_ALTERNATIVO]
- Procedure [CCC].[cccEnvio].[usp_CCC_Contribuinte_Atualizacoes_Queue_Insert]

  - Verifica se já existe registro equivalente na tabela [CCC].[cccEnvio].[ContribuinteAtualizacoesQueue]
  - Verifica se já existe registro equivalente na tabela [CCC].[cccEnvio].[Contribuinte], ignorando cSit, indST, xRegApur, end_xMun, IndOperNaoContrib
  - Se não existir duplicação:
    - Insere registro na tabela [CCC].[cccEnvio].[ContribuinteAtualizacoesQueue]
    - Se houver outro registro na tabela de contribuinte com mesmo CNPJ e outra IE, insere mais um registro na tabela [CCC].[cccEnvio].[ContribuinteAtualizacoesQueue] com cSit=cSitCaDESP=0 (desabilitando o registro):
      - Se for exclusão (indAtua == 9), todos os registros
      - Se não for exclusão (indAtua != 9), somente registros ativos (cSitIE == 1).
- Procedure [CADESP].[CCC].[usp_CCC_Apagar_Fila_Fluxo_Alternativo]

  - Apaga registro da tabela [CADESP].[CCC].[FILA_FLUXO_ALTERNATIVO]
 

_Produção_ ((Fluxo Alternativo READ = false)

- Pega registro CCCGeraFilaEntity da fila em memória (_pumperQueue)

- Procedure [CCC].[cccEnvio].[usp_CCC_Contribuinte_Atualizacoes_Queue_Insert]
  - Verifica se já existe registro equivalente na tabela [CCC].[cccEnvio].[ContribuinteAtualizacoesQueue]
  - Verifica se já existe registro equivalente na tabela [CCC].[cccEnvio].[Contribuinte], ignorando cSit, indST, xRegApur, end_xMun, IndOperNaoContrib
  - Se não existir duplicação:
    - Insere registro na tabela [CCC].[cccEnvio].[ContribuinteAtualizacoesQueue]
    - Se houver outro registro na tabela de contribuinte com mesmo CNPJ e outra IE, insere mais um registro na tabela [CCC].[cccEnvio].[ContribuinteAtualizacoesQueue] com cSit=cSitCaDESP=0 (desabilitando o registro):
      - Se for exclusão (indAtua == 9), todos os registros
      - Se não for exclusão (indAtua != 9), somente registros ativos (cSitIE == 1).

- Procedure [CADESP].[CCC].[usp_CCC_Fila_Fluxo_Alternativo_Insert] (Fluxo Alternativo SAVE = true)
  - Insere registro na tabela [CADESP].[CCC].[FILA_FLUXO_ALTERNATIVO]

- Procedure [CADESP].[CCC].[usp_CCC_Apagar_Fila]
  - Apaga registro da tabela [CADESP].[CCC].[FILA]

**=== DEPRECATED -> não está mais funcionando - lógica movida para o CADESP ===**

### 3) Thread TaskCargaContribuintesHabilitadosCADESP 
- Lê todos os Contribuintes Habilitados ativos de SP através da proc [NFe_Out].[NFeOut].[ContribuintesHabilitadosList]
- Trunca tabela [CADESP].[dbo].[SSIS_TB_ContribuintesHabilitadosList]
- Escreve todos os contribuintes lidos em [CADESP].[dbo].[SSIS_TB_ContribuintesHabilitadosList]
- Detalhe: thread roda apenas uma vez ao dia; quando roda, atualiza a tabela [CCC].[CCC].[Configuracao] com chave "ULTIMA_CARGA_CADESP_NFEOUT" do serviço "GeraFila"
 
### 4) Thread TaskCfgMonitor
- Lê a flag FORCE_RESTART_FLAG com serviço "CCCGeraFila" = no tabela [CCC].[CCC].[Configuracao] , passando o nome do serviço
- Se for true, restarta o serviço.