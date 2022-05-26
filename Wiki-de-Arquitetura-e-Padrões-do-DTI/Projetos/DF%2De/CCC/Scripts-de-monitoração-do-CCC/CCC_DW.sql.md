
```
USE CCC
GO

SELECT '' AS 'CCC.DW.QUEUE', COUNT(1) 
FROM [CCC].[DW].[QUEUE] (nolock)

SELECT TOP 3 '' AS 'CCC.DW.QUEUE - mais novos', * 
FROM [CCC].[DW].[QUEUE] (nolock)
ORDER BY timestampReg DESC

SELECT TOP 3 '' AS 'CCC.DW.QUEUE - mais antigos', * 
FROM [CCC].[DW].[QUEUE] (nolock)
ORDER BY timestampReg ASC

-- Análise de contribuintes na fila do DW
SELECT TOP 3 '' AS 'Fila DW join Contribuinte', * 
  FROM      [DW].[Queue] q (NOLOCK)
 INNER JOIN [ccc].[Contribuinte] c
   ON q.NSUInc = c.NSUCCC 
   -- q.NSUAtu = c.NSUCCCMovto
 --WHERE c.cUF = 35
 ORDER BY q.timestampReg DESC

--=========================================================

USE [ETL_NFE]
GO

SELECT * 
FROM [DWCCC].[ConfigSistema]

DECLARE @hojeZeroHora DATETIME = CONVERT(DATE, GETDATE())
SELECT '' AS 'ResultadoProcessamento de hoje', count(houveErro) as NroRegistros, houveErro
  FROM [ETL_NFE].[DWCCC].[ResultadoProcessamento] (NOLOCK)
 WHERE dataProcessamento > @hojeZeroHora 
 GROUP BY houveErro

SELECT TOP 500000 '' AS '[ETL_NFE].[DWCCC].[ResultadoProcessamento]', *
  FROM [ETL_NFE].[DWCCC].[ResultadoProcessamento] (NOLOCK)
  ORDER BY dataProcessamento DESC

SELECT TOP 50 '' AS '[ETL_NFE].[DWCCC].[ResultadoProcessamento] - com erro', *
  FROM [ETL_NFE].[DWCCC].[ResultadoProcessamento] (NOLOCK)
  WHERE houveErro = 1
  --WHERE NSUAtu = 22335318 AND NSUInc = 5807502
  ORDER BY dataProcessamento DESC

SELECT TOP 50 '' AS '[ETL_NFE].[DWCCC].[LogErros]', *
  FROM [ETL_NFE].[DWCCC].[LogErros] (NOLOCK)
ORDER BY pKey DESC

SELECT TOP 50 '' AS '[ETL_NFE].[DWCCC].[ProcessamentoQueue]',*
  FROM [ETL_NFE].[DWCCC].[ProcessamentoQueue] (NOLOCK)
ORDER BY pKey DESC

--========= REENFILEIRANDO REGISTROS ========================

-- Registros que deram errado explicitamente
/*
DELETE FROM [ETL_NFE].[DWCCC].[ResultadoProcessamento]
OUTPUT 
    DELETED.pKey,
    DELETED.NSUInc,
    DELETED.NSUAtu,
    DELETED.timestampReg
INTO 
    CCC.DW.QUEUE
    (pKey, NSUInc, NSUAtu, timestampReg)
WHERE houveErro = 1 AND dataProcessamento > '2017-04-03 16:20:00.000'
*/

-- Registros que deram errado mas que estão correto na ResultadoProcessamento
/*
SET IDENTITY_INSERT [DW].[Queue] ON
GO

DELETE FROM [ETL_NFE].[DWCCC].[ResultadoProcessamento]
OUTPUT
    DELETED.pKey, 
    DELETED.NSUInc,
    DELETED.NSUAtu,
    DELETED.timestampReg
INTO 
    CCC.DW.QUEUE
    (pKey, NSUInc, NSUAtu, timestampReg)
WHERE dataProcessamento >= '2017-07-15 00:00:00.000'
  AND dataProcessamento <  '2017-07-16 00:00:00.000'

SET IDENTITY_INSERT [DW].[Queue] OFF
GO
*/
```
