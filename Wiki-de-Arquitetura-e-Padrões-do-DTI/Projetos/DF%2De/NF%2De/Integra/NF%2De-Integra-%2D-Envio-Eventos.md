## NFeEnvioEventos
 
### 1) Task thrEnvioPumperEventos - ControlExecutePumperEventos()
Loop:

 * Pega N eventos pela proc [DB_NFeIntegra].[NFeEventos].[usp_NFeIntegra_Obter_Eventos_Queue], que lê as tabelas:
   * [DB_NFeIntegra].[NFeEventos].[EVENTO_ENVIO_QUEUE]
   * [DB_NFeIntegra].[NFeEventos].[EVENTO]
   * [DB_NFeIntegra].[NFeEventos].[EventosHabilitados] (restringe os tipos de eventos a serem enviados)
   * [DB_NFeIntegra].[NFeEventos].[EVENTO_XML]
 * Pega os eventos cuja UF sejam os mesmos (não faz sentido nesse caso, é tudo 35, faz sentido para SEFAZ virtual) e gera o lote de eventos
 * Constroi lote XML para ser enviado ao AN
 * Procura a fila com menor quantidade e enfileira nela
 * Exceções chama proc [DB_NFeIntegra].[NFeEventos].[usp_NFeIntegra_LOG_XML_Create], que armazena em [DB_NFeIntegra].[NFeEventos].[LOG_XML]
 
### 2) Task "N" - ControlExecuteConsumerEventos()
Loop:

 * Cada consumer pega dados de uma fila.
 * Envia request para o AN
 * Pega resposta e chama [DB_NFeIntegra].[NFeEventos].[usp_NFeIntegra_EventosRetornoQueue_Create], que:
   * Armazena estatística em [DB_NFeIntegra].[dbo].[ESTATISTICA_ENVIO]
   * Faz um parse nos protocolos retornados, armazenando código de eventos em tabela temporária
   * Apaga registros de [DB_NFeIntegra].[NFeEventos].[EVENTO_ENVIO_QUEUE] cujos eventos estão na tabela temporária acima.
   * Insere registros em [DB_NFeIntegra].[NFeEventos].[EVENTO_RETORNO_QUEUE]
 * Exceções chama proc [DB_NFeIntegra].[NFeEventos].[usp_NFeIntegra_LOG_XML_Create], que armazena em [DB_NFeIntegra].[NFeEventos].[LOG_XML]
 
### 3) Task thrEnvioRetornoPumper - ControlExecuteRetornoPumper()
Loop:

 * Só é habilitada se EnvioConfigurationEntity.AUX_THREAD_ENABLED.Value == true
 * Chama proc [DB_NFeIntegra].[NFeEventos].[usp_NFeIntegra_Obter_Retorno_Queue] que lê os N primeiros elementos de [DB_NFeIntegra].[NFeEventos].[EVENTO_RETORNO_QUEUE]
 * Coloca cada elemento em uma fila EnvioConfigurationEntity.DFE_PUMPER_RETORNO_QUEUE
 
 
### 4) Task thrEnvioRetornoConsumer "N"- ControlExecuteRetornoConsumer()
Loop:

 * Só é habilitada se EnvioConfigurationEntity.AUX_THREAD_ENABLED.Value == true
 * Pega elemento da fila EnvioConfigurationEntity.DFE_PUMPER_RETORNO_QUEUE
 * Pega lista de nós "retProcNFe", extrai os protocolos retornados e demais parâmetros.
   * Se cStat = 113 ou erro de validação G indicando duplicidade no AN (possivel erro de timeout na tentativa anterior), chama proc [DB_NFeIntegra].[NFeEventos].[usp_NFeIntegra_Criar_EventoRecebido]
     * Insere protocolos em [DB_NFeIntegra.[NFeEventos].[EVENTO_RECEBIDO]
     * Insere registros na tabela [NFe_Out].[NFeIntegra].[IntegraNSU], filtrando protocolos cujo NSUs já estavam nela (seta fkIntegraTipo=1)
     * Apaga registros das filas [Eventos].[EVENTO_XML] e [Eventos].[EVENTO]
   * Se cStat indica erro de validação A, B, C, D , chama proc [DB_NFeIntegra].[NFeEventos].[usp_NFeIntegra_Criar_LogXml_Eventos_ReEnqueue], que:
     * Se pediu retentativa (validação C e D), incrementa nro retentativas em [DB_NFeIntegra].[NFeEventos].[EVENTO]
     * Insere registro em [DB_NFeIntegra].[NFeEventos].[EVENTO_ENVIO_QUEUE], se não atingiu número máximo de retentativas
     * Insere log em [DB_NFeIntegra].[NFeEventos].[LOG_XML]
   * Se cStat indica erro de validação E, F, G (exceto se houve indicação de duplicidade do AN), chama proc [DB_NFeIntegra].[NFeEventos].[usp_NFeIntegra_Criar_EventoRejeitado]
     * Insere registro em [DB_NFeIntegra].[NFeEventos].[EVENTO_REJEITADO]
     * Insere registro em [DB_NFeIntegra].[NFeEventos].[EVENTO_REJEITADO_XML],
     * Insere registro em [DB_NFeIntegra].[NFeEventos].[LOG_XML]
     * Apaga registro de [DB_NFeIntegra].[NFeEventos].[EVENTO_XML]
     * Apaga registro de [DB_NFeIntegra].[NFeEventos].[EVENTO]
   * Se cStat = 217 ("DF-e não consta na base de dados do Ambiente Nacional"), chama proc [NFe_Out].[NFeEventos].[usp_NFeIntegra_Obter_Eventos_Emissao_Reenvio], que:
     * Verifica se a autorização não foi rejeitada, procura em [DB_NFeIntegra].[NFeEnvio].[ProtocoloRejeitado], [DB_NFeIntegra].[dbo].[PROTOCOLO_REJEITADO_INFO], [DB_NFeIntegra].[dbo].[PROTOCOLO_REJEITADO] (essas 2 últimas são tabelas 'antigas')
     * Verifica se a autorização foi recebida, procura em [DB_NFeIntegra].[NFeEnvio].[ProtocoloRecebido]e [DB_NFeIntegra].[dbo].[PROTOCOLO_RECEBIDO] (essa última é tabela 'antiga')
     * Se a emissão foi rejeitada e não recebia, chama proc [DB_NFeIntegra].[NFeEventos].[usp_NFeIntegra_Criar_EventoRejeitado]
       * Insere em [DB_NFeIntegra].[NFeEventos].[EVENTO_REJEITADO]
       * Insere em [DB_NFeIntegra].[NFeEventos].[EVENTO_REJEITADO_XML]
       * Insere em [DB_NFeIntegra].[NFeEventos].[LOG_XML]
       * Apaga registro de [DB_NFeIntegra].[NFeEventos].[EVENTO_XML]
       * Apaga registro de [DB_NFeIntegra].[NFeEventos].[EVENTO]
     * Se a emissão foi rejeitada e o erro foi especificamente de read-only, chama proc [DB_NFeIntegra].[NFeEventos].[usp_NFeIntegra_Criar_EventoRejeitado] (acima)
     * Senão
       * Verifica se a respectiva autorização está sendo enviada ([DB_NFeIntegra].[NFeEnvio].[ProtocoloNFe] join [NFe_Out].[NFeOut].[NFe]); se sim e já tiver 3 retentativas, seta nro de retentativas em 3 para o evento em [DB_NFeIntegra].[NFeEventos].[EVENTO]
       * Se a respectiva autorização não está sendo reenviada e presa:
         * Apaga registro de evento de [DB_NFeIntegra].[NFeEventos].[EVENTO_XML] e [DB_NFeIntegra].[NFeEventos].[EVENTO]
         * Reenfileira evento em [NFe_Out].[NFeIntegra].[ProtocoloNFeQueue]
         * Se a emissão não estiver sendo reenviada, reenfileira a partir de [NFe_Out].[NFeOut].[NFe] e ([NFe_Out].[NFeOut].[Protocolo] ou [NFe_Out].[NFeOut].[ProtocoloLog])
 * Chama proc [DB_NFeIntegra].[NFeEventos].[usp_NFeIntegra_Excluir_RetornoQueue]
Apaga registro da tabela [DB_NFeIntegra].[NFeEventos].[EVENTO_RETORNO_QUEUE]
 
### 5) Task thrCfgMonitor - ControlCfgMonitor()
Reinicia o serviço caso a flag em banco FORCE_RESTART_FLAG for setada para 'true'