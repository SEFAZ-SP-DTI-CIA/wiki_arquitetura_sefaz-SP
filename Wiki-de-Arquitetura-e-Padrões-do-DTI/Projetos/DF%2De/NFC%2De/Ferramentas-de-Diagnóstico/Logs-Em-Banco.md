&nbsp; 1) [NFCe_In].[NFCe_In].[ProcessamentoLog]

&nbsp; Registra o host que fez o processamento assíncrono de um lote.

 

&nbsp; 2) [NFCe_Out].[NFCeLog].[ConsultaPublica]

&nbsp; Regista consulta pública via site (modo = 0) ou qr-code (modo = 1)

 

&nbsp; 3) [NFCe_Out].[NFCeLog].[UsoIndevido]

&nbsp; Registra uso indevido de contribuintes (envio de requests repetidos)

 

&nbsp; 4) [NFCe_Out].[NFCeLog].[VerificacaoSAT]

&nbsp; Registra a hora da última execução do serviço de Verificação SAT

 

&nbsp; 5) [NFCe_Out].[NFCeLog].[ValidacaoCertificado]

&nbsp; Registra as validações de certificado que demoraram mais de 2 segundos ou quando o certificado apresenta algum erro.

 

&nbsp; 6) [NFCe_Out].[NFCeLog].[CadespConsulta]

&nbsp; Registra os erros de acesso ao CADESP

 

&nbsp; 7) [NFCe_Out].[NFCeLog].[LogExcecao]

&nbsp; Registra exceções lançadas pelo sistema. Obs: algumas exceções são registradas nas tabelas de LogAudit

 

&nbsp; 8) [NFCe_Out].[NFCeLogAudit].[LogAcesso] e [NFCe_Out].[NFCeLogAudit].[RegistroLog]

&nbsp; Registra logs diversos do sistema, incluindo alguns erros. Para cada registro em LogAcesso, existem zero ou mais entradas em RegistroLog.

 

&nbsp; 9) [NFCe_EPEC_Dados].[EPEC].[LogEPEC] e [NFCe_EPEC_Dados].[EPEC].[RegistroLogEPEC] e

&nbsp; Registra logs diversos do sistema, incluindo erros e exceções. Para cada registro em LogEPEC, existem zero ou mais entradas em RegistroLogEPEC.
