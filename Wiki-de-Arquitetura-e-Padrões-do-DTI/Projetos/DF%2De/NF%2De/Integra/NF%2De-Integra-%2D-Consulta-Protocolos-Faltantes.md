## NFeProtocoloFaltante
 
PROTOCOLO_LIMITE = maior protocolo enviado para o AN menos um RANGE
PROTOCOLO_INICIAL = maior protocolo faltante retornado pelo AN, é o valor utilizado como base no request para o AN. Fica armazenado em [DB_NFeIntegra].[dbo].[ULTIMO_PROTOCOLO_FALTANTE].
 
Com base no PROTOCOLO_INICIAL, o AN responde algo como "a partir desse PROTOCOLO_INICIAL, eu não recebi os protocolos W, X e Y. Faça um novo request com base no protocolo Z", com Z >= Y >= X >= W >= PROTOCOLO_INICIAL. Depois de processados, Z torna-se o novo PROTOCOLO_INICIAL.
 
O request para o AN é enviado somente se PROTOCOLO_INICIAL < PROTOCOLO_LIMITE.
 
 
## 1) Thread thrExecuteConsultarProtocolosFaltantesAN - ControlExecuteConsultarProtocolosFaltantesAN()
Loop:
 * Verifica nro linhas em [DB_NFeIntegra].[NFeEnvio].[ProtocoloNFe_Queue]; se for acima de um determinado limite, deixa para depois
 * Da lista de UFs atendidas (só SP, mas código está preparado para virtual), para cada UF
   * Chama proc [DB_NFeINtegra].[dbo].[usp_NFeIntegra_Obter_Protocolo_Faltantes] para verificar qual o maior protocolo na tabela [DB_NFeINtegra].[NFeEnvio].[ProtocoloRecebido], para subtrair e calcular um protocol-limite; verifica também o [DB_NFeINtegra].[dbo].[ULTIMO_PROTOCOLO_FALTANTE]
   * Gera XML de Consulta de Protocolos Faltantes com o valor do último-protocolo-faltante
   * Envia XML para o AN e verifica cStat de retorno
     * Se 115/116 (DFes localizados)
       * Cria um XML auxiliar contendo todos os valores discretos de protocolos faltantes (o AN pode mandar "ranges"). Tamanho do XML pode ter limite.
       * Salva XML chamando proc [DB_NFeIntegra].[dbo].[usp_NFeIntegra_ProtocoloFaltantes_Create], que:
         * Primeiro remove protocolos que tenham sido enviados, presentes em [DB_NFeIntegra].[NFeEnvio].[ProtocoloRecebido], [DB_NFeIntegra].[NFeEnvio].[ProtocoloNFe], [DB_NFeIntegra].[NFeEventos].[EVENTO], [DB_NFeIntegra].[NFeEventos].[EVENTO_RECEBIDO], [DB_NFeIntegra].[dbo].[PROTOCOLOS_FALTANTES]
         * Salva registros em [DB_NFeIntegra].[dbo].[PROTOCOLOS_FALTANTES] e [DB_NFeIntegra].[dbo].[PROTOCOLOS_FALTANTES_HISTORICO]
         * Atualiza ultimo-protocolo-faltante em [DB_NFeIntegra].[dbo].[ULTIMO_PROTOCOLO_FALTANTE]
     * Se 114 (nenhum protocolo faltante), chama [DB_NFeIntegra].[dbo].[usp_NFeIntegra_ProtocoloFaltantes_UltimoProtocolo] para atualizar último-protocolo-faltante em [DB_NFeIntegra].[dbo].[ULTIMO_PROTOCOLO_FALTANTE]
     * Se outros erros, lança exceção
   * Salva log chamando proc [DB_NFeIntegra].[dbo].[usp_NFeIntegra_LOG_Protocolos_Faltantes_Create], que só armazena 1 registro em [DB_NFeIntegra].[dbo].[LOG_PROTOCOLOS_FALTANTES]
 
## 2) Thread thrExecuteReenfileirarProtocolosFaltantesRecebidosDoAN(N) - ControlExecuteReenfileirarProtocolosFaltantesRecebidosDoAN()
Loop:

 * Só existem se FaltantesConfigurationEntity.AUX_THREAD_ENABLED
 * Da lista de UFs atendidas (só SP, mas código está preparado para virtual), para cada UF pega o protocolo-inicial e extrai o IdReceptor, formando uma lista de id-receptores
 * Para cada idReceptor, chama proc [DB_NFeIntegra].[dbo].[usp_NFeIntegra_GeraFilaFaltantes], que chama [NFe_Out].[dbo].[usp_NFeIntegra_Obter_Protocolos_Faltantes], que:
   * Move os N primeiros registros de [DB_NFeIntegra].[dbo].[PROTOCOLOS_FALTANTES] para uma tabela temporária, de acordo com idReceptor
   * Apaga da tabela temporária registros que estão sendo enviados - estão em [DB_NFeIntegra].[NFeEnvio].[ProtocoloNFe]
   * Apaga da tabela temporária registros que foram recebidos - estão em [DB_NFeIntegra].[NFeEnvio].[ProtocoloRecebido] e [DB_NFeIntegra].[dbo].[PROTOCOLO_RECEBIDO]
   * Faz join da tabela temporária com [NFe_Out].[NFeOut].[Protocolo] e [NFe_Out].[NFeOut].[ProtocoloLog] para pegar dados dos protocolos e inserir em [NFe_Out].[NFeIntegra].[ProtocoloNFeQueue]
 * Exceções chamam proc [DB_NFeIntegra].[dbo].[usp_NFeIntegra_LOG_XML_Create], que insere registro em [DB_NFeIntegra].[dbo].[LOG_XML]

## 3) Thread thrCfgMonitor - ControlCfgMonitor()

Reinicia o serviço caso a flag em banco FORCE_RESTART_FLAG for setada para 'true'