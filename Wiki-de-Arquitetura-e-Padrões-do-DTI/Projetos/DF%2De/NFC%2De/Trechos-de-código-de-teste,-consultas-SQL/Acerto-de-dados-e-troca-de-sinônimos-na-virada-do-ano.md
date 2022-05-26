# NFCeTrocaSinonimosInicioAno


```
--------------------------------------------------------------------------------------
-- Todos os scripts devem ser rodados depois que as 3 bases forem copiadas
-- Instrução: renomeie as ocorrências de <ano> para <ano+1>
--------------------------------------------------------------------------------------
 
--------------------------------------------------------------------------------
--- Essa primeira parte deve ser rodada para verificar os dados a serem apagados
--------------------------------------------------------------------------------
 
SELECT pkey, timestampReg
  FROM [NFCe_Out_XML_2016].[NFCeOut].[EventoXml]
  where timestampReg < '2016-01-01' order by pkey asc
 
SELECT pkey, timestampReg
  FROM [NFCe_Out_XML_2015].[NFCeOut].[EventoXml]
  where timestampReg >= '2016-01-01' order by pkey asc
-----
SELECT pkey, timestampReg
  FROM [NFCe_Out_XML_2016].[NFCeOut].[NFCeXml]
  where timestampReg < '2016-01-01' order by pkey asc
 
SELECT pkey, timestampReg
  FROM [NFCe_Out_XML_2015].[NFCeOut].[NFCeXml]
  where timestampReg >= '2016-01-01' order by pkey asc
-----
SELECT pkey, timestampReg
  FROM [NFCe_Out_XML_2016].[NFCeOut].[InutXml]
  where timestampReg < '2016-01-01' order by pkey asc
 
SELECT pkey, timestampReg
  FROM [NFCe_Out_XML_2015].[NFCeOut].[InutXml]
  where timestampReg >= '2016-01-01' order by pkey asc
 
SELECT * FROM NFCe_Out.NFceOut.PeriodoPKeys
 
RETURN
-----------------------------------------------------------------------------
--- Essa segunda parte é o que realmente faz o acerto dos dados
-----------------------------------------------------------------------------
DELETE
  FROM [NFCe_Out_XML_2016].[NFCeOut].[EventoXml]
  where timestampReg < '2016-01-01'
 
DELETE
  FROM [NFCe_Out_XML_2015].[NFCeOut].[EventoXml]
  where timestampReg >= '2016-01-01'
-----
DELETE
  FROM [NFCe_Out_XML_2016].[NFCeOut].[NFCeXml]
  where timestampReg < '2016-01-01'
 
DELETE
  FROM [NFCe_Out_XML_2015].[NFCeOut].[NFCeXml]
  where timestampReg >= '2016-01-01'
-----
DELETE
  FROM [NFCe_Out_XML_2016].[NFCeOut].[InutXml]
  where timestampReg < '2016-01-01'
 
DELETE
  FROM [NFCe_Out_XML_2015].[NFCeOut].[InutXml]
  where timestampReg >= '2016-01-01'
 
USE [NFCe_Out_XML]
GO
 
DROP SYNONYM [NFCeOut].[EventoXml]
DROP SYNONYM [NFCeOut].[InutXml]
DROP SYNONYM [NFCeOut].[NFCeXml]
GO
 
CREATE SYNONYM [NFCeOut].[EventoXml] FOR [NFCe_Out_XML_2016].[NFCeOut].[EventoXml]
CREATE SYNONYM [NFCeOut].[InutXml]   FOR [NFCe_Out_XML_2016].[NFCeOut].[InutXml]
CREATE SYNONYM [NFCeOut].[NFCeXml]   FOR [NFCe_Out_XML_2016].[NFCeOut].[NFCeXml]
GO
 
DECLARE @max_pkey_2015 BIGINT;
SELECT  @max_pkey_2015 = MAX(pkey)
FROM
(
    SELECT MAX(pkey) as pkey FROM [NFCe_Out_XML_2015].[NFCeOut].[EventoXml] WHERE timestampReg < '2016-01-01'
    union all
    SELECT MAX(pkey) as pkey FROM [NFCe_Out_XML_2015].[NFCeOut].[NFCeXml] WHERE timestampReg < '2016-01-01'
    union all
    SELECT MAX(pkey) as pkey FROM [NFCe_Out_XML_2015].[NFCeOut].[InutXml] WHERE timestampReg < '2016-01-01'
) as ssss
DECLARE @min_pkey_2016 BIGINT = @max_pkey_2015 + 1
--SELECT @min_pkey_2016
 
INSERT INTO NFCe_Out.NFceOut.PeriodoPKeys
(ano, mes, MinPkeyDocumento, EmManutencao)
values
(2016, null, @min_pkey_2016, 0)
```
