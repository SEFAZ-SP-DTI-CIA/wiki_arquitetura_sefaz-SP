## Consultas SQL para relatórios, testes e depuração

**1) Relatório de Status de Homologação**

[RelatorioStatusNFCeEPECHomologacao](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/331/Relat%C3%B3rio-Status-NFC-e-EPEC-Homologa%C3%A7%C3%A3o)

**2) Consulta Protocolos recebidos por minuto nas últimas 24 horas, colocando ZERO nos minutos sem Protocolos** 
 

```
DECLARE @startdate smalldatetime, @enddate smalldatetime
SET @StartDate = CAST(getdate()-1 as smalldatetime);
SET @EndDate = CAST(getdate() as smalldatetime);
;with cte as
(
select @startdate DateValue
union all
select DATEADD (mi,1,DateValue)
from cte 
where DATEADD (mi,1,DateValue) < @enddate
)
select a.DateValue as minuto, COUNT(b.pKey) AS conta
from cte a
LEFT outer join NFCe_Out.NFCeOut.Protocolo b 
on a.DateValue = cast(b.timestampReg as smalldatetime)
group by a.DateValue
order by a.DateValue desc
OPTION (MAXRECURSION 0, maxdop 4)
```



**3) Consulta Lotes Recebidos/seg (usada após Teste de Carga)**
 

```
SELECT cast(DATEPART(hour, recebimento) as varchar) + ':' +
       cast(DATEPART(MINUTE, recebimento) as varchar) +':'+ 
       cast(DATEPART(SECOND, recebimento) as varchar) Tempo
      ,count(1) Notas_Autorizadas
FROM [NFCe_Teste_Carga_In].[NFCeIn].[Lote]
where recebimento between '2014-05-06 15:00' and '2014-05-06 23:00'
  and cnpj <> '12345678901234'
group by DATEPART(hour, recebimento)
        ,DATEPART(MINUTE, recebimento) 
        ,DATEPART(SECOND, recebimento)
order by DATEPART(hour, recebimento)
        ,DATEPART(MINUTE, recebimento) 
        ,DATEPART(SECOND, recebimento)
```


 
**4) Consulta Notas Processadas/seg (usada após Teste de Carga)**


```
SELECT cast(DATEPART(hour, timestampReg) as varchar) + ':' +
       cast(DATEPART(MINUTE, timestampReg) as varchar) +':'+ 
       cast(DATEPART(SECOND, timestampReg) as varchar) Tempo
      ,count(1) Notas_Autorizadas
FROM [NFCe_Teste_Carga_Out_Constraint].[NFCeOut].[NFCe_Constraint]
where timestampReg > '2014-04-29 18:30'
  and cnpjEmit <> '12345678901234'
group by DATEPART(hour, timestampReg)
        ,DATEPART(MINUTE, timestampReg) 
        ,DATEPART(SECOND, timestampReg)
order by DATEPART(hour, timestampReg)
        ,DATEPART(MINUTE, timestampReg) 
        ,DATEPART(SECOND, timestampReg)
```


**5) Consulta XML Notas por CNPJ**

```
SELECT [NFCe_Out_XML].[NFCeOut].UdfDecompressNFCe(xmle.[NFCe])
      ,xmle.[timestampR​eg]
  FROM [NFCe_Out_Constraint].[NFCeOut].[NFCe_Constraint] const
  JOIN [NFCe_Out_XML].[NFCeOut].[NFCeXml] xmle
  ON xmle.pKey = const.fkProtocolo
  WHERE const.cnpjEmit = '<CNPJ>'​
```


**6) Inserindo novos contribuintes**


```
USE [NFCe_Out_Constraint]
GO
INSERT INTO [NFCe_Out_Constraint].[NFCeOut].[ContribuintesHabilitados]
([CNPJ], [IE], [Empresa], [dataDeHabilitacao], [dataCredenciamento], [UF], [valorNFeIlimitado], ignorarCadesp, tipoCredContribuinte, tipoEmissaoContribuinte, credVoluntarioHabilitado, CD_DRT) 
VALUES 
    (N'00000000000000', N'000000000000', N'yyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyy', GETDATE(), GETDATE(), 35, 0, 0, 2, 1, 1, 0)
-- ,(N'11111111111111', N'111111111111', N'xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx', GETDATE(), GETDATE(), 35, 0, 0, 2, 1, 1, 0)
```


 

**7) Deletando milhões de entradas sem travar o MGTStudio​**


```
DECLARE @a INT = 0
WHILE EXISTS (SELECT TOP 1 pKey FROM [EPEC].[Dados_EPEC] WHERE ieEmit = '111111111111')
BEGIN
    DELETE TOP (1000000) 
    FROM [EPEC].[Dados_EPEC]
    WHERE ieEmit = '111111111111'

    PRINT @a
    SET  @a = @a + 1
END
```
