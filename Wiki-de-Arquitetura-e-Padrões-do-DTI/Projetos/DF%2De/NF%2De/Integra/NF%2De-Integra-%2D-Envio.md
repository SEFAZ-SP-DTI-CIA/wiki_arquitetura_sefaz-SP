## NFeEnvio 

Utiliza serviço _NFeRecepcaoRFB_, que retorna NSU e NSUAut:

 * NSU (número único no AN todo), armazenado em
   * [DB_NFeIntegra].[NFeEnvio].[ProtocoloRecebido] , coluna NSU (índice)
   * [NFe_Out].[NFeIntegra].[IntegraNSU]                  , coluna NSU (chave primária)
 * NSUAut  (número único para estado autorizador, quando tpAutorização = 1) armazenado em:
   * [NFe_Out].[NFeIntegra].[IntegraNSU] , coluna nsuAutorizacaoBusca. Nota: o NFeEnvio sinaliza fkIntegraTipo=1 ao armazenar nessa coluna
  
### 1) Task TaskEnfileirarLotesEmMemoria

Loop:

 * Chama proc [DB_NFeIntegra].[NFeEnvio].[usp_NFeIntegra_Obter_Protocolo_Queue]
   * Atualiza [DB_NFeIntegra].[NFeEnvio].[ProtocoloNFe_Queue], marcando N itens a serem enviados (flagRead=1, uid=<GUID>, dataModificado=GETDATE())
   * Retorna N notas, com dados de join das seguintes tabelas
     * [DB_NFeIntegra].[NFeEnvio].[ProtocoloNFe]
     * [NFe_Out].[NFeOut].[NFe_Constraint]
     * [NFe_Out].[NFeOut].[Protocolo]
     * [NFe_Out].[NFeOut].[NFe] ou [InutNFe]
     * [DFe_XML].[NFe].[NFeXml] ou [InutNFeXml]
     * [NFe_Out].[NFeOut].[DadosTransmissao]
   * Gera XML para Cancelamento (legado), Emissão ou Inutilização
   * Gera involucro para o lote e adiciona em fila (objeto EnvioController2::_filaEnvio)

### 2) Task ​TaskEnviarRequestEnfileirado<N>

Loop:

 * Cada Thread desenfileira um item de '_filaEnvio' e envia para AN
 * Para cada item salva estatísticas pela proc [DB_NFeIntegra].[NFeEnvio].[usp_NFeIntegra_Save_Tempo_Envio_Lote] na tabela [DB_NFeIntegra].[NFeEnvio].[EstatisticaEnvio]
 * Se "log_ligado", para cada item salva request/retorno pela proc [DB_NFeINtegra].[NFeEnvio].[usp_NFeIntgra_RetornoAN_Insert] na tabela [DB_NFeIntegra].[NFeEnvio].[RetornoAN]
 * Extrai nós "retProcNFe" do retorno do AN. Se houver nós, extrai campos daquele retorno (cStat, xMotivo, NSU, NSUAut) e:
   * Para cStat == 113 (DF-e recebido no AN) ou cStat reportando erro validação G reportando duplicidade AN chama proc [DB_NFeIntegra].[NFeEnvio].[usp_NFeIntegra_Criar_ProtocoloRecebido], que:
     * Armazena em [DB_NFeIntegra].[NFeEnvio].[ProtocoloRecebido]
     * Armazena registros em [NFe_Out].[NFeIntegra].[IntegraNSU], mas somente os que tem NSU diferente
       * seta fkIntegraTipo=1
     * Apaga registros de [DB_NFeIntegra].[NFeEnvio].[ProtocoloNFe]
     * Apaga registros de [DB_NFeIntegra].[NFeEnvio].[ProtocoloNFe_Queue]
   * Para cStat reportando erro nas validações A, B, C, D, chama proc [DB_NFeIntegra].[NFeEnvio].[usp_NFeIntegra_Save_LogXml_Protocolo_ReEnqueue], que:
     * Se pedir retentativa (validação C e D) , incrementa nroTentativas de [DB_NFeIntegra].[NFeEnvio].[ProtocoloNFe], mas apaga de [DB_NFeIntegra].[NFeEnvio].[ProtocoloNFe_Queue] se tiver atingido número máximo de tentativas
     * Reseta flagReag de [DB_NFeIntegra].[NFeEnvio].[ProtocoloNFe_Queue] (para reenfileirar imediatamente)
     * Insere registro em [DB_NFeIntegra].[NFeEnvio].[LogXML]
   * Para cStat reportando erro nas validações E, F, G (sem ser duplicidade AN), chama proc [DB_NFeIntegra].[NFeEnvio].[usp_NFeIntegra_Criar_ProtocoloRejeitado]
     * Insere registro em [DB_NFeIntegra].[NFeEnvio].[ProtocoloRejeitado]
     * Insere registro em [DB_NFeIntegra].[NFeEnvio].[LOGXML]
     * Apaga registros de [DB_NFeIntegra].[NFeEnvio].[ProtocoloNFe]
     * Apaga registros de [DB_NFeIntegra].[NFeEnvio].[ProtocoloNFe_Queue]
   * Para cStat 217, chama proc [DB_NFeIntegra].[NFeEnvio].[usp_NFeIntegra_Obter_Protocolos_Emissao_Reenvio], que:
     * Procura emissão rejeitada em [DB_NFeIntegra].[dbo].[PROTOCOLO_REJEITADO_INFO]
     * Procura emissão recebida em [DB_NFeIntegra].[dbo].[PROTOCOLO_RECEBIDO] e em [DB_NFeIntegra].[NFeEnvio].[ProtocoloRecebido]
     * Se emissão foi rejeitada e não foi recebida, chama proc [DB_NFeIntegra].[NFeEnvio].[usp_NFeIntegra_Criar_ProtocoloRejeitado] (acima)
     * Senão
       * Apaga registros de [DB_NFeIntegra].[NFeEnvio].[ProtocoloNFe]
       * Apaga registros de [DB_NFeIntegra].[NFeEnvio].[ProtocoloNFe_Queue]
       * Insere registro em [NFe_Out].[NFeIntegra].[ProtocoloNFeQueue] a partir de [NFe_Out].NFeOut.[NFe] e ([NFe_Out].NFeOut.[Protocolo] ou [NFe_Out].NFeOut.[ProtocoloLog])
       * Insere registro em [NFe_Out].[NFeIntegra].[ProtocoloNFeQueue], com tpDocumento=4 (cancelamento)
       * Insere registro em [DB_NFeIntegra].[NFeEnvio].[LogXML]
   * Para protocolos que não foram retornados pelo AN, chama proc [DB_NFeIntegra].[NFeEnvio].[usp_NFeIntegra_Save_LogXml_Protocolo_ReEnqueue] descrita acima, pedindo retentativa se cStat==113, não pedindo retentativa caso 999.
   * Caso haja exceção, chama proc [DB_NFeIntegra].[NFeEnvio].[usp_NFeIntegra_Save_Log_XML], que grava erro em [DB_NFeIntegra].[NFeEnvio].[LogXML]

### 3) Thread ControlCFgMonitor

Reinicia o serviço caso a flag em banco FORCE_RESTART_FLAG for setada para 'true'