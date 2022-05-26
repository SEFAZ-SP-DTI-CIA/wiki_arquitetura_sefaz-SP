# AcompanhamentoSQLEPEC


```
USE NFCe_EPEC_Dados
GO
 
SELECT TOP 100 * FROM [EPEC].[Certificado] (NOLOCK)
 
SELECT TOP 100 * FROM [EPEC].[DadosTransmissao] (NOLOCK)
 WHERE $Partition.PF_Part_01_Data(timestampReg) = $Partition.PF_Part_01_Data(GETDATE()) ORDER BY pKey DESC
 
SELECT TOP 100 * FROM [EPEC].[NFCe_Constraint_Conciliacao] (NOLOCK)
 WHERE $Partition.PF_Part_01_Data(timestampReg) = $Partition.PF_Part_01_Data(GETDATE()) ORDER BY timestampReg DESC
 
SELECT TOP 1000 * FROM [EPEC].[Protocolo] (NOLOCK)
 WHERE $Partition.PF_Part_01_Data(timestampReg) = $Partition.PF_Part_01_Data(GETDATE()) ORDER BY timestampReg DESC
 
SELECT TOP 1000 * FROM [EPEC].[ProtocoloRejeitado] (NOLOCK)
 WHERE $Partition.PF_Part_01_Data(timestampReg) = $Partition.PF_Part_01_Data(GETDATE()) ORDER BY timestampReg DESC
 
SELECT TOP 100 * FROM [EPEC].[ConciliacaoPendente] (NOLOCK)
 
SELECT TOP 100 * FROM [EPEC].[LogEPEC] (NOLOCK)
SELECT TOP 100 * FROM [EPEC].[RegistroLogEPEC] (NOLOCK)
 
SELECT TOP 100 * FROM [EPEC].[AgendamentoAtivacao] (NOLOCK)
 
SELECT TOP 100 * FROM [EPEC].[HistoricoOperacoesServicoEPEC] (NOLOCK)
 
SELECT TOP 100 * FROM [EPECAdmin].[HistoricoEPECsDesbloquedas] (NOLOCK)
 
SELECT * FROM [EPEC].[TipoLogEPEC] (NOLOCK)
SELECT * FROM [EPEC].[TipoRegistroLogEPEC] (NOLOCK)
SELECT * FROM [EPEC].[TipoStatusAgendamentoAtivacao] (NOLOCK)
 
SELECT TOP 200 * FROM [EPEC].[ValidacaoCertificado] (NOLOCK) ORDER BY id   DESC
 
RETURN
 
--========================================================================================
USE NFCe_EPEC_Dados
GO
 
-- Log com registros sem erros
SELECT top 100 TL.Descricao, TR.Descricao, L.pkey as 'pKey Log', RL.Valor, L.*, RL.*
  FROM [EPEC].[LogEPEC] L (nolock)
  INNER JOIN [EPEC].[TipoLogEPEC] TL (nolock)
  ON L.idTipoLogEPEC = TL.Id
  LEFT JOIN [EPEC].[RegistroLogEPEC] RL (nolock)
  ON RL.fkLogEPEC = L.pKey
  LEFT JOIN [EPEC].[TipoRegistroLogEPEC] TR (nolock)
  ON RL.IdTipoRegistroLogEPEC = TR.Id
--WHERE tl.descricao = 'Lista de Contribuintes Bloqueados Atualizada'
WHERE TL.descricao <> 'Rotina de Concilia褯 de EPECs executada'
--   AND TL.Id >= 0
ORDER BY L.pKey DESC
 
-- Log com erros
SELECT top 300 L.timestampReg, TL.Descricao, TR.Descricao, L.pkey as 'pKey Log', RL.Valor, L.*, RL.*
  FROM [EPEC].[LogEPEC] L (nolock)
  INNER JOIN [EPEC].[TipoLogEPEC] TL (nolock)
  ON L.idTipoLogEPEC = TL.Id
  LEFT JOIN [EPEC].[RegistroLogEPEC] RL (nolock)
  ON RL.fkLogEPEC = L.pKey
  LEFT JOIN [EPEC].[TipoRegistroLogEPEC] TR (nolock)
  ON RL.IdTipoRegistroLogEPEC = TR.Id
WHERE TL.id < 0
  --AND TL.descricao  != 'Exce褯 em Servi诠de Concilia褯 EPEC'
ORDER BY L.pKey DESC
 
RETURN
--========================================================================================
--========================================================================================
--========================================================================================
--========================================================================================
USE NFCe_EPEC_CONSTRAINT
GO
 
SELECT TOP 100 * FROM [EPEC].[ConfigSistema] (NOLOCK)
SELECT TOP 100 * FROM [EPEC].[ConfigSistema_LOG] (NOLOCK) order by pKeyLog DESC
 
SELECT TOP 100 * FROM [EPEC].[ContribuintesBloqueados_01] (NOLOCK)
SELECT TOP 100 * FROM [EPEC].[ContribuintesBloqueados_02] (NOLOCK)
SELECT TOP 100 * FROM [EPEC].[ContribuintesBloqueados] (NOLOCK)
 
SELECT TOP 1000 * FROM [EPEC].[Dados_EPEC] (NOLOCK)
 WHERE $Partition.PF_Part_01_Data(timestampReg) = $Partition.PF_Part_01_Data(GETDATE()) ORDER BY pKey DESC
 
SELECT TOP 1000 * FROM [EPEC].[EPEC_Constraint] (NOLOCK)
 WHERE $Partition.PF_Part_01_Data(timestampReg) = $Partition.PF_Part_01_Data(GETDATE()) ORDER BY pKey DESC
 
SELECT TOP 100 * FROM [EPEC].[TipoDestinatario] (NOLOCK)
 
RETURN
--========================================================================================
--========================================================================================
--========================================================================================
--========================================================================================
USE NFCe_EPEC_XML
GO
 
SELECT TOP 100 * FROM [EPEC].[Certificado] (NOLOCK)
 WHERE $Partition.PF_Part_01_Data(timestampReg) = $Partition.PF_Part_01_Data(GETDATE()) ORDER BY pKey DESC
 
SELECT TOP 100 *, EPEC.UdfDecompressNFCe(evento) FROM [EPEC].[EventoXml] (NOLOCK)
 WHERE $Partition.PF_Part_01_Data(timestampReg) = $Partition.PF_Part_01_Data(GETDATE()) ORDER BY pKey DESC
 
SELECT TOP 100 * FROM [EPEC].[LoteXml_01] (NOLOCK)
 WHERE $Partition.PF_Part_01_Data(recebimento) = $Partition.PF_Part_01_Data(GETDATE()) ORDER BY pKey DESC
 
SELECT TOP 100 * FROM [EPEC].[LoteXml_02] (NOLOCK)
 WHERE $Partition.PF_Part_01_Data(recebimento) = $Partition.PF_Part_01_Data(GETDATE()) ORDER BY pKey DESC
 
 SELECT TOP 100 * FROM [EPEC].[LoteXml] (NOLOCK)
 WHERE $Partition.PF_Part_01_Data(recebimento) = $Partition.PF_Part_01_Data(GETDATE()) ORDER BY pKey DESC
 
--========================================================================================
--========================================================================================
--========================================================================================
--========================================================================================
 
-- Query para pegar dados de EPECs não conciliados
 
SELECT const.cnpjEmit, const.numero, const.serie, convert(varchar, dados.dhEmi, 126) as 'dmEmi formatado', dados.*
  FROM [NFCe_EPEC_Dados].[EPEC].[ConciliacaoPendente] pend
  JOIN [NFCe_EPEC_Constraint].[EPEC].[EPEC_Constraint] const
  ON const.pKey = pend.pKey
  JOIN [NFCe_EPEC_Constraint].[EPEC].[Dados_EPEC] dados
  ON dados.pKey = pend.pKey
  WHERE const.cnpjEmit = '01215911000107'
  order by pend.pkey
```
