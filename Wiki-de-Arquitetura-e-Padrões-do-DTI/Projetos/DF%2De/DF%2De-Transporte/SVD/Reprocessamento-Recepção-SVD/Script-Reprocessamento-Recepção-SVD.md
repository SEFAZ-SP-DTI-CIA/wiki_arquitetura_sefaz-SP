
```
USE CTe_Out
GO
-----------------------------------------------------
-- Elementos enfileirados
SELECT COUNT(1) FROM [SVD].[QUEUE_REPROCESSAMENTO] (NOLOCK)
SELECT TOP 10 * FROM [SVD].[QUEUE_REPROCESSAMENTO] (NOLOCK) ORDER BY SQRE_PKEY DESC

-----------------------------------------------------
-- Dados dos registros enfileirados para reprocessamento

-- Últimos registros
SELECT TOP 10 * 
  FROM [SVD].[QUEUE_DATA_REPROCESSAMENTO] (NOLOCK)
 ORDER BY SQRE_PKEY DESC

-- Últimos registros com sucesso
 SELECT TOP 10 * 
  FROM [SVD].[QUEUE_DATA_REPROCESSAMENTO] (NOLOCK)
 WHERE QDRE_STATUS = 99
 ORDER BY SQRE_PKEY DESC

-- Últimos registros com falha
 SELECT TOP 10 * 
  FROM [SVD].[QUEUE_DATA_REPROCESSAMENTO] (NOLOCK)
 WHERE QDRE_STATUS <> 99 AND QDRE_STATUS <> 1
 ORDER BY SQRE_PKEY DESC

-- Últimos registros não processados
 SELECT TOP 10 * 
  FROM [SVD].[QUEUE_DATA_REPROCESSAMENTO] (NOLOCK)
 WHERE QDRE_STATUS = 1
 ORDER BY SQRE_PKEY DESC

-----------------------------------------------------
-- Logs

-- Específico do Reprocessamento 
SELECT TOP 10 * 
  FROM [SVD].[QUEUE_LOG_REPROCESSAMENTO] (NOLOCK)
 ORDER BY QLRE_PKEY DESC

-- Específico do Reprocessamento  com dados dos registros reprocessados
SELECT TOP 10 * 
  FROM [SVD].[QUEUE_LOG_REPROCESSAMENTO]  lg (NOLOCK)
  JOIN [SVD].[QUEUE_DATA_REPROCESSAMENTO] dt (NOLOCK)
    ON lg.SQRE_PKEY = dt.SQRE_PKEY
 ORDER BY lg.SQRE_PKEY DESC

-- Genérico
SELECT TOP 10 *
  FROM [SVD].[LOG_SISTEMA] (NOLOCK)
 WHERE LOSI_DESCRICAO like '%CTeReprocessamentoRecepcaoSvdBll%'
 ORDER BY LOSI_PKEY DESC
```
