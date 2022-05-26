
```
USE CCC
GO

SELECT '' AS '[ccc].[Configuracao]', servico, nome, valor, descricao, *
  FROM [ccc].[Configuracao] c
INNER JOIN [CCC].[ccc].[ConfiguracaoTipo] ct
   ON c.pkeyConfiguracaoTipo = ct.pkey
 WHERE servico = 'cccRecebimento' -- 76108097
   AND nome = 'NSUCCC_INICIAL'

-----
SELECT TOP 30 '' AS LogXML_Queue, COUNT(1) AS NroRegistros
  FROM [cccRecebimento].[LogXML_Queue] (nolock)

SELECT TOP 30 '' AS LogXML_Queue, *
  FROM [cccRecebimento].[LogXML_Queue] (nolock)
  ORDER BY pkeyLogXML DESC
-----
SELECT TOP 30 '' AS LogXML, COUNT(1) AS NroRegistros
  FROM [cccRecebimento].[LogXML] (nolock)

SELECT TOP 30 '' AS LogXML, *, CONVERT(XML, XML_Request), CONVERT(XML, XML_Response)
  FROM [cccRecebimento].[LogXML] (nolock)
  ORDER BY TimestampReg DESC
-----
SELECT TOP 30 '' AS RegistrosLoteErro, COUNT(1) AS NroRegistros
  FROM [cccRecebimento].[RegistrosLoteErro] (nolock)

SELECT TOP 30 '' AS RegistrosLoteErro, *
  FROM [cccRecebimento].[RegistrosLoteErro] (nolock)
 ORDER BY pkey DESC
-----
SELECT TOP 50 '' AS ContribuinteForaDeSP, c.datu, indAtua, *
  FROM [ccc].[Contribuinte] c (NOLOCK)
 WHERE cUF != 35
 ORDER BY 4 DESC
-----
SELECT TOP 50 '' AS ContribuinteHistoricoForaDeSP, c.datu, indAtua, *
  FROM [ccc].[ContribuinteHistorico] c (NOLOCK)
 WHERE cUF != 35
 ORDER BY 4 DESC
----
SELECT TOP 5 '' AS '[DW].[Queue]', * 
  FROM [DW].[Queue] (NOLOCK)
 ORDER BY timestampReg DESC
SELECT '' AS '[DW].[Queue]', COUNT(1) AS NroRegistros 
  FROM [DW].[Queue] (NOLOCK)
----
SELECT '' AS '[ccc].[Configuracao]', servico, nome, valor, descricao, *
  FROM [ccc].[Configuracao] c
INNER JOIN [CCC].[ccc].[ConfiguracaoTipo] ct
   ON c.pkeyConfiguracaoTipo = ct.pkey
 WHERE servico = 'cccRecebimento'

 GO
 RETURN

----------------------------------------------------------------------------------

---- Procura de contribuinte por CNPJ
DECLARE @CNPJ    BIGINT = 28995718000184
DECLARE @NR_CNPJ VARCHAR(14) = (SELECT FORMAT(@CNPJ, 'd14'))

SELECT '' AS CCC_Contribuinte, datu, * FROM [CCC].[Contribuinte] (nolock)
WHERE InscMF = @CNPJ

SELECT '' AS CCC_ContribuinteHistorico, datu, * FROM [CCC].[ContribuinteHistorico] (nolock)
WHERE InscMF = @CNPJ


---- Procura de contribuinte por NSUCCC
DECLARE @NSUCCC INT = 16163714

SELECT '' AS CCC_Contribuinte, datu, * FROM [CCC].[Contribuinte] (nolock)
WHERE NSUCCC = @NSUCCC

SELECT '' AS CCC_ContribuinteHistorico, datu, * FROM [CCC].[ContribuinteHistorico] (nolock)
WHERE NSUCCC = @NSUCCC

----
 /* Reprocessamento
 INSERT INTO [cccRecebimento].[LogXML_Queue]
           ([pkeyLogXML]           
           ,[dataModificacao])
	
    SELECT [pkeyLogXML]
		    ,GETDATE()
      FROM [cccRecebimento].[RegistrosLoteErro]
      WHERE ....
 */
```

