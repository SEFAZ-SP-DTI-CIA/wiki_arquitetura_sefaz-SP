# AcompanhamentoSQLNFCe


```
USE NFCe_In
 
GO
  
SELECT * FROM [NFCeIn].[FilaProcessamento] (NOLOCK)
SELECT * FROM [NFCeIn].[Status]
SELECT * FROM [NFCeIn].[TempoMedioAutorizacao]
 
SELECT TOP 100 *, [NFCeIn].[UdfDecompressNFCe](mensagemBin), [NFCeIn].[UdfDecompressNFCe](cabecalhoBin) FROM [NFCeIn].[LoteStaging]    (NOLOCK) ORDER BY pKey DESC  --[NFCe_In].[NFCeIn].[usp_LoteInsert]
SELECT TOP 100 *, [NFCeIn].[UdfDecompressNFCe](mensagemBin), [NFCeIn].[UdfDecompressNFCe](cabecalhoBin) FROM [NFCeIn].[LoteStaging_01] (NOLOCK) ORDER BY pKey DESC  --[NFCe_In].[NFCeIn].[usp_LoteInsert]
SELECT TOP 100 *, [NFCeIn].[UdfDecompressNFCe](mensagemBin), [NFCeIn].[UdfDecompressNFCe](cabecalhoBin) FROM [NFCeIn].[LoteStaging_02] (NOLOCK) ORDER BY pKey DESC  --[NFCe_In].[NFCeIn].[usp_LoteInsert]
 
SELECT TOP 100 * FROM [NFCeIn].[LoteStaging]    (NOLOCK) ORDER BY pKey DESC   --[NFCe_In].[NFCeIn].[usp_LoteInsert]
SELECT TOP 100 * FROM [NFCeIn].[LoteStaging_01] (NOLOCK) ORDER BY pKey DESC   --[NFCe_In].[NFCeIn].[usp_LoteInsert]
SELECT TOP 100 * FROM [NFCeIn].[LoteStaging_02] (NOLOCK) ORDER BY pKey DESC   --[NFCe_In].[NFCeIn].[usp_LoteInsert]
 
SELECT TOP 100 * FROM [NFCeIn].[ProcessamentoLog] (NOLOCK) ORDER BY pKey DESC -- truncada todo sábado 0h
 
SELECT TOP 100 * FROM [NFCeIn].[Lote] (NOLOCK)                                              --[NFCe_In].[NFCeIn].[usp_LoteInsert]
WHERE $Partition.PF_Part_01_Data(recebimento) = $Partition.PF_Part_01_Data(GETDATE())
ORDER BY pKey DESC
 
SELECT TOP 100 * FROM [NFCeIn].[LoteProcessamento] (NOLOCK)                                 --[NFCe_In].[NFCeIn].[usp_FilaProcessamentoSelect]
WHERE $Partition.PF_Part_01_Data(recebimento) = $Partition.PF_Part_01_Data(GETDATE())
ORDER BY pKey DESC
 
SELECT TOP 100 * FROM [NFCeIn].[LoteSinc] (NOLOCK)                                          --[NFCe_In].[NFCeIn].[usp_LoteSincInsert]
WHERE $Partition.PF_Part_01_Data(processamento) = $Partition.PF_Part_01_Data(GETDATE())
ORDER BY pKey DESC
 
SELECT TOP 100 l.recebimento, l.CNPJ,l.tempoRecebimento, lp.tempoProcessamento,
               l.tempoRecebimento + lp.tempoProcessamento as 'tempoAutorizacao calculado', lp.cStat
FROM [NFCeIn].[Lote] l (NOLOCK)                                                         --[NFCe_In].[NFCeIn].[usp_LoteInsert]
INNER JOIN [NFCeIn].[LoteProcessamento] lp (NOLOCK)
ON  l.pKey = lp.pKey
AND l.recebimento = lp.recebimento
WHERE $Partition.PF_Part_01_Data(l.recebimento) = $Partition.PF_Part_01_Data(GETDATE())
ORDER BY l.pKey DESC
 
RETURN
--========================================================================================
--========================================================================================
--========================================================================================
--========================================================================================
USE NFCe_In_XML
GO
 
SELECT TOP 100 *
FROM [NFCeIn].[LoteCompl] (NOLOCK)                                                          --[NFCe_In].[NFCeIn].[usp_LoteInsert]
WHERE $Partition.PF_Part_01_Data(recebimento) = $Partition.PF_Part_01_Data(GETDATE())
ORDER BY pKey DESC
 
SELECT TOP 100 *, [NFCeIn].[UdfDecompressNFCe](mensagemBin), [NFCeIn].[UdfDecompressNFCe](cabecalhoBin)
FROM [NFCeIn].[LoteXML] (NOLOCK)                                                            --[NFCeIn].[usp_ChaveamentoStagingArmazenamentoXML_JOB]
WHERE $Partition.PF_Part_01_Data(recebimento) = $Partition.PF_Part_01_Data(GETDATE())
ORDER BY pKey DESC
 
SELECT TOP 100 *, [NFCeIn].[UdfDecompressNFCe](mensagemBin), [NFCeIn].[UdfDecompressNFCe](cabecalhoBin)
FROM [NFCeIn].[LoteSincXML] (NOLOCK)                                                        --[NFCe_In].[NFCeIn].[usp_LoteSincInsert]
WHERE $Partition.PF_Part_01_Data(processamento) = $Partition.PF_Part_01_Data(GETDATE())
ORDER BY pKey DESC
 
SELECT TOP 100 * FROM [NFCeIn].[LoteEventoXML] (NOLOCK)                                     -- [NFCeIn].[usp_LoteEventoInsert]
WHERE $Partition.PF_Part_01_Data(recebimento) = $Partition.PF_Part_01_Data(GETDATE())
ORDER BY pKey DESC
 
RETURN
--========================================================================================
--========================================================================================
--========================================================================================
--========================================================================================
USE NFCe_Out
GO
 
SELECT TOP 100 * FROM [NFCeAdmin].[ResponsaveisEstabelecimentos] (NOLOCK) ORDER BY pKey DESC
SELECT TOP 100 * FROM [NFCeAdmin].[HistoricoCancExtemporaneo]    (NOLOCK) ORDER BY pKey DESC
SELECT TOP 100 * FROM [NFCeAdmin].[HistoricoCredenciamento]      (NOLOCK) ORDER BY pKey DESC
SELECT TOP 100 * FROM [NFCeAdmin].[CredenciamentoVoluntarioCNAE] (NOLOCK) ORDER BY pKey DESC
SELECT TOP 100 * FROM [NFCeAdmin].[AdministraPaginas]            (NOLOCK) ORDER BY pKey DESC
SELECT TOP 100 * FROM [NFCeAdmin].[ArquivosPortal]               (NOLOCK) ORDER BY pKey DESC
 
SELECT TOP 100 * FROM [NFCeLog].[LogExcecao]      (NOLOCK) ORDER BY pKey DESC
SELECT TOP 100 * FROM [NFCeLog].[ConsultaPublica] (NOLOCK) ORDER BY pKey DESC -- 0=Página 1=QR-Code
SELECT TOP 100 * FROM [NFCeLog].[CadespConsulta]  (NOLOCK) ORDER BY id   DESC
SELECT TOP 100 * FROM [NFCeLog].[UsoIndevido]     (NOLOCK) ORDER BY id   DESC -- Truncado todo primeiro dia do mes, 1h5' + toda Quinta-Feira 01:00h - mantendo no mínimo 1000 registros
SELECT         * FROM [NFCeLog].[VerificacaoSAT]  (NOLOCK)
 
SELECT TOP 200 timestampReg, * FROM [NFCeLog].[ValidacaoCertificado] (NOLOCK) ORDER BY id   DESC -- Truncado todo primeiro dia do mes, 1h5' + toda Quinta-Feira 01:00h - mantendo no mínimo 1000 registros
SELECT TOP 200 timestampReg, hostname, tempoValidacaoCertificado, acEmissora FROM [NFCeLog].[ValidacaoCertificado] (NOLOCK)
       WHERE tempoValidacaoCertificado >= 15 ORDER BY id   DESC
 
SELECT TOP 100 * FROM [NFCeLogAudit].[LogAcesso]    (NOLOCK) ORDER BY pKey DESC
SELECT TOP 100 * FROM [NFCeLogAudit].[RegistroLog]  (NOLOCK) ORDER BY pKey DESC
 
SELECT * FROM [NFCeLog].[ComponenteLogExcecao] (NOLOCK)
SELECT * FROM [NFCeLogAudit].[TipoAcaoLog]     (NOLOCK)
SELECT * FROM [NFCeLogAudit].[TipoRegistroLog] (NOLOCK)
 
SELECT TOP 100 * FROM [LCR].[Certificado]        (NOLOCK) ORDER BY pKey DESC
SELECT TOP 100 * FROM [LCR].[LCRQueue]           (NOLOCK) ORDER BY pkeyListaRevogados DESC
SELECT TOP 100 * FROM [LCR].[ListaRevogados]     (NOLOCK) ORDER BY pKey DESC
SELECT TOP 100 * FROM [LCR].[PontosDistribuicao] (NOLOCK) ORDER BY pkeyListaRevogados DESC
SELECT TOP 100 * FROM [LCR].[Revogados]          (NOLOCK) ORDER BY pkeyListaRevogados DESC
 
SELECT * FROM [NFCeOut].[CodMunIBGE]   (NOLOCK)
SELECT * FROM [NFCeOut].[CodNCM]       (NOLOCK)
SELECT * FROM [NFCeOut].[PeriodoPKeys] (NOLOCK)
 
SELECT TOP 100 * FROM [NFCeOut].[SondaMonitoracaoSefaz] (NOLOCK) ORDER BY pKey DESC
SELECT TOP 100 * FROM [NFCeOut].[ViewConsultaInut]      (NOLOCK) ORDER BY pKey DESC
SELECT TOP 100 * FROM [NFCeOut].[Certificado]           (NOLOCK) ORDER BY pKey DESC
 
SELECT TOP 100 * FROM [NFCeOut].[DadosApoio]       (NOLOCK)
  WHERE $Partition.PF_Part_01_Data(timestampReg) = $Partition.PF_Part_01_Data(GETDATE()) ORDER BY pKey DESC
 
SELECT TOP 100 * FROM [NFCeOut].[DadosTransmissao] (NOLOCK)
  WHERE $Partition.PF_Part_01_Data(timestampReg) = $Partition.PF_Part_01_Data(GETDATE()) ORDER BY pKey DESC
 
SELECT TOP 100 * FROM [NFCeOut].[Evento]           (NOLOCK)  
  WHERE $Partition.PF_Part_01_Data(timestampReg) = $Partition.PF_Part_01_Data(GETDATE()) ORDER BY pKey DESC
 
SELECT TOP 100 * FROM [NFCeOut].[NFCe]             (NOLOCK)
  WHERE $Partition.PF_Part_01_Data(timestampReg) = $Partition.PF_Part_01_Data(GETDATE()) ORDER BY pKey DESC
 
SELECT TOP 100 * FROM [NFCeOut].[Protocolo]        (NOLOCK)
  WHERE $Partition.PF_Part_01_Data(timestampReg) = $Partition.PF_Part_01_Data(GETDATE()) ORDER BY pKey DESC
 
SELECT TOP 100 * FROM [NFCeOut].[ProtocoloRejeitado] (NOLOCK)
  WHERE $Partition.PF_Part_01_Data(timestampReg) = $Partition.PF_Part_01_Data(GETDATE()) ORDER BY pKey DESC
 
SELECT TOP 100 * FROM [NFCeOut].InutNFCe              (NOLOCK)
  WHERE $Partition.PF_Part_01_Data(timestampReg) = $Partition.PF_Part_01_Data(GETDATE()) ORDER BY pKey DESC
 
SELECT TOP 100 * FROM [NFCeOut].[TokenContribuinte] (NOLOCK) ORDER BY pkey DESC
SELECT TOP 100 * FROM [NFCeOut].[CancExtemporaneo]  (NOLOCK) ORDER BY pkey DESC
 
SELECT * FROM [NFCeOut].[TipoDestinatario]
SELECT * FROM [NFCeOut].[TpDocumento]
 
SELECT TOP 100 * FROM [NFCeOut].[CancExtemporaneo_LOG]  (NOLOCK) ORDER BY pkey DESC
SELECT TOP 100 * FROM [NFCeOut].[TokenContribuinte_LOG] (NOLOCK) ORDER BY pkey DESC
 
RETURN
------------------------------------------------------------------------------------------
USE NFCe_Out
GO
-- Log com registros
SELECT top 200 L.timestampOffsetReg, TL.Descricao AS 'Descriçao Log', TR.Descricao AS 'Descriçao Registro',
               L.pkey as 'pKey Log', L.IdSistema, RL.Valor, ' ', L.*, RL.*
  FROM [NFCeLogAudit].[LogAcesso] L (NOLOCK)
  INNER JOIN [NFCeLogAudit].[TipoAcaoLog] TL (NOLOCK)
  ON L.IdTipoAcao = TL.Id
  LEFT JOIN [NFCeLogAudit].[RegistroLog] RL (NOLOCK)
  ON RL.IdLogAcesso = L.pKey
  LEFT JOIN [NFCeLogAudit].[TipoRegistroLog] TR (NOLOCK)
  ON RL.IdTipoRegistroLog = TR.Id
  --WHERE tl.Descricao = 'Salvar HTML da pagina home Portal'
  --WHERE TL.Id != 1
  --WHERE IdUsuario LIKE '57484768%'
  --WHERE L.pkey = 56485
  --WHERE RL.Valor LIKE '%Invalid viewstate%'
    --AND RL.Valor not like '%35150738787057000155650010000014611551854890%'
  order by L.pkey desc
 
-- Log tipo Erro com registros
SELECT top 100 L.timestampOffsetReg, TL.Descricao AS 'Descriçao Log', TR.Descricao AS 'Descriçao Registro',
               L.pkey as 'pKey Log', L.IdSistema, RL.Valor, ' ', L.*, RL.*
  FROM [NFCeLogAudit].[LogAcesso] L (NOLOCK)
  INNER JOIN [NFCeLogAudit].[TipoAcaoLog] TL (NOLOCK)
  ON L.IdTipoAcao = TL.Id
  LEFT JOIN [NFCeLogAudit].[RegistroLog] RL (NOLOCK)
  ON RL.IdLogAcesso = L.pKey
  LEFT JOIN [NFCeLogAudit].[TipoRegistroLog] TR (NOLOCK)
  ON RL.IdTipoRegistroLog = TR.Id
  WHERE TL.Id = 1
  order by L.pkey desc
 
-- Exceções
SELECT TOP 100 LE.timestampReg, LE.pKey, CE.Descricao, LE.mensagem, *
  FROM [NFCeLog].[LogExcecao] LE (NOLOCK)
  INNER JOIN [NFCeLog].[ComponenteLogExcecao] CE (NOLOCK)
  ON LE.componente = CE.Id
  ORDER BY LE.timestampReg DESC
 
-- Erros de acesso ao CADESP
SELECT TOP 100 timestampReg, *
  FROM [NFCeLog].[CadespConsulta] C (NOLOCK)
  ORDER BY id DESC
 
RETURN
--========================================================================================
--========================================================================================
--========================================================================================
--========================================================================================
USE NFCe_Out_Constraint
GO
 
SELECT         * FROM [NFCeOut].[ConfigSistema]      (NOLOCK)
SELECT TOP 100 * FROM [NFCeOut].[ConfigSistema_LOG]  (NOLOCK) ORDER BY pKeyLog DESC
 
SELECT         * FROM [NFCeOut].[ContribuintesHabilitados]     (NOLOCK)
SELECT TOP 100 * FROM [NFCeOut].[ContribuintesHabilitados_LOG] (NOLOCK) ORDER BY pKeyLog DESC
 
SELECT TOP 100 * FROM [NFCeOut].[Evento_Constraint] (NOLOCK) ORDER BY timestampReg DESC
SELECT TOP 100 * FROM [NFCeOut].[NFCe_Constraint]   (NOLOCK) ORDER BY timestampReg DESC
 
SELECT * FROM [NFCeOut].[TipoCredContribuinte]
SELECT * FROM [NFCeOut].[TipoEmissaoContribuinte]
 
------------------------------------------------------------------------------------------
 
-- Contribuintes Habilitados
SELECT ch.pKey, ch.CNPJ, ch.ie, ch.Empresa, tcc.descricao AS 'Credenciamento', tec.descricao 'Emissao',
       ch.dataCredenciamento, ch.credVoluntarioHabilitado, ch.datadehabilitacao '---', *
      FROM [NFCeOut].[ContribuintesHabilitados] ch (NOLOCK)
INNER JOIN [NFCeOut].[TipoCredContribuinte] tcc    (NOLOCK)
   ON tcc.[TipoCredContribuinte] = ch.tipoCredContribuinte
INNER JOIN [NFCeOut].[TipoEmissaoContribuinte] tec (NOLOCK)
   ON tec.[TipoEmissaoContribuinte] = ch.tipoEmissaoContribuinte
--WHERE ch.CNPJ IN ('07188628000101','00623904000688','61149506004220','66970229020001')
--WHERE ch.CD_DRT = 0
--ORDER BY ch.timestampUpd desc
ORDER BY ch.timestampUpd DESC
 
-- Contribuintes e responsáveis
SELECT ch.pKey, ch.CNPJ, ch.ie, ch.Empresa, tcc.descricao AS 'Credenciamento', tec.descricao 'Emissao',
       ch.dataCredenciamento, ch.credVoluntarioHabilitado, ch.datadehabilitacao, re.nome, email, telefone, '---', *
      FROM [NFCeOut].[ContribuintesHabilitados] ch (NOLOCK)
INNER JOIN [NFCeOut].[TipoCredContribuinte] tcc    (NOLOCK)
   ON tcc.[TipoCredContribuinte] = ch.tipoCredContribuinte
INNER JOIN [NFCeOut].[TipoEmissaoContribuinte] tec (NOLOCK)
   ON tec.[TipoEmissaoContribuinte] = ch.tipoEmissaoContribuinte
LEFT JOIN [NFCe_Out].[NFCeAdmin].[ResponsaveisEstabelecimentos] re (NOLOCK)
   ON re.pKeyContribuintes = ch.pKey
WHERE ch.CNPJ IN ('59418806001623','00623904000688','61149506004220','66970229020001')
--WHERE ch.CD_DRT = 0
--ORDER BY ch.timestampUpd desc
ORDER BY ch.timestampUpd DESC
 
SELECT TOP 100 * FROM [NFCeOut].[NFCe_Constraint]   (NOLOCK)
WHERE cnpjEmit LIKE '05312516%'
ORDER BY timestampReg DESC
 
SELECT TOP 100 * FROM [NFCeOut].[ContribuintesHabilitados] ch   (NOLOCK)
WHERE empresa LIKE '%post%'
 
RETURN
--========================================================================================
--========================================================================================
--========================================================================================
--========================================================================================
USE NFCe_Out_XML
GO
 
SELECT TOP 100 * FROM [NFCeOut].[Certificado] (NOLOCK)
WHERE $Partition.PF_Part_01_Data(timestampReg) = $Partition.PF_Part_01_Data(GETDATE())
ORDER BY pKey DESC
 
SELECT TOP 100 *, [NFCeOut].UdfDecompressNFCe(evento) FROM [NFCeOut].[EventoXml] (NOLOCK)
WHERE $Partition.PF_Part_01_Data(timestampReg) = $Partition.PF_Part_01_Data(GETDATE())
ORDER BY pKey DESC
 
SELECT TOP 100 *, [NFCeOut].UdfDecompressNFCe(inutilizacao) FROM [NFCeOut].[InutXml] (NOLOCK)
WHERE $Partition.PF_Part_01_Data(timestampReg) = $Partition.PF_Part_01_Data(GETDATE())
ORDER BY pKey DESC
 
SELECT TOP 100 *, [NFCeOut].UdfDecompressNFCe(NFCe) FROM [NFCeOut].[NFCeXml] (NOLOCK)
WHERE $Partition.PF_Part_01_Data(timestampReg) = $Partition.PF_Part_01_Data(GETDATE())
ORDER BY pKey DESC
 
RETURN
--========================================================================================
--========================================================================================
--========================================================================================
--========================================================================================
 
-- Achar NFCe por chave - autorizada ou rejeitada
DECLARE  @chNFCe [VARCHAR](44) = '35150805252730000101650010000000011072675331'
DECLARE @cnpjEmit CHAR(14)     = SUBSTRING(@chNFCe, 7, 14)
DECLARE @serie SMALLINT        = SUBSTRING(@chNFCe, 23, 3)
DECLARE @numero INT            = SUBSTRING(@chNFCe, 26, 9)   
 
SELECT * FROM [NFCe_Out].[NFCeOut].[ProtocoloRejeitado] (NOLOCK)
WHERE cnpj   = @cnpjEmit
  AND chNFCe = @chNFCe
 
SELECT n.pKey, n.recibo, n.timestampReg, nc.cnpjEmit, indSinc
FROM [NFCe_Out_Constraint].[NFCeOut].[NFCe_Constraint] AS nc WITH (NOLOCK)
LEFT JOIN [NFCeOut].[NFCe] AS n WITH (NOLOCK)
ON      n.pKey         = nc.fkProtocolo
    AND n.timestampReg = nc.timestampReg
WHERE   nc.cnpjEmit = @cnpjEmit
    AND nc.numero   = @numero
    AND nc.serie    = @serie
 
-- Nota + certificado de uma nota autorizada
 
SELECT pr.*, dt.ipOrigem, [NFCe_Out_XML].[NFCeOut].UdfDecompressNFCe(nx.NFCe), c.certificado
FROM [NFCe_Out].[NFCeOut].[Protocolo] pr (NOLOCK)
INNER JOIN [NFCe_Out_XML].[NFCeOut].NFCeXml nx (NOLOCK)
ON nx.pKey = pr.pKey
INNER JOIN [NFCe_Out].[NFCeOut].[DadosTransmissao] dt (NOLOCK)
ON dt.pKey = pr.pKey
INNER JOIN [NFCe_Out_XML].[NFCeOut].[Certificado] c (NOLOCK)
ON c.pkey = dt.pKeyCertificado
WHERE pr.pKey = 238897
 
RETURN
 
-- Nota + certificado de uma nota rejeitada
 
DECLARE @recibo bigint = 182189
SELECT 'LoteXML + LoteCompl' AS 'Tabelas',  PR.*, [NFce_In_XML].[NFCeIn].[UdfDecompressNFCe](mensagemBin), [NFce_In_XML].[NFCeIn].[UdfDecompressNFCe](cabecalhoBin), lc.[Certificado]
FROM [NFCe_Out].[NFCeOut].[ProtocoloRejeitado] PR (NOLOCK)
INNER JOIN [NFCe_In_XML].[NFCeIn].[LoteXML] LO WITH (NOLOCK)
    ON LO.pKey = PR.recibo
INNER JOIN [NFCe_In_XML].[NFCeIn].[LoteCompl] lc WITH (NOLOCK)
    ON LO.pKey = lc.pKey
    WHERE LO.pKey = @recibo
UNION
SELECT '[LoteSincXML]' AS 'Tabelas', PR.*, [NFce_In_XML].[NFCeIn].[UdfDecompressNFCe](LO.mensagemBin), [NFce_In_XML].[NFCeIn].[UdfDecompressNFCe](LO.cabecalhoBin), lo.certificado
FROM [NFCe_Out].[NFCeOut].[ProtocoloRejeitado] PR (NOLOCK)
INNER JOIN [NFCe_In_XML].[NFCeIn].[LoteSincXML] LO WITH (NOLOCK)
    ON LO.pKey = PR.recibo
    WHERE LO.pKey = @recibo
UNION
SELECT 'LoteStaging + LoteCompl' AS 'Tabelas', PR.*, [NFce_In_XML].[NFCeIn].[UdfDecompressNFCe](LS.mensagemBin), [NFce_In_XML].[NFCeIn].[UdfDecompressNFCe](LS.cabecalhoBin), LC.certificado
FROM [NFCe_Out].[NFCeOut].[ProtocoloRejeitado] PR (NOLOCK)
INNER JOIN [NFCe_In].[NFCeIn].[LoteStaging] LS WITH (NOLOCK)
    ON LS.pKey = PR.recibo
INNER JOIN [NFCe_In_XML].[NFCeIn].[LoteCompl] lc WITH (NOLOCK)
    ON LS.pKey = lc.pKey
    WHERE LS.pKey = @recibo
 
RETURN
--========================================================================================
-- Tokens de um contribuinte
SELECT * FROM [NFCe_Out].[NFCeOut].TokenContribuinte
 WHERE CNPJBase = '61077905'
 
RETURN
--========================================================================================
-- Notas por minuto
DECLARE @dataBase  DATETIME = '2015-08-26 00:00:00'
DECLARE @dataCorte DATETIME = '2015-08-27 00:00:00'
 
SELECT --cast(DATEPART(day   , timestampReg) as varchar) + ':' +
       cast(DATEPART(hour  , timestampReg) as varchar) + ':' +
       cast(DATEPART(MINUTE, timestampReg) as varchar) as Data, count(1) Notas_Autorizadas
  FROM[NFCe_Out].[NFCeOut].[Protocolo] (NOLOCK)
  WHERE $Partition.PF_Part_01_Data(timestampReg) = $Partition.PF_Part_01_Data(GETDATE())
    AND timestampReg > @dataBase
    AND timestampReg < @dataCorte
  group by DATEPART(hour, timestampReg),
           DATEPART(MINUTE, timestampReg)        
  order by --Notas_Autorizadas Desc
           DATEPART(hour, timestampReg),
           DATEPART(MINUTE, timestampReg)
 
-- Quantidade de notas em um determinado mes
DECLARE @mesSelecionado datetime = '2015-11-01'
SELECT DATEPART(DAY, timestampReg) AS DIA, count(1)
  FROM [NFCe_Out].[NFCeOut].[Protocolo]        (NOLOCK)
  WHERE $Partition.PF_Part_01_Data(timestampReg) = $Partition.PF_Part_01_Data(@mesSelecionado)
  AND timestampReg > @mesSelecionado
  GROUP BY DATEPART(DAY, timestampReg)
  ORDER BY DIA
 
SELECT DATEPART(MONTH, timestampReg) AS MES, count(1)
  FROM [NFCe_Out].[NFCeOut].[Protocolo]        (NOLOCK)
  WHERE $Partition.PF_Part_01_Data(timestampReg) = $Partition.PF_Part_01_Data(@mesSelecionado)
  AND timestampReg > @mesSelecionado
  GROUP BY DATEPART(MONTH, timestampReg)
 
-- Autorizaçoes/eventos/inutilizaçoes diárias desde um determinado dia, no mes corrente:
DECLARE @diaSelecionado datetime = '2015-11-01'
SELECT CAST(datepart(day, timestampReg) AS int) AS 'Dia', TD.descricao, count(1) AS 'Nro de Protocolos'
FROM [NFCe_Out].[NFCeOut].[Protocolo] P (nolock)
INNER JOIN [NFCe_Out].[NFCeOut].TpDocumento TD (nolock)
ON TD.tpDocumento = P.tpDocumento
WHERE $Partition.PF_Part_01_Data(timestampReg) = $Partition.PF_Part_01_Data(GETDATE())
  AND timestampReg > @diaSelecionado
GROUP BY DATEPART(DAY, timestampReg), td.descricao
ORDER BY Dia
 
 
-- Notas por hora emitidas por um determinado contribuinte em um dia
SELECT cast(DATEPART(hour, timestampReg) as varchar) + ':'
      ,count(1) Notas_Autorizadas
from [NFCe_Out_Constraint].[NFCeOut].[NFCe_Constraint] (nolock)
where cnpjEmit in ('00623904000173', '00623904000688')
 and timestampReg > '2015-05-16'
 and timestampReg < '2015-05-17'
group by DATEPART(hour, timestampReg)
order by DATEPART(hour, timestampReg)
 
-- Notas de determinado contribuinte em determinado horário
SELECT TOP 1000 *, [NFCe_Out_XML].[NFCeOut].UdfDecompressNFCe(nx.NFCe)
FROM [NFCe_Out_Constraint].[NFCeOut].[NFCe_Constraint] nc (nolock)
INNER JOIN [NFCe_Out_XML].[NFCeOut].[NFCeXml] nx
ON nx.pKey = nc.fkProtocolo
WHERE nc.cnpjEmit in ('46057626000135','46057626000135') --,'66970229002704','66970229020001')
AND nx.timestampReg BETWEEN '2015-05-22' AND '2015-07-24'
ORDER BY nx.timestampReg DESC
 
RETURN
--========================================================================================
-- select para qr-code
SELECT TOP 100 chNFCe, dhEmi, idDestinatario, vNF, vICMS, digVal, P.timestampReg
  FROM       [NFCe_Out].[NFCeOut].[Protocolo] P (NOLOCK)
  INNER JOIN [NFCe_Out].[NFCeOut].[DadosApoio]  da
  ON da.pKey = P.pKey
  INNER JOIN [NFCe_Out].[NFCeOut].[NFCe] n
  ON n.pKey = P.pKey
  WHERE $Partition.PF_Part_01_Data(P.timestampReg) = $Partition.PF_Part_01_Data(GETDATE())
  ORDER BY p.pKey DESC
 
SELECT chNFCe, dhEmi, idDestinatario, vNF, vICMS, digVal, P.timestampReg
  FROM       [NFCe_Out].[NFCeOut].[Protocolo] P (NOLOCK)
  INNER JOIN [NFCe_Out].[NFCeOut].[DadosApoio]  da
  ON da.pKey = P.pKey
  INNER JOIN [NFCe_Out].[NFCeOut].NFCe n
  ON n.pKey = P.pKey
  WHERE $Partition.PF_Part_01_Data(P.timestampReg) = $Partition.PF_Part_01_Data(GETDATE())
    AND chNFCe = '35150746057626000135650010000097881001749110'
  ORDER BY p.pKey DESC
 
RETURN
--========================================================================================
-- Queries da NFP
USE [NFCe_Out]
GO
 
DECLARE @mesSelecionado datetime = '2015-05-01'
 
SELECT count(1) as 'Total NFC-e'
  FROM [NFCe_Out].[NFCeOut].[NFCe]        (NOLOCK)
  WHERE $Partition.PF_Part_01_Data(timestampReg) = $Partition.PF_Part_01_Data(@mesSelecionado)
  AND timestampReg > @mesSelecionado
 
SELECT count(1) as 'Total cancelamento de notas emitidas naquele mês'
  FROM [NFCe_Out].[NFCeOut].NFCe nfce (NOLOCK)
  INNER JOIN [NFCe_Out_Constraint].[NFCeOut].[Evento_Constraint] ec (NOLOCK)
  ON nfce.chNFCe = ec.chNFCe
  WHERE $Partition.PF_Part_01_Data(nfce.timestampReg) = $Partition.PF_Part_01_Data(@mesSelecionado)
 
SELECT count(1) as 'Total NFC-e com IdDestinatario'
  FROM [NFCe_Out].[NFCeOut].[NFCe] n       (NOLOCK)
  INNER JOIN[NFCe_Out].[NFCeOut].[DadosApoio]  d (NOLOCK)
  ON n.pKey = d.pKey
  WHERE $Partition.PF_Part_01_Data(n.timestampReg) = $Partition.PF_Part_01_Data(@mesSelecionado) AND
  n.timestampReg > @mesSelecionado
  AND d.idDestinatario IS NOT NULL
  AND d.tipoDestinatario <> 3
 
SELECT COUNT(1) as 'Total cancelamento de notas emitidas naquele mês com IdDestinatário'
FROM [NFCe_Out].[NFCeOut].NFCe nfce (NOLOCK)
INNER JOIN [NFCe_Out_Constraint].[NFCeOut].[Evento_Constraint] ec (NOLOCK)
ON nfce.chNFCe = ec.chNFCe
INNER JOIN[NFCe_Out].[NFCeOut].[DadosApoio]  d (NOLOCK)
on d.pKey = nfce.pKey
WHERE $Partition.PF_Part_01_Data(nfce.timestampReg) = $Partition.PF_Part_01_Data(@mesSelecionado)
  AND d.idDestinatario IS NOT NULL
  AND d.tipoDestinatario <> 3
 
RETURN
--========================================================================================
-- Objetos de um determinado banco
 SELECT 'SELECT * FROM ' + TABLE_SCHEMA + '.' + TABLE_NAME FROM information_schema.tables
 SELECT * FROM sys.sequences s
 
-- Número de linhas de todas as tabelas de uma base
CREATE TABLE #counts
(
    table_name varchar(255),
    row_count int
)
EXEC sp_MSForEachTable @command1='INSERT #counts (table_name, row_count) SELECT ''?'', COUNT(*) FROM ?'
SELECT table_name, row_count FROM #counts ORDER BY table_name, row_count DESC
DROP TABLE #counts
```
