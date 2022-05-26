**Envio**
 
### 1) Thread TaskSendRequestAndProcess
 
- Procedure [CCC].[cccEnvio].[usp_CCC_GetFilaEnvio] , retorna registro
  - Atualiza campos da tabela [CCC].[cccEnvio].[ContribuinteAtualizacoesQueue]: UID_execution para um novo GUID, flagRead =  1, dataModificacao = "now, no caso de registros marcados como não lido ou registros antigos
  - Retorna registro da tabela [CCC].[cccEnvio].[ContribuinteAtualizacoesQueue] com GUID setado acima

- Realiza algumas transformações no registro lido, ou setando outros não mapeados para valores hardcoded:
  - cUF = 35
  - CSitCnpj = 1
  - TpIE = 2 (indST = true OU IndOperNaoContrib = true), TpIe = 1 (caso contrário)
  - IndIEDescOpc = 0
  - Porte = 0
  - RegTrib =
    - 9 se xRegApor começar com NORMAL
    - 2 se xRegApur começar com MEI
    - 1 se xRegApur começar com SIMPLES
    - 0 caso contrário
  - Se Cnae == 8888988 , troca por 4713001
  - CSitIE = cSit
  - DBaixa = nulo (CSitIE == 1), DBaixa = DSit (caso contrário) 

- Transforma o registro lido em XML e envia para CCC do Rio Grande do Sul
- Valida a resposta, extraindo NSUCCC e NSUCCCMovto
- Se veio resposta código 9320 / 9322 (Contribuinte (CNPJ/IE ja cadastrado para a UF e dados nao foram alterados / Contribuinte (CNPJ / IE) ja excluido para a UF):

  - Procedure [CCC].[cccEnvio].[usp_CCC_Grava_Envio_Contribuinte_Repetido]
    - Apaga registro de contribuinte da tabela [CCC].[cccEnvio].[ContribuinteAtualizacoesQueue]
    - Atualiza cSit=9 se indAtua=9
    - Atualiza dAtu = "now"
    - Insere registro de transação na tabela [CCC].[cccEnvio].[LogXML]
    - Se registro de contribuinte existe na tabela [CCC].[CCC].[Contribuinte]
      - Atualiza registro na [CCC].[CCC].[Contribuinte] a partir do registro da [CCC].[cccEnvio].[ContribuinteAtualizacoesQueue]
      - Atualiza registro na [CCC].[CCC].[ContribuinteHistorico] a partir do registro anterior da tabela [CCC].[CCC].[Contribuinte] se NSUCCCMovto for diferente
    - Senão
      - Insere registro na [CCC].[CCC].[Contribuinte] a partir do registro da [CCC].[cccEnvio].[ContribuinteAtualizacoesQueue]
    - Insere registro na tabela  [CCC].[DW].[Queue] . Nota: não insere se não houve atualização do NSUCCCMovto (= NSUAtu)

- Se veio resposta 9210 (alteração aceita):
    - Procedure [CCC].[cccEnvio].[usp_CCC_Grava_Envio_Com_Sucesso]
      - Pega registro atualizado da tabela [CCC].[cccEnvio].[ContribuinteAtualizacoesQueue] e atualiza cSit=9 se indAtua=9 em memória, apagando o registro da tabela
      - Procura registro na tabela [CCC].[CCC].[Contribuinte] pelo NSU (pegando NSUAtu menor do que o enviado)
    - Se não achou registro:
      - Insere registro atualizado na tabela de [CCC].[CCC].[Contribuinte]
      - Insere registro na tabela [CCC].[DW].[Queue]
    - Se achou registro:
      - Atualiza registro na tabela [CCC].[CCC].[Contribuinte] com dado atualizado
      - Se NSUCCCMovto for diferente
        - Atualiza registro na [CCC].[CCC].[ContribuinteHistorico] a partir do registro anterior da tabela [CCC].[CCC].[Contribuinte]
        - Insere registro na tabela [CCC].[DW].[Queue]

- Se veio outro valor - TBD
 
 
### 2) Thread TaskCfgMonitor
 
- Lê a flag FORCE_RESTART_FLAG com serviço "CCCGeraFila" = no tabela [CCC].[CCC].[Configuracao] , passando o nome do serviço
- Se for true, restarta o serviço.