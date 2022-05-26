
```
USE CTe_Out
GO
-----------------------------------------------------
-- Parâmetros de configuração
SELECT * FROM [SVD].[NSU_FALTANTE_UF] (NOLOCK) 
 ORDER BY NSFU_UF ASC

-----------------------------------------------------
-- Documentos mais recentes reprocessados a partir do NSU Faltantes 
SELECT TOP 10 * 
  FROM [SVD].[QUEUE_DATA_REPROCESSAMENTO] (NOLOCK)
 WHERE QDRE_ORIGEM = 1 
    OR QDRE_ORIGEM = 2
 ORDER BY SQRE_PKEY DESC

 -----------------------------------------------------
-- Logs de erro

-- Genérico
SELECT TOP 10 *
  FROM [SVD].[LOG_SISTEMA] (NOLOCK)
 WHERE LOSI_DESCRICAO like '%CTeNsuFaltantesRecepcaoSvdController%'
 ORDER BY LOSI_PKEY DESC
```
