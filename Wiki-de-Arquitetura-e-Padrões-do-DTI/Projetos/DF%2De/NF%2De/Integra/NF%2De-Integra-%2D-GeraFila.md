## NFeGeraFila
 
### 1) Thread thrGeraFila<N> -> ControlExecute
 
_Síntese_:
* pega e apaga registros enfileirados em [NFe_Out].[NFeIntegra].[ProtocoloNFeQueue] (enfileirados durante autorização)
* armazena em tabelas/filas:

  * [DB_NFeIntegra].[NFeEnvio].[ProtocoloNFe]
  * [DB_NFeIntegra].[NFeEnvio].[ProtocoloNFe_Queue]
  * [DB_NFeIntegra].[NFeEventos].[Evento]
  * [DB_NFeIntegra].[NFeEventos].[Evento_XML]
  * [DB_NFeIntegra].[NFeEventos].[Evento_Envio_Queue]
 
_Loop_ -> chama proc [DB_NFeIntegra].[dbo].[usp_NFeIntegra_GeraFila]

 * Executa proc [NFe_Out].[NFeIntegra].[usp_NFeIntegra_Obter_Protocolos]
   * Pega e apaga os N primeiros registros de [NFe_Out].[NFeIntegra].[ProtocoloNFeQueue] com tpDocumento = 1,3,4 (autorizaçao, inutilização, cancelamento)
   * A partir desses registros e join com [NFe_Out].[NFeOut].[Protocolo]:
     * Insere registros em [DB_NFeIntegra].[NFeEnvio].[ProtocoloNFe]
     * Insere registros em [DB_NFeIntegra].[NFeEnvio].[ProtocoloNFe_Queue]
 * Executa proc [NFe_Out].[NFeEventos].[usp_NFeIntegra_Obter_Eventos]
   * Pega N primeiros registros de [NFe_Out].[NFeIntegra].[ProtocoloNFeQueue] com tpDocumento = 2 (eventos)
   * Armazena registro em tabela temporária fazendo join de várias tabelas:
     * [NFe_Out].[NFeEventos].[Evento]
     * [NFe_Out].[NFeEventos].[Evento_Constraint]
     * [DFe_XML].[NFe].[EventoXml]
     * [NFe_Out].[NFeOut].[Protocolo]
     * [NFe_Out].[NFeOut].[DadosTransmissao]
   * Armazena registro em [DB_NFeIntegra].[NFeEventos].[ProtocolosTemp_Eventos]
 * Executa proc [DB_NFeIntegra].[NFeEventos].[usp_NFeIntegra_Salvar_Evento]
   * Apaga registros de [DB_NFeIntegra].[NFeEventos].[ProtocolosTemp_Eventos] que existam em [DB_NFeIntegra].[NFeEventos].[Evento]
   * Insere em [DB_NFeIntegra].[NFeEventos].[Evento] dados a partir de [DB_NFeIntegra].[NFeEventos].[ProtocolosTemp_Eventos] que foram gerados nessa "session"
   * Insere em [DB_NFeIntegra].[NFeEventos].[Evento_XML] dados a partir de [DB_NFeIntegra].[NFeEventos].[ProtocolosTemp_Eventos] que foram gerados nessa "session"
   * Insere em [DB_NFeIntegra].[NFeEventos].[Evento_Envio_Queue] dados a partir de [DB_NFeIntegra].[NFeEventos].[ProtocolosTemp_Eventos] que foram gerados nessa "session"
 * Apaga arquivos de [DB_NFeIntegra].[NFeEventos].[ProtocolosTemp_Eventos] que foram criados por essa "session"

 Em caso de erro (exceção), armazena log em [DB_NFeIntegra].[NFeEnvio].[LogGeraFila]
 
### 2) Thread thrCfgMonitor -> ControlCFgMonitor
 
 Reinicia o serviço caso a flag em banco FORCE_RESTART_FLAG for setada para 'true'