# AcompanhamentoSqlBatimento


```
USE [NFCe_Out]
GO

DECLARE @mesSelecionado datetime = '2018-01-01'
DECLARE @mesSelecionadoFim datetime = '2018-02-01'

--SELECT chNFCe
--  FROM [NFCe_Out].[NFCeOut].[NFCe] n
--  INNER JOIN [NFCe_Out_Constraint].[NFCeOut].[NFCe_Constraint] c
--  ON c.fkProtocolo = n.pKey
--  AND c.cStat <> 301
--  WHERE $Partition.PF_Part_01_Data(n.timestampReg) = $Partition.PF_Part_01_Data(@mesSelecionado)
--  AND n.timestampReg between @mesSelecionado and @mesSelecionadoFim
--  ORDER BY chNFCe asc

SELECT LEFT(FORMAT(count(1), '#,#', 'pt-br'), 20) as 'Total NFC-e'
  FROM [NFCe_Out].[NFCeOut].[NFCe] n --WITH (NOLOCK)      
  INNER JOIN [NFCe_Out_Constraint].[NFCeOut].[NFCe_Constraint] c  --WITH (NOLOCK)
  ON c.fkProtocolo = n.pKey
  AND c.cStat <> 301
  WHERE $Partition.PF_Part_01_Data(n.timestampReg) = $Partition.PF_Part_01_Data(@mesSelecionado)
  AND n.timestampReg between @mesSelecionado and @mesSelecionadoFim
 
SELECT LEFT(FORMAT(count(1), '#,#', 'pt-br'), 20) as 'Total cancelamento de notas emitidas naquele mês'
  FROM [NFCe_Out].[NFCeOut].NFCe nfce  --WITH (NOLOCK)
  INNER JOIN [NFCe_Out_Constraint].[NFCeOut].[Evento_Constraint] ec  --WITH (NOLOCK)
  ON nfce.chNFCe = ec.chNFCe
  WHERE $Partition.PF_Part_01_Data(nfce.timestampReg) = $Partition.PF_Part_01_Data(@mesSelecionado)
  AND nfce.timestampReg between @mesSelecionado and @mesSelecionadoFim
  OPTION(MAXDOP 2)

SELECT LEFT(FORMAT(count(1), '#,#', 'pt-br'), 20) as 'Total NFC-e com IdDestinatario'
  FROM [NFCe_Out].[NFCeOut].[NFCe] n       --WITH (NOLOCK)
  INNER JOIN[NFCe_Out].[NFCeOut].[DadosApoio]  d  --WITH (NOLOCK)
  ON n.pKey = d.pKey
  INNER JOIN [NFCe_Out_Constraint].[NFCeOut].[NFCe_Constraint] c  --WITH (NOLOCK)
  ON c.fkProtocolo = n.pKey
  AND c.cStat <> 301
  WHERE $Partition.PF_Part_01_Data(n.timestampReg) = $Partition.PF_Part_01_Data(@mesSelecionado)
  AND n.timestampReg between @mesSelecionado and @mesSelecionadoFim
  AND d.idDestinatario IS NOT NULL
  AND d.tipoDestinatario <> 3
  OPTION(MAXDOP 2)

SELECT LEFT(FORMAT(count(1), '#,#', 'pt-br'), 20) as 'Total cancelamento de notas emitidas naquele mês com IdDestinatário'
  FROM [NFCe_Out].[NFCeOut].NFCe nfce  WITH (NOLOCK)
  INNER JOIN [NFCe_Out_Constraint].[NFCeOut].[Evento_Constraint] ec  --WITH (NOLOCK)
  ON nfce.chNFCe = ec.chNFCe
  INNER JOIN[NFCe_Out].[NFCeOut].[DadosApoio]  d  --WITH (NOLOCK)​
  on d.pKey = nfce.pKey
  WHERE $Partition.PF_Part_01_Data(nfce.timestampReg) = $Partition.PF_Part_01_Data(@mesSelecionado)
  AND nfce.timestampReg between @mesSelecionado and @mesSelecionadoFim
  AND d.idDestinatario IS NOT NULL
  AND d.tipoDestinatario <> 3
  OPTION(MAXDOP 2)

RETURN
```
