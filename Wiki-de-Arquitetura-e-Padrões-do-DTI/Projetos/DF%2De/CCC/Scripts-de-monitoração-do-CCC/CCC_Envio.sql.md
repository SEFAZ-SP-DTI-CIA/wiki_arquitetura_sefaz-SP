
```
--=============================================================================================
--Acompanhamento genérico
--=============================================================================================
USE CCC
GO

SELECT '' AS ContribuinteAtualizacoesQueue, Count(1) AS ContribuinteAtualizacoesQueue FROM cccEnvio.ContribuinteAtualizacoesQueue (NOLOCK)
SELECT TOP 20 '' AS ContribuinteAtualizacoesQueue, flagRead, dataModificacao, *
  FROM [cccEnvio].[ContribuinteAtualizacoesQueue] (NOLOCK)
 ORDER BY pkey ASC

SELECT TOP 50 '' AS LogXML, timestampReg, *
  FROM [cccEnvio].[LogXML] (NOLOCK)
 ORDER BY 3 DESC

SELECT TOP 10 '' AS 'LogXML exceto erro 9320', timestampReg, *
 FROM [cccEnvio].[LogXML] (NOLOCK)
WHERE erro <> 
'Erro de validacao do tipo J: Mensagem no Web Service
cStat:9320
Rejeicao: Registro no CCC ja cadastrado pela UF e dados nao foram alterados'
ORDER BY 3 DESC

SELECT TOP 50 '' AS ContribuinteRejeitado, *
FROM [cccEnvio].[ContribuinteRejeitado] (NOLOCK)
ORDER BY 2 DESC

SELECT TOP 50 '' AS ContribuinteDeSP, c.datu, *
  FROM [ccc].[Contribuinte] c (NOLOCK)
 WHERE cUF = 35
ORDER BY 3 DESC

SELECT TOP 50 '' AS ContribuinteHistoricoDeSP, c.datu, *
  FROM [ccc].[ContribuinteHistorico] c (NOLOCK)
 WHERE cUF = 35
ORDER BY 3 DESC

SELECT '' AS '[DW].[Queue]', COUNT(1) FROM [DW].[Queue] (NOLOCK)
SELECT TOP 5 '' AS '[DW].[Queue]', * 
FROM [DW].[Queue] (NOLOCK)
ORDER BY timestampReg DESC
SELECT TOP 5 '' AS '[DW].[Queue]', * 
FROM [DW].[Queue] (NOLOCK)
ORDER BY timestampReg ASC

SELECT '' AS '[CCC].[ccc].[Configuracao]', servico, nome, valor, descricao, *
  FROM [CCC].[ccc].[Configuracao] c
INNER JOIN [CCC].[ccc].[ConfiguracaoTipo] ct
  ON c.pkeyConfiguracaoTipo = ct.pkey
WHERE servico = 'cccEnvio' OR servico = 'cccGeraFila'

GO
RETURN

--=============================================================================================
--Acompanhamento de envio de um CNPJ
--=============================================================================================

-- Contribuintes presos em produçao: 25076094000103, 23882834000173
-- Contribuintes presos em homologaçao: 24721096000145

DECLARE @CNPJ    BIGINT = 24721096000145
DECLARE @NR_CNPJ VARCHAR(14) = (SELECT FORMAT(@CNPJ, 'd14'))

--SELECT ID_SITUACAO_ESTABELECIMENTO, ID_OCORRENCIA_CADASTRAL, * 
--FROM CADESP.DBO.TB_ESTABELECIMENTO (NOLOCK)
--WHERE NR_CNPJ =  @NR_CNPJ

--SELECT DT_INICIO_TRANSACAO, ID_SITUACAO_ESTABELECIMENTO, ID_OCORRENCIA_CADASTRAL, * 
--FROM CADESP.DBO.TB_ESTABELECIMENTO_LOG L (NOLOCK)
--WHERE NR_CNPJ =  @NR_CNPJ
--ORDER BY L.DT_INICIO_TRANSACAO DESC

SELECT '' AS CCC_Contribuinte, datu, * FROM [CCC].[Contribuinte] (nolock)
WHERE InscMF = @CNPJ

SELECT '' AS CCC_ContribuinteHistorico, datu, * FROM [CCC].[ContribuinteHistorico] (nolock)
WHERE InscMF = @CNPJ

SELECT '' AS cccEnvio_ContribuinteAtualizacoesQueue, dataModificacao, flagRead, datu, *
FROM [cccEnvio].[ContribuinteAtualizacoesQueue] (NOLOCK)
WHERE COD_INSCR_MF = @CNPJ

DECLARE @RESTRICAO VARCHAR(16)  = ('%'+ CONVERT(VARCHAR,@CNPJ) +'%')
--SELECT @RESTRICAO

SELECT TOP 5 '' AS cccEnvio_ContribuinteRejeitado, *
FROM [cccEnvio].[ContribuinteRejeitado] (NOLOCK)
WHERE XML_Request LIKE @RESTRICAO
ORDER BY 2 DESC

SELECT TOP 10 '' AS LogXML, timestampReg, *
FROM [cccEnvio].[LogXML] (NOLOCK)
--WHERE erro NOT LIKE '%9320%'
WHERE TimestampReg > '2018-06-01'
 AND XML_Request LIKE @RESTRICAO
ORDER BY 3 DESC

SELECT TOP 10 * 
  FROM [CADESP].[CCC].[FILA_LOG] fl (nolock)
 INNER JOIN [CADESP].DBO.TB_ESTABELECIMENTO es (nolock)
    ON fl.ID_ESTABELECIMENTO = es.ID_ESTABELECIMENTO
 WHERE es.NR_CNPJ = @NR_CNPJ
 ORDER BY ID_FILA_LOG DESC

SELECT TOP 10 * 
  FROM [CADESP].[CCC].[FILA_FLUXO_ALTERNATIVO_LOG] fl (nolock)
 INNER JOIN [CADESP].DBO.TB_ESTABELECIMENTO es (nolock)
    ON fl.ID_ESTABELECIMENTO = es.ID_ESTABELECIMENTO
 WHERE es.NR_CNPJ = @NR_CNPJ
 ORDER BY pkey DESC

GO
RETURN

--=============================================================================================
--Acompanhamento de envio de um NSU
--=============================================================================================
DECLARE @NSUCCC BIGINT      = 3923598 --15868675
DECLARE @NSUCCCMOVTO BIGINT = 53648457 --53615899

SELECT 'CCC.Contribuinte', datu, indAtua, * FROM [CCC].[Contribuinte] (nolock)
WHERE NSUCCC      = @NSUCCC
  AND NSUCCCMOVTO = @NSUCCCMOVTO 

SELECT 'CCC.ContribuinteHistorico', datu, indAtuaEndereco, * FROM [CCC].[ContribuinteHistorico] (nolock)
WHERE NSUCCC      = @NSUCCC
  AND NSUCCCMOVTO = @NSUCCCMOVTO 

SELECT 'cccEnvio.ContribuinteAtualizacoesQueue', dataModificacao, flagRead, indAtuaEndereco, *
FROM [cccEnvio].[ContribuinteAtualizacoesQueue] (NOLOCK)
WHERE NSUInc = @NSUCCC
  AND NSUAtu = @NSUCCCMOVTO 

--=============================================================================================
--Atualização de config
--=============================================================================================
/*
USE [CCC]
GO

UPDATE [CCC].[ccc].[Configuracao]
   SET valor = 'true'
 WHERE 1=1
   AND servico = 'cccGeraFila'
   AND servico = 'cccEnvio'
   AND pkeyConfiguracaoTipo = (SELECT pkey FROM [CCC].[ccc].[ConfiguracaoTipo] where nome = 'FLUXO_ALTERNATIVO_READ_FLAG')
*/

/*
USE [CCC]
GO

UPDATE [CCC].[ccc].[Configuracao]
   SET valor = 'true'
   -- SELECT * FROM [CCC].[ccc].[Configuracao]
 WHERE pkey = (SELECT c.pkey
                 FROM      [CCC].[ccc].[Configuracao]     c  (nolock)
                INNER JOIN [CCC].[ccc].[ConfiguracaoTipo] ct (nolock)
                   ON c.pkeyConfiguracaoTipo = ct.pkey
                WHERE ct.nome   = 'FORCE_RESTART_FLAG'
                  AND c.servico = 'cccEnvio')

WAITFOR DELAY '00:00:10'

UPDATE [CCC].[ccc].[Configuracao]
   SET valor = '100'
   -- SELECT * FROM [CCC].[ccc].[Configuracao]
 WHERE pkey = (SELECT c.pkey
                 FROM      [CCC].[ccc].[Configuracao]     c  (nolock)
                INNER JOIN [CCC].[ccc].[ConfiguracaoTipo] ct (nolock)
                   ON c.pkeyConfiguracaoTipo = ct.pkey
                WHERE ct.nome   = 'GC_THREADSLEEP_TIME'
                  AND c.servico = 'cccEnvio')
*/
```
