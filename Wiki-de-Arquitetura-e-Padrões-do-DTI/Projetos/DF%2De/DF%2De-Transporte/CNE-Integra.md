## Tipos manipulados:

Tipo de DF-e:

  * 55    NFe
  * 57    CTe
  * 99    Utilizado pelo Fisco
  * 5701 CTe Rodoviário
  * 5702 CTe Aéreo
  * 5703 CTe Aquaviário
  * 5704 CTe Ferroviário
  * 5705 CTe Dutoviário
  * 5706 CTe Multimodal
  * 5799 CTe não controlado por modal
 
Tipos de Evento:

  * 1   Credenciamento
  * 2   Suspensão
  * 3   Reativação
  * 4   Descredenciamento
  * 5   Alteração

## CNE GeraFila
### Thread ControlExecute

Somente chama 2 procs em loop:

  * NFe_Out.CNeIntegra.usp_NFeOut_Create_GeraFila_Queue
  * CTe_Out.CNeIntegra.usp_CTeOut_Create_GeraFila_Queue

Essas procs:

  * Deletam registros de [NFe_Out/CTe_Out].[NFeIntegra].[FilaCNE]
  * Inserem registros em [DB_CNeIntegra].[dbo].[CONTRIBUINTE_ATUALIZACOES_QUEUE]

## CNE Envio
### Thread ExecutePumper
Lê registros de [DB_CNeIntegra].[dbo].[CONTRIBUINTE_ATUALIZACOES_QUEUE] e enfileira em memória CNeEnvioConfigurationEntity.PUMPER_QUEUE
 
### Thread ControlExecuteRetornoPumper
Lê registros de CNeEnvioConfigurationEntity.PUMPER_QUEUE e envia para o AN 
Com a resposta ok, insere em [DB_CNeIntegra].[dbo].[CONTRIBUINTE_ATUALIZACOES] e apaga de [DB_CNeIntegra].[dbo].[CONTRIBUINTE_ATUALIZACOES_QUEUE]

## CNE Recebimento
### Thread ControlExecute 

Envia request para AN baseado em NSU
Valida resposta e chama 2 procs:

  * [DB_CNeIntegra].[dbo].[usp_DFe_ContribuintesHabilitadosInsert]: chama várias procs da NFe_Out ou do CTe_Out, dependendo do @tpDFe
  * [DB_CNeIntegra].[dbo].[usp_CNeIntegra_ContribuinteAtualizacoesRecebimento_Insert]: Insere ou atualiza [DB_CNeIntegra].[dbo].[CONTRIBUINTE_ATUALIZACOES_RECEBIMENTO]

## CNE RecebimentoCNPJ
Serviço que nada recebe desde 2014, dados de XML muito parecidos com o CNE Recebimento