## NFeNSUFaltantes
 
Utiliza serviço da receita NFeConsNSUFal, passando indNSU=1 (NSU de Busca)
 
### Principais Tabelas:
* [NFe_Out].[NFeIntegra].[IntegraNSU] - tabela onde são recebidos documentos pelo Integra Recebimento (SCAN) ou pelo Integra Envio. É analisada para ver se tem buracos.
* [NFe_Out_UFs].[dbo].[IntegraNSU]     - tabela onde são recebidos documentos pelo Integra Recebimento. É analisada para ver se tem buracos.
* [NFe_Out_UFs].[dbo].[LOG_XML]              - log da fila do Recebimento e NSUFaltantes. Deve-se analisar erros na coluna LOXM_TX_ERRO.
* [NFe_Out_UFs].[dbo].[LOG_XML_QUEUE]  - fila de registros sendo processados pelo Recebimento e NSUFaltantes. Não pode ser grande.
* [NFe_Out_UFs].[NSUFALT].[NSU_FALTANTES_QUEUE] - fila do NFeNSUFaltantes, contendo NSUs enviados para o AN e sendo processados.
* [NFe_Out_UFs].[NSUFALT].[NSU_FALTANTES_VERIFICACAO]             - log da verificação de NSUs recebidos
* [NFe_Out_UFs].[NSUFALT].[NSU_FALTANTES_VERIFICACAO_QUEUE] - fila de verificação de NSUs recebidos.
* [NFe_Out_UFs].[NSUFALT].[LOG_XML] - log de erros do NSUFaltantes
* [NFe_Out_UFs].[NSUFALT].[ULTIMO_NSU_FALTANTE] - nsu base de busca

_Importante_: As tabelas [LOG_XML] e [LOG_XML_QUEUE] são usadas também pelo pelo processo de Recebimento, que é o processo responsável por apagar os registros da [LOG_XML_QUEUE]

 
## 1) Thread thrEnfileirarNSUsNaoRecebidosEmBanco - ControlEnfileirarNSUsNaoRecebidosEmBanco() 

Síntese:

* Pega nsu faltantes (buracos) de NSUs em [NFe_Out_UFs].[dbo].[IntegraNSU] e em [NFe_Out].[NFeIntegra].[IntegraNSU] (fkIntegraTipo=3,4,5)
* Coloca esses faltantes em [NFe_Out_UFs].[NSUFALT].[NSU_FALTANTES_QUEUE]
* Conceitos:
  * NSUFaltanteConfigurationEntity.NSU_INICIAL = último NSU varrido nos buracos.​​​
  * NSUFaltanteConfigurationEntity.NSU_LIMITE = (maior NSU recebido) - (range)
  * Os buracos são pesquisados desde o NSU_INICIAL até o menor entre (maior NSU recebido) e (NSU_INICIAL + quantidade de linhas)

​​Loop:
- Se NSU_LIMITE = 0 ou NSU_LIMITE > NSU_INICIAL (??)

  * Proc [NFe_Out_UFs].[NSUFALT].[usp_NFeOutUFs_Obter_NSU_Faltantes_Queue]
    * Acha o MAX(nsuBusca) da tabela [NFe_Out_UFs].[dbo].[IntegraNSU], que é o máximo NSU recebido.
    * Varre 1 a 1 os nsus entre NSU_INICIAL e o menor entre (máximo-nsu-recebido) e (NSU_INICIAL+quantidade-de-linhas) analisando-os em duas tabelas:
      * "nsuBusca"                  em [NFe_Out_UFs].[dbo].[IntegraNSU]
      * "nsuAutorizacaoBusca" em [NFe_Out].[NFeIntegra].[IntegraNSU] (mas nesse caso somente fkIntegraTipo=3,4,5 , relativos a recebimento em contingência)
    * Se não existir em nenhuma das tabelas, insere em uma tabela temporária @TAB_NSU_FALTANTE
    * Insere registros de @TAB_NSU_FALTANTE em [NFe_Out_UFs].[NSUFALT].[NSU_FALTANTES_QUEUE]
    * Atualiza tabela [NFe_Out_UFs].[NSUFALT].[ULTIMO_NSU_FALTANTE] com o NSU inicial
    * Retorna:
      * Maior nsu varrido nas tabelas como ULTIMO_NSU_FALTANTE
      * "Max_NSU - range" como NSU_LIMITE
- Se NSU_LIMITE < NSU_INICIAL (??)Se NSU_LIMITE < NSU_INICIAL (??)

  * Proc [NFe_Out_UFs].[NSUFALT].[usp_NFeOutUFs_Obter_Last_NSU_Faltante]
    * Pega o NSU em  [NFe_Out_UFs].[NSUFALT].[ULTIMO_NSU_FALTANTE] e retorna como ULTIMO_NSU_FALTANTE
    * Pega o maior valor de "nsuBusca" em [NFe_Out_UFs].[dbo].[IntegraNSU] , subtrai range, e retorna como NSU_LIMITE
 
- Em ambos os casos, atualiza NSUFaltanteConfigurationEntity.NSU_INICIAL (a partir de ULTIMO_NSU_FALTANTE) e NSUFaltanteConfigurationEntity.NSU_LIMITE
 
Em produção:
 * range = 10.000
 * quantidade de linhas = 1.000

Em homologação:
 * range = 2.000
 * quantidade de linhas = 1.000
 
## 2) Thread thrObterDocumentosFaltantesDoAN - ControlObterDocumentosFaltantesDoAN()

Síntese:

 * Lê NSUs de [NFe_Out_UFs].[NSUFALT].[NSU_FALTANTES_QUEUE] e manda para AN. 
 * Se resultado ok:
   * coloca requests em [NFe_Out_UFs].[dbo].[LOG_XML] e [NFe_Out_UFs].[dbo].[LOG_XML_QUEUE] para serem processados pelo Recebimento
   * insere em [NFe_Out_UFs].[NSUFALT].[NSU_FALTANTES_VERIFICACAO] e [NFe_Out_UFs].[NSUFALT].[NSU_FALTANTES_VERIFICACAO_QUEUE]

Loop:

 * Proc [NFe_Out_UFs].[NSUFalt].[usp_NFeOutUFs_Obter_Lista_NSU_Faltante_Queue]
   * Seleciona os TOP N (50 no max) primeiros NSUs da tabela [NFe_Out_UFs].[NSUFALT].[NSU_FALTANTES_QUEUE]
 * Gera XML com nó <consNSUFaltNFe>, com cada NSU retornado como uma tag.
 * Envia XML para o AN e valida resposta
   * Caso cStat (geral, não por NSU) seja 118/123:
     * chama Proc [NFe_Out_UFs].[dbo].[usp_NFeOutUFs_LOG_XML_Create]:
       * insere request/response do AN em [NFe_Out_UFs].[dbo].[LOG_XML]
       * insere índice da tabela acima em [NFe_Out_UFs].[dbo].[LOG_XML_QUEUE]
       * Nota: esses registros serão processados pelo Recebimento, que apagará a fila posteriormente
     * chama Proc [NFe_Out_UFs].[NSUFALT].[usp_NFeOutUFs_Apagar_NSU_Faltante_Queue] passando NSUs 
       * insere em [NFe_Out_UFs].[NSUFALT].[NSU_FALTANTES_VERIFICACAO]
       * insere em [NFe_Out_UFs].[NSUFALT].[NSU_FALTANTES_VERIFICACAO_QUEUE]
       * apaga NSUs de [NFe_Out_UFs].[NSUFALT].[NSU_FALTANTES_QUEUE]
   * Casos de outros retornos, sinaliza erro:
     * Chama proc [NFe_Out_UFs].[NSUFALT].[usp_NFeOutUFs_LOG_XML_Create]
       * Insere request/response/erro em [NFe_Out_UFs].[NSUFALT].[LOG_XML]
 
## 3) Thread thrVerificarNSUsSalvosFilaVerificacao - ControlVerificarNSUsSalvosFilaVerificacao()

Síntese:

 * Lê registros de [NFe_Out_UFs].[NSUFALT].[NSU_FALTANTES_VERIFICACAO_QUEUE]
 * Verifica se respectivo lote foi processado pelo processo NFeRecebimento (se está em [dbo].[LOG_XML_QUEUE]). Se foi, valida se cada NSU foi recebido - [NFe_Out_UFs].[dbo].[IntegraNSU] e [NFe_Out].[NFeIntegra].[IntegraNSU], atualizando [NFe_Out_UFs].[NSUFALT].[NSU_FALTANTES_VERIFICACAO] com erro ou sucesso

Loop:

 * Chama Proc [NFe_Out_UFs].[NSUFALT].[usp_NFeOutUFs_Obter_NSU_Faltantes_Verificacao_Queue], para obter uma lista de NSUs de um mesmo lote
   * Lê NSUs da tabela [NFe_Out_UFs].[NSUFALT].[NSU_FALTANTES_VERIFICACAO_QUEUE] que tenham o MIN(LOCD_XML) em comum
 * Verifica se o primeiro LOTE da lista foi processado, chama proc [NFe_Out_UFs].[NSUFALT].[usp_NFeOutUFs_Verificar_Recebimento_Processado]
   * Retorna "processado" se não existir mais em [NFe_Out_UFs].[dbo].[LOG_XML_QUEUE] e [NFe_Out_UFs].[NSUFALT].[NSU_FALTANTES_VERIFICACAO_QUEUE]
 * Se não foi processado, sleep e verifica de novo, até um número máximo de tentativas.
 * Se atingiu número máximo e não foi processado, loga erro "NSU FALTANTE ainda não processado. Verifique na fila o lote <xxxx>", chamando proc [NFe_Out_UFs].[NSUFALT].[usp_NFeOutUFs_LOG_XML_Create]:
   * Insere request/response/erro em [NFe_Out_UFs].[NSUFALT].[LOG_XML]
 * Se foi processado, valida cada NSU chamando proc [NFe_Out_UFs].[NSUFALT].[usp_NFeOutUFs_Verificar_NSU_Faltantes]
   * Se NSU existe em [NFe_Out_UFs].[NSUFALT].[NSU_FALTANTES_VERIFICACAO_QUEUE] e [NFe_Out_UFs].[dbo].[IntegraNSU], sinaliza que NSU recebido com sucesso em Out_UFs
   * Se NSU existe em [NFe_Out_UFs].[NSUFALT].[NSU_FALTANTES_VERIFICACAO_QUEUE] e [NFe_Out].[NFeIntegra].[IntegraNSU], sinaliza que NSU recebido com sucesso (SCAN)
     * Nota: nesse caso, filtra por fkIntegraTipo=3,4,5 que são relativos a recebimento em contingência.
   * Senão, verifica se tem registro de erro em [NFe_Out_UFs].[NSUFALT].[NSU_FALTANTES_VERIFICACAO_QUEUE], sinalizando erro se houver.
   * Atualiza [NFe_Out_UFs].[NSUFALT].[NSU_FALTANTES_VERIFICACAO] com erro ou sucesso calculados acima
   * Apaga registro do NSU de [NFe_Out_UFs].[NSUFALT].[NSU_FALTANTES_VERIFICACAO_QUEUE]
 
## 4) Thread thrCfgMonitor -> ControlCfgMonitor()
 
 Reinicia o serviço caso a flag em banco FORCE_RESTART_FLAG for setada para 'true'