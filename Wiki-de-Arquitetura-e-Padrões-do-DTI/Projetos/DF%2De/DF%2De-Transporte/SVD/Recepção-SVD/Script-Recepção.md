
```
USE CTe_Out
GO
-----------------------------------------------------
-- Parâmetros de configuração
SELECT * FROM [SVD].[RECEPCAO_UF] (NOLOCK)

-----------------------------------------------------
-- Lotes recebidos do RS

-- Requisições mais recentes
SELECT TOP 10 REXM_DATA_RECEPCAO, * 
  FROM [SVD].[RECEPCAO_XML] (NOLOCK)
 ORDER BY REXM_PKEY DESC

-- Requisições mais recentes com dados
SELECT TOP 10 REXM_DATA_RECEPCAO, * , CONVERT(XML,DECOMPRESS(REXM_XML_REQUEST)), CONVERT(XML,DECOMPRESS(REXM_XML_RESPONSE))
  FROM [SVD].[RECEPCAO_XML] (NOLOCK)
 WHERE REXM_STATUS = 118
 ORDER BY REXM_PKEY DESC

-----------------------------------------------------
-- Documentos armazenados mais recentes
SELECT TOP 10 RENS_DATA_INTEGRA, * 
  FROM [SVD].[RECEPCAO_NSU] (NOLOCK)
 ORDER BY RENS_PKEY DESC 

-- Documentos armazenados mais recentes e respectivos lotes
SELECT TOP 10 RENS_DATA_INTEGRA, * 
  FROM [SVD].[RECEPCAO_NSU] n (NOLOCK)
  JOIN [SVD].[RECEPCAO_XML] x (NOLOCK)
    ON n.REXM_PKEY = x.REXM_PKEY
 ORDER BY RENS_PKEY DESC 

-----------------------------------------------------
-- Logs de erro

-- Específico
SELECT TOP 10 * 
  FROM [SVD].[QUEUE_DATA_REPROCESSAMENTO] (NOLOCK)
 WHERE QDRE_ORIGEM = 3 -- CTeOrigemReprocessamentoSvd.ERRO_RECEPCAO
 ORDER BY SQRE_PKEY DESC

-- Genérico
SELECT TOP 10 *
  FROM [SVD].[LOG_SISTEMA] (NOLOCK)
 WHERE LOSI_DESCRICAO like '%CTeRecepcaoSvdBll%'
    OR LOSI_DESCRICAO like '%CTeRecepcaoSvdProcessItemBLL_v1_00%'
 ORDER BY LOSI_PKEY DESC

-----------------------------------------------------
-- Monitoração de últimos recebimentos

-- Lotes
SELECT TOP 1 DATEDIFF(MINUTE, REXM_DATA_RECEPCAO, GETDATE()) as 'Minutos sem receber Lotes do RS', *
  FROM [SVD].[RECEPCAO_XML] (NOLOCK)
 WHERE REXM_STATUS = 118
 ORDER BY REXM_PKEY DESC

-- Documentos
 SELECT TOP 1 DATEDIFF(MINUTE, RENS_DATA_INTEGRA, GETDATE()) as 'Minutos sem armazenar documentos', * 
   FROM [SVD].[RECEPCAO_NSU] (NOLOCK)
  ORDER BY RENS_PKEY DESC 

-- Documentos por UF
DECLARE @maxPkey BIGINT = (SELECT MAX(RENS_PKEY) FROM [SVD].[RECEPCAO_NSU] (NOLOCK))
 SELECT DATEDIFF(MINUTE, RENS_DATA_INTEGRA, GETDATE()) as 'Minutos sem armazenar documentos da UF', RENS_UF
   FROM [SVD].[RECEPCAO_NSU] (NOLOCK)
  WHERE RENS_PKEY IN (SELECT MAX(RENS_PKEY)
                        FROM [SVD].[RECEPCAO_NSU] (NOLOCK)
                       WHERE RENS_PKEY > @maxPkey - 100000 
                       GROUP BY RENS_UF)
  ORDER BY RENS_UF
```
