```
USE CTe_Out
GO
-----------------------------------------------------
-- Eventos enfileirados
SELECT COUNT(1) FROM [SVD].[QUEUE_EVENTO_FISCO] (NOLOCK) --where SQEF_PKEY = 187747459
SELECT COUNT(1) FROM [SVD].[QUEUE_EVENTO_FISCO] (NOLOCK) WHERE [QUEF_NEXT_TRY_DATE] <= SYSUTCDATETIME()

-- Ordenado por pkey
SELECT TOP 500 * FROM [SVD].[QUEUE_EVENTO_FISCO] (NOLOCK) ORDER BY SQEF_PKEY DESC
SELECT TOP 500 * FROM [SVD].[QUEUE_EVENTO_FISCO] (NOLOCK) ORDER BY SQEF_PKEY ASC

-- Ordenado por NextTryDate
SELECT TOP 500 * FROM [SVD].[QUEUE_EVENTO_FISCO] (NOLOCK) ORDER BY QUEF_NEXT_TRY_DATE DESC
SELECT TOP 500 * FROM [SVD].[QUEUE_EVENTO_FISCO] (NOLOCK) ORDER BY QUEF_NEXT_TRY_DATE ASC

-----------------------------------------------------
-- Dados dos eventos enfileirados

-- Por pkey (mais rápido)
SELECT TOP 200 d.*, CAST(DECOMPRESS(QDEF_XML_RESPONSE) AS XML)
  FROM [SVD].[QUEUE_DATA_EVENTO_FISCO] d (NOLOCK)
 ORDER BY d.SQEF_PKEY DESC 

-- Por data (mais demorado)
SELECT TOP 200 d.*, CAST(DECOMPRESS(QDEF_XML_RESPONSE) AS XML)
  FROM [SVD].[QUEUE_DATA_EVENTO_FISCO] d (NOLOCK)
 ORDER BY d.QDEF_DATA_ENVIO DESC 

-- Eventos enfileirados e já processados (fora da fila) ordenado por Pkey
SELECT TOP 20 d.*, CAST(DECOMPRESS(QDEF_XML_RESPONSE) AS XML)
  FROM      [SVD].[QUEUE_DATA_EVENTO_FISCO] d (NOLOCK)
  LEFT JOIN [SVD].[QUEUE_EVENTO_FISCO]      q (NOLOCK) 
    ON d.SQEF_PKEY = q.SQEF_PKEY
 WHERE q.SQEF_PKEY IS NULL
 ORDER BY d.SQEF_PKEY DESC 

-- Eventos enfileirados e já processados (fora da fila) ordenado por Data de Envio
SELECT TOP 20 d.*, CAST(DECOMPRESS(QDEF_XML_RESPONSE) AS XML)
  FROM      [SVD].[QUEUE_DATA_EVENTO_FISCO] d (NOLOCK)
  LEFT JOIN [SVD].[QUEUE_EVENTO_FISCO]      q (NOLOCK) 
    ON d.SQEF_PKEY = q.SQEF_PKEY
 WHERE q.SQEF_PKEY IS NULL
 ORDER BY d.QDEF_DATA_ENVIO DESC 

 -- Eventos ainda fila
SELECT TOP 1000 QDEF_DATA_ENVIO, QUEF_NEXT_TRY_DATE, q.*, d.*
  FROM [SVD].[QUEUE_DATA_EVENTO_FISCO] d (NOLOCK)
  JOIN [SVD].[QUEUE_EVENTO_FISCO]      q (NOLOCK) 
    ON d.SQEF_PKEY = q.SQEF_PKEY
WHERE QUEF_TRIES > 1
 ORDER BY q.SQEF_PKEY DESC 

 -- Eventos ainda fila join com log mais recente
SELECT TOP 1000 QDEF_DATA_ENVIO, QUEF_NEXT_TRY_DATE, q.*, d.*, l.*
  FROM [SVD].[QUEUE_DATA_EVENTO_FISCO] d (NOLOCK)
  JOIN [SVD].[QUEUE_EVENTO_FISCO]      q (NOLOCK) 
    ON d.SQEF_PKEY = q.SQEF_PKEY
  JOIN [SVD].[QUEUE_LOG_EVENTO_FISCO] l (NOLOCK)
    ON d.SQEF_PKEY = l.SQEF_PKEY
 WHERE QUEF_TRIES > 1
   AND l.QLEF_PKEY IN (SELECT MAX(QLEF_PKEY) 
                         FROM [SVD].[QUEUE_LOG_EVENTO_FISCO] (NOLOCK)
                         GROUP BY SQEF_PKEY)
 ORDER BY q.SQEF_PKEY DESC   
-----------------------------------------------------
-- Logs

-- Específico do Gerar Eventos Fisco (gerado no reenfileiramento de eventos)
SELECT TOP 1000 *, CAST(DECOMPRESS(QLEF_XML_REQUEST) AS XML), CAST(DECOMPRESS(QLEF_XML_RESPONSE) AS XML)       
  FROM [SVD].[QUEUE_LOG_EVENTO_FISCO] (NOLOCK)
 ORDER BY QLEF_PKEY DESC

-- Específico do Gerar Eventos Fisco do dia corrente, analisando os maiores causadores
SELECT QLEF_STATUS, COUNT(QLEF_STATUS) as NroOcorrencias
  FROM [SVD].[QUEUE_LOG_EVENTO_FISCO] (NOLOCK)
 WHERE QLEF_DATE > CONVERT(DATE, GETDATE())
 GROUP BY QLEF_STATUS

-- QUEUE_LOG e QUEUE_DATA
SELECT TOP 20 QLEF_DATE, l.*, d.*
  FROM [SVD].[QUEUE_DATA_EVENTO_FISCO] d (NOLOCK)
  JOIN [SVD].[QUEUE_LOG_EVENTO_FISCO]  l (NOLOCK)
    ON d.SQEF_PKEY = l.SQEF_PKEY
 ORDER BY d.SQEF_PKEY DESC 

-- QUEUE_LOG e QUEUE_DATA enfileirados
SELECT TOP 100 QLEF_DATE, QUEF_NEXT_TRY_DATE, l.*, q.*, d.*
  FROM [SVD].[QUEUE_DATA_EVENTO_FISCO] d (NOLOCK)
  JOIN [SVD].[QUEUE_LOG_EVENTO_FISCO]  l (NOLOCK)
    ON d.SQEF_PKEY = l.SQEF_PKEY
  JOIN [SVD].[QUEUE_EVENTO_FISCO]      q (NOLOCK) 
    ON d.SQEF_PKEY = q.SQEF_PKEY
 ORDER BY d.SQEF_PKEY DESC
 
-- Contador erros 678 = Uso Indevido
SELECT DATEPART(MONTH, QLEF_DATE) as Mes, DATEPART(DAY, QLEF_DATE) as Dia, COUNT(1) as NroOcorrencias
  FROM [SVD].[QUEUE_LOG_EVENTO_FISCO] (NOLOCK)
 WHERE QLEF_DATE > '2021-1-1'
   AND QLEF_STATUS = 678
   AND DATEPART(WEEKDAY, QLEF_DATE) > 1
   AND DATEPART(WEEKDAY, QLEF_DATE) < 7
 GROUP BY DATEPART(MONTH, QLEF_DATE), DATEPART(DAY, QLEF_DATE)
 ORDER BY DATEPART(MONTH, QLEF_DATE), DATEPART(DAY, QLEF_DATE)

-- Genérico
SELECT TOP 20 *
  FROM [SVD].[LOG_SISTEMA] (NOLOCK)
 WHERE LOSI_DESCRICAO like '%CTeGerarEventoFiscoBLL%'
 --AND LOSI_DESCRICAO not like '%There was no endpoint listening%'
 --AND LOSI_DESCRICAO like '%CallWebService%'
 ORDER BY LOSI_PKEY DESC

-----------------------------------------------------
-- Eventos órfãos 
SELECT d.*
  FROM      [SVD].[QUEUE_DATA_EVENTO_FISCO] d (NOLOCK)
  LEFT JOIN [SVD].[QUEUE_EVENTO_FISCO]      q (NOLOCK) 
    ON d.SQEF_PKEY = q.SQEF_PKEY
 WHERE q.SQEF_PKEY   IS NULL
   AND d.QDEF_STATUS IS NULL
   AND d.QDEF_DATA_RECEPCAO_CTE < DATEADD(HOUR, -1, SYSDATETIMEOFFSET())

-----------------------------------------------------
-- Monitoração da fila

SELECT DATEDIFF(MINUTE, QDEF_DATA_ENVIO       , SYSUTCDATETIME()) as 'Minutos sem autorizar Evento Fisco com sucesso', 
       DATEDIFF(MINUTE, QDEF_DATA_RECEPCAO_CTE, SYSUTCDATETIME()) as 'Minutos desde recepção documento até autorização Evento Fisco'
  FROM [SVD].[QUEUE_DATA_EVENTO_FISCO] d (NOLOCK)
 WHERE SQEF_PKEY = (SELECT TOP 1 d.SQEF_PKEY 
                      FROM      [SVD].[QUEUE_DATA_EVENTO_FISCO] d (NOLOCK)
                      LEFT JOIN [SVD].[QUEUE_EVENTO_FISCO]      q (NOLOCK) 
                        ON d.SQEF_PKEY = q.SQEF_PKEY
                     WHERE q.SQEF_PKEY IS NULL
                     ORDER BY d.SQEF_PKEY DESC)

DECLARE @minPkeyFila BIGINT = (SELECT MIN(q.SQEF_PKEY)
                                 FROM [SVD].[QUEUE_DATA_EVENTO_FISCO] d (NOLOCK)
                                 JOIN [SVD].[QUEUE_EVENTO_FISCO]      q (NOLOCK) 
                                   ON d.SQEF_PKEY = q.SQEF_PKEY)

SELECT TOP 10 QDEF_DATA_RECEPCAO_CTE as 'Data do evento mais antigo ainda enfileirado', *
  FROM [SVD].[QUEUE_DATA_EVENTO_FISCO] d (NOLOCK)
  JOIN [SVD].[QUEUE_EVENTO_FISCO]      q (NOLOCK) 
    ON d.SQEF_PKEY = q.SQEF_PKEY
 WHERE d.SQEF_PKEY = @minPkeyFila
 ORDER BY d.SQEF_PKEY ASC 

DECLARE @LimiteEnfileiramentoEventosDias INT = 1
SELECT COUNT(1) as EventosEnfileiradosMaisDeXDias
  FROM [SVD].[QUEUE_DATA_EVENTO_FISCO] d (NOLOCK)
  JOIN [SVD].[QUEUE_EVENTO_FISCO]      q (NOLOCK) 
    ON d.SQEF_PKEY = q.SQEF_PKEY
 WHERE DATEDIFF(DAY, QDEF_DATA_RECEPCAO_CTE, SYSUTCDATETIME()) > @LimiteEnfileiramentoEventosDias

SELECT *
  FROM [SVD].[QUEUE_DATA_EVENTO_FISCO] d (NOLOCK)
  JOIN [SVD].[QUEUE_EVENTO_FISCO]      q (NOLOCK) 
    ON d.SQEF_PKEY = q.SQEF_PKEY
 WHERE DATEDIFF(DAY, QDEF_DATA_RECEPCAO_CTE, SYSUTCDATETIME()) > @LimiteEnfileiramentoEventosDias
 ORDER BY d.SQEF_PKEY ASC 

-----------------------------------------------------
-- Análise individual por pkey
DECLARE @SQEF_PKEY BIGINT = 214381224
DECLARE @DataAutorizacaoCTe DATETIMEOFFSET = (SELECT QDEF_DATA_RECEPCAO_CTE FROM [SVD].[QUEUE_DATA_EVENTO_FISCO] d (NOLOCK) WHERE d.SQEF_PKEY  = @SQEF_PKEY)

SELECT *
  FROM [SVD].[QUEUE_EVENTO_FISCO]      q (NOLOCK) 
 WHERE q.SQEF_PKEY  = @SQEF_PKEY
 ORDER BY q.SQEF_PKEY ASC 

SELECT *
     FROM [SVD].[QUEUE_DATA_EVENTO_FISCO] d (NOLOCK)
LEFT JOIN [SVD].[QUEUE_EVENTO_FISCO]      q (NOLOCK) 
       ON d.SQEF_PKEY = q.SQEF_PKEY
    WHERE d.SQEF_PKEY  = @SQEF_PKEY
    ORDER BY d.SQEF_PKEY ASC 

SELECT *, CAST(DECOMPRESS(QLEF_XML_REQUEST) AS XML) as Request, CAST(DECOMPRESS(QLEF_XML_RESPONSE) AS XML) as Response
  FROM [SVD].[QUEUE_LOG_EVENTO_FISCO] l (NOLOCK)
 WHERE SQEF_PKEY = @SQEF_PKEY
 ORDER BY QLEF_PKEY DESC

SELECT  *
  FROM [SVD].[LOG_SISTEMA] (NOLOCK)
 WHERE LOSI_DESCRICAO like '%CTeGerarEventoFiscoBLL%'
   AND LOSI_DATA > @DataAutorizacaoCTe
   AND LOSI_DATA < DATEADD(DAY, 1, @DataAutorizacaoCTe)
 ORDER BY LOSI_PKEY DESC
-----------------------------------------------------
-- Análise feita na Monitoração - hora atual

DECLARE @horaInicial DATETIMEOFFSET = SYSDATETIMEOFFSET()

DECLARE @thresholdMinutosEventoFiscoSPFila INT = (SELECT CONVERT(INT, [COSI_VALOR])
                                                    FROM [SVD].[CONFIGURACAO_SISTEMA] WITH (NOLOCK)
                                                   WHERE [COSI_NOME] = 'MON_SVD_THRESH_MINUTOS_EVENTOS_FISCO_SP_FILA')

DECLARE @thresholdMinutosEventoFiscoUFsFila INT = (SELECT CONVERT(INT, [COSI_VALOR])
                                                     FROM [SVD].[CONFIGURACAO_SISTEMA] WITH (NOLOCK)
                                                    WHERE [COSI_NOME] = 'MON_SVD_THRESH_MINUTOS_EVENTOS_FISCO_UFS_FILA')

DECLARE @horarioLimiteSuperiorSP  DATETIMEOFFSET = DATEADD(MINUTE, -@thresholdMinutosEventoFiscoSPFila      , @horaInicial)
DECLARE @horarioLimiteInferiorSP  DATETIMEOFFSET = DATEADD(MINUTE, -@thresholdMinutosEventoFiscoSPFila - 120, @horaInicial)

DECLARE @horarioLimiteSuperiorUFs DATETIMEOFFSET = DATEADD(MINUTE, -@thresholdMinutosEventoFiscoUFsFila      , @horaInicial)
DECLARE @horarioLimiteInferiorUFs DATETIMEOFFSET = DATEADD(MINUTE, -@thresholdMinutosEventoFiscoUFsFila - 120, @horaInicial)

SELECT 'SP' , @horarioLimiteInferiorSP  AS 'Limite Inferior SP'   , @horarioLimiteSuperiorSP  AS 'Limite Superior SP'
SELECT 'UFs', @horarioLimiteInferiorUFs AS 'Limite Inteferior UFs', @horarioLimiteSuperiorUFs AS 'Limite Superior UFs'

SELECT *
  FROM [SVD].[QUEUE_DATA_EVENTO_FISCO] WITH (NOLOCK)
 WHERE [QDEF_UF] = 35
   AND [QDEF_DATA_ENVIO] IS NULL 
   AND [QDEF_DATA_RECEPCAO_CTE] >= @horarioLimiteInferiorSP
   AND [QDEF_DATA_RECEPCAO_CTE] <= @horarioLimiteSuperiorSP

 SELECT *
   FROM [SVD].[QUEUE_DATA_EVENTO_FISCO] WITH (NOLOCK)
  WHERE [QDEF_UF] <> 35
    AND [QDEF_DATA_ENVIO] IS NULL 
    AND [QDEF_DATA_RECEPCAO_CTE] >= @horarioLimiteInferiorUFs
    AND [QDEF_DATA_RECEPCAO_CTE] <= @horarioLimiteSuperiorUFs

SELECT *
  FROM    [SVD].[QUEUE_DATA_EVENTO_FISCO] d WITH (NOLOCK)
LEFT JOIN [SVD].[QUEUE_LOG_EVENTO_FISCO]  l WITH (NOLOCK)
    ON l.SQEF_PKEY = d.SQEF_PKEY
 WHERE [QDEF_UF] = 35
   AND [QDEF_DATA_ENVIO] IS NULL 
   AND [QDEF_DATA_RECEPCAO_CTE] >= @horarioLimiteInferiorSP
   AND [QDEF_DATA_RECEPCAO_CTE] <= @horarioLimiteSuperiorSP

 SELECT *
   FROM   [SVD].[QUEUE_DATA_EVENTO_FISCO] d WITH (NOLOCK)
LEFT JOIN [SVD].[QUEUE_LOG_EVENTO_FISCO]  l WITH (NOLOCK)
    ON l.SQEF_PKEY = d.SQEF_PKEY
  WHERE [QDEF_UF] <> 35
    AND [QDEF_DATA_ENVIO] IS NULL 
    AND [QDEF_DATA_RECEPCAO_CTE] >= @horarioLimiteInferiorUFs
    AND [QDEF_DATA_RECEPCAO_CTE] <= @horarioLimiteSuperiorUFs

GO
-----------------------------------------------------------------------------
-- Análise feita na Monitoração - hora definida

DECLARE @horaInicial DATETIMEOFFSET = '2021-8-04 13:40 -03:00'

DECLARE @thresholdMinutosEventoFiscoSPFila INT = (SELECT CONVERT(INT, [COSI_VALOR])
                                                    FROM [SVD].[CONFIGURACAO_SISTEMA] WITH (NOLOCK)
                                                   WHERE [COSI_NOME] = 'MON_SVD_THRESH_MINUTOS_EVENTOS_FISCO_SP_FILA')

DECLARE @thresholdMinutosEventoFiscoUFsFila INT = (SELECT CONVERT(INT, [COSI_VALOR])
                                                     FROM [SVD].[CONFIGURACAO_SISTEMA] WITH (NOLOCK)
                                                    WHERE [COSI_NOME] = 'MON_SVD_THRESH_MINUTOS_EVENTOS_FISCO_UFS_FILA')

DECLARE @horarioLimiteSuperiorSP  DATETIMEOFFSET = DATEADD(MINUTE, -@thresholdMinutosEventoFiscoSPFila      , @horaInicial)
DECLARE @horarioLimiteInferiorSP  DATETIMEOFFSET = DATEADD(MINUTE, -@thresholdMinutosEventoFiscoSPFila - 120, @horaInicial)

DECLARE @horarioLimiteSuperiorUFs DATETIMEOFFSET = DATEADD(MINUTE, -@thresholdMinutosEventoFiscoUFsFila      , @horaInicial)
DECLARE @horarioLimiteInferiorUFs DATETIMEOFFSET = DATEADD(MINUTE, -@thresholdMinutosEventoFiscoUFsFila - 120, @horaInicial)

SELECT 'SP' , @horarioLimiteInferiorSP  AS 'Limite Inferior SP'   , @horarioLimiteSuperiorSP  AS 'Limite Superior SP'
SELECT 'UFs', @horarioLimiteInferiorUFs AS 'Limite Inteferior UFs', @horarioLimiteSuperiorUFs AS 'Limite Superior UFs'

SELECT *
  FROM [SVD].[QUEUE_DATA_EVENTO_FISCO] WITH (NOLOCK)
 WHERE [QDEF_UF] = 35
   AND [QDEF_DATA_ENVIO] > @horaInicial
   AND [QDEF_DATA_RECEPCAO_CTE] >= @horarioLimiteInferiorSP
   AND [QDEF_DATA_RECEPCAO_CTE] <= @horarioLimiteSuperiorSP

 SELECT *
   FROM [SVD].[QUEUE_DATA_EVENTO_FISCO] WITH (NOLOCK)
  WHERE [QDEF_UF] <> 35
    AND [QDEF_DATA_ENVIO] > @horaInicial
    AND [QDEF_DATA_RECEPCAO_CTE] >= @horarioLimiteInferiorUFs
    AND [QDEF_DATA_RECEPCAO_CTE] <= @horarioLimiteSuperiorUFs

SELECT *
  FROM    [SVD].[QUEUE_DATA_EVENTO_FISCO] d WITH (NOLOCK)
LEFT JOIN [SVD].[QUEUE_LOG_EVENTO_FISCO]  l WITH (NOLOCK)
    ON l.SQEF_PKEY = d.SQEF_PKEY
 WHERE [QDEF_UF] = 35
   AND [QDEF_DATA_ENVIO] > @horaInicial
   AND [QDEF_DATA_RECEPCAO_CTE] >= @horarioLimiteInferiorSP
   AND [QDEF_DATA_RECEPCAO_CTE] <= @horarioLimiteSuperiorSP

 SELECT *
   FROM   [SVD].[QUEUE_DATA_EVENTO_FISCO] d WITH (NOLOCK)
LEFT JOIN [SVD].[QUEUE_LOG_EVENTO_FISCO]  l WITH (NOLOCK)
    ON l.SQEF_PKEY = d.SQEF_PKEY
  WHERE [QDEF_UF] <> 35
    AND [QDEF_DATA_ENVIO] IS NULL -- > @horaInicial
    AND [QDEF_DATA_RECEPCAO_CTE] >= @horarioLimiteInferiorUFs
    AND [QDEF_DATA_RECEPCAO_CTE] <= @horarioLimiteSuperiorUFs

-----------------------------------------------------------------------------
-- Acompanhamento de eventos de chave de SP

DECLARE @chCTe CHAR(44) = '51211033070814001042570030035197431142129934'
DECLARE @tipoEvento INT = 440130
DECLARE @uf TINYINT = CAST(SUBSTRING(@chCTe, 1, 2) AS TINYINT)

SELECT '' as CTeOut_CTe, * FROM [CTeOut].[CTe] (nolock)
 where chCTe = @chCTe 

SELECT '' as CTeEventos_Constraint, * , CAST(decompress(mensagemBin) as XML)
  FROM [CTeEventos].[Evento_Constraint]  c (nolock)
  JOIN [CTe_Out_XML].[CTeEventos].[EventoXml] x (nolock)
    ON c.fkProtocolo = x.pKey
  JOIN [CTeOut].[Protocolo] p (nolock)
    ON c.fkProtocolo = p.pKey
 WHERE chCTe = @chCTe 

SELECT '' as SVC_Evento, * 
  FROM [SVC].[Evento] (nolock)
 WHERE chDFe = @chCTe 

SELECT '' as CTe_Out_UFs_dbo_CTe, * 
  FROM [CTe_Out_UFs].[dbo].[CTe] (nolock)
 WHERE idCTe = @chCTe

SELECT '' as CTe_Out_UFs_CTeEventos_Evento, * 
  FROM [CTe_Out_UFs].[CTeEventos].[Evento] (nolock)
 WHERE idCTe = @chCTe

SELECT q.*, d.*, CAST(decompress(QDEF_XML_RESPONSE) as xml)
  FROM      [SVD].[QUEUE_DATA_EVENTO_FISCO] d (NOLOCK)
  LEFT JOIN [SVD].[QUEUE_EVENTO_FISCO]      q (NOLOCK) 
    ON d.SQEF_PKEY = q.SQEF_PKEY
 WHERE [QDEF_TIPO_EVENTO] = @tipoEvento
   AND [QDEF_UF]		  = @uf
   AND [QDEF_CNPJ_EMIT]	  = SUBSTRING(@chCTe, 7, 14)
   AND [QDEF_MODELO]	  = SUBSTRING(@chCTe, 21, 2)
   AND [QDEF_SERIE]		  = SUBSTRING(@chCTe, 23, 3)
   AND [QDEF_NUMERO]	  = SUBSTRING(@chCTe, 26, 9)
 ORDER BY d.SQEF_PKEY ASC

SELECT l.*, d.*, CAST(decompress(QDEF_XML_RESPONSE) as xml)
  FROM      [SVD].[QUEUE_DATA_EVENTO_FISCO] d (NOLOCK)
  LEFT JOIN [SVD].[QUEUE_LOG_EVENTO_FISCO]  l (NOLOCK) 
    ON d.SQEF_PKEY = l.SQEF_PKEY
 WHERE [QDEF_TIPO_EVENTO] = @tipoEvento
   AND [QDEF_UF]		  = @uf
   AND [QDEF_CNPJ_EMIT]	  = SUBSTRING(@chCTe, 7, 14)
   AND [QDEF_MODELO]	  = SUBSTRING(@chCTe, 21, 2)
   AND [QDEF_SERIE]		  = SUBSTRING(@chCTe, 23, 3)
   AND [QDEF_NUMERO]	  = SUBSTRING(@chCTe, 26, 9)
 ORDER BY QLEF_PKEY ASC, d.SQEF_PKEY ASC
```
