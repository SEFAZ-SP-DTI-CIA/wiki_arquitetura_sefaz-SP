# AcompanhamentoSQLDW

-- Processamento DW
SELECT TOP 20 *, CONVERT(varchar, (PRCT_DT_FIM_PROCESSAMENTO - PRCT_DT_INICIO_PROCESSAMENTO), 108) as TempoProcessamento
  FROM [ETL_NFCe].[Monitoracao].[Processamento]
  ORDER BY PRCT_ID_PROCESSAMENTO DESC
 
SELECT TOP 20 *
  FROM [ETL_NFCe].[Monitoracao].[Log_Execucao_Processo]
  ORDER BY LEXP_DT_LOG DESC
 
SELECT TOP 20 *
  FROM [ETL_NFCe].[Monitoracao].[Execucao_Processo]
  ORDER BY EXPR_ID_EXECUCAO_PROCESSO DESC
 
SELECT * FROM [ETL_NFCe].[Monitoracao].[Processo]
 
-- Config
SELECT * FROM [ETL_NFCe].[Monitoracao].[ConfigSistema]
 
SELECT TOP 100 *
  FROM [ETL_NFCe].[Monitoracao].[ConfigSistema_LOG]
 ORDER BY pKeyLog DESC