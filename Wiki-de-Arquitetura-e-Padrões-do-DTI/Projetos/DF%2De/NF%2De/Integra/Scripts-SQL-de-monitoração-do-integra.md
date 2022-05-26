
```
USE DB_NFeIntegra
GO

select 'ATRASO_INTEGRA_NSU_ENVIO', AVG(atraso_envio_segundos) as MEDIA_SEGUNDOS , AVG(atraso_envio_minutos) as MEDIA_MINUTOS from (
select top 10 DATEDIFF(SECOND,timestampReg, timestampIntegra) as atraso_envio_segundos,DATEDIFF(MINUTE,timestampReg, timestampIntegra) as atraso_envio_minutos, * from NFe_Out.NFeIntegra.IntegraNSU (nolock)
where
NFe_Out.$partition.PF_Part_02_Data(timestampReg) =  NFe_Out.$partition.PF_Part_02_Data(getdate())
and fkIntegraTipo = 1
order by nsu desc) TAB_A

select  'ATRASO_INTEGRA_NSU_RECEBIMENTO', AVG(atraso_recebimento_segundos) as MEDIA_SEGUNDOS , AVG(atraso_recebimento_minutos) as MEDIA_MINUTOS from (
select top 10 DATEDIFF(SECOND,timestampReg, timestampIntegra) as atraso_recebimento_segundos,DATEDIFF(MINUTE,timestampReg, timestampIntegra) as atraso_recebimento_minutos, * from NFe_Out.NFeIntegra.IntegraNSU (nolock)
where
NFe_Out.$partition.PF_Part_02_Data(timestampReg) =  NFe_Out.$partition.PF_Part_02_Data(getdate())
and fkIntegraTipo != 1
order by nsu desc) TAB_A


DECLARE @dataInicio datetime
    DECLARE @dataFim datetime
    DECLARE @offset int
    SET @offset     = 1
    SET @dataInicio = DATEADD(MINUTE, -@offset, GETDATE()) 
    SET @dataFim    = GETDATE()
    
       SELECT SUM(qtdItensLote) as NroNFesEnviadas, AVG(qtdItensLote) as DocsPorLote, AVG(tempoEnvio) as TMedioAN INTO #tmpNumEstatEnvio
       FROM
       (
             SELECT qtdItensLote, 
                       tempoEnvio
             FROM NFeEnvio.EstatisticaEnvio (nolock)
             WHERE dataEnvio >= @dataInicio and dataEnvio < @dataFim 
       ) as tmpTable1

    SELECT COUNT(1) as NroNFesAutorizadas INTO #tmpNumNFeOut
    FROM
    (
           SELECT 1 as HUM
           FROM NFe_Out.NFeOut.NFe (nolock)
           WHERE 
             NFE_Out.$partition.PF_Part_02_Data(timestampReg) =  NFE_Out.$partition.PF_Part_02_Data(getdate())
             AND timestampReg >= @dataInicio and timestampReg < @dataFim
    ) as tmpTable
    ORDER BY 1
        
    SELECT 'Media nos últimos ' + CAST(@offset AS VARCHAR) + ' minutos', CAST (EE.TMedioAN as DECIMAL(5,2)) as tMedioAN, 
           EE.DocsPorLote, NFO.NroNFesAutorizadas, EE.NroNFesEnviadas, 
           NFO.NroNFesAutorizadas - EE.NroNFesEnviadas as 'Entrada - Saida (+ aumenta a fila, - diminui a fila' 
    FROM       #tmpNumEstatEnvio EE (nolock)
    INNER JOIN #tmpNumNFeOut     NFO
    ON 1=1
    
    DROP TABLE #tmpNumEstatEnvio
    DROP TABLE #tmpNumNFeOut

    -- fazendo estatísticas para NFeEventos
    SELECT SUM(Lotes) as NroEventosEnviados, AVG(Lotes) as DocsPorLote, AVG(TempoAN) as TMedioAN INTO #tmpNumEstatEnvioEventos
    FROM
    (
       SELECT ESWE_QN_ITENS_LOTE as Lotes, 
              ESWE_QN_TEMPO_ENVIO as TempoAN
       FROM dbo.ESTATISTICA_ENVIO (nolock)
       WHERE ESWE_DT_ENVIO >= @dataInicio and ESWE_DT_ENVIO < @dataFim
             and [CONF_TX_SERVICO] = 'NFeEnvioEventos'
    ) as tmpTable1
       
       
    SELECT COUNT(1) as NroEventosAutorizados INTO #tmpNumNFeEventos
    FROM
    (
           SELECT 1 as HUM
           FROM NFe_Out.NFeEventos.Evento (nolock)
           WHERE --timestampPart >= @dataInicio 
             --and timestampPart < @dataFim
             timestampReg >= @dataInicio 
             and timestampReg < @dataFim
    ) as tmpTable
    ORDER BY 1
      
           
    SELECT 'Media nos últimos ' + CAST(@offset AS VARCHAR) + ' minutos para eventos', CAST (EE.TMedioAN as DECIMAL(5,2)) as tMedioAN, 
           EE.DocsPorLote, NFO.NroEventosAutorizados, EE.NroEventosEnviados, 
           NFO.NroEventosAutorizados - EE.NroEventosEnviados as 'Entrada - Saida (+ aumenta a fila, - diminui a fila)' 
    FROM       #tmpNumEstatEnvioEventos EE (nolock)
    INNER JOIN #tmpNumNFeEventos     NFO
    ON 1=1    
        
    drop table #tmpNumEstatEnvioEventos
    drop table #tmpNumNFeEventos
    GO



    -- TEMPO DE RESPOSTA MÉDIO nas últimas N horas
    DECLARE @N INT;
    SET @N=2
    select * from 
       (SELECT cast(DATENAME(hour, dataEnvio) as int) as HORA_ENVIO, AVG(tempoEnvio) AS MEDIA_TEMPO_RESPOSTA
       FROM
             NFeEnvio.EstatisticaEnvio (NOLOCK)
       WHERE
             dataEnvio BETWEEN DATEADD(HOUR, -@N, GETDATE()) AND GETDATE()
       GROUP BY cast(DATENAME(hour, dataEnvio) as int)) TAB
       order by TAB.HORA_ENVIO desc
       
DECLARE @cd_protocolo AS BIGINT
DECLARE @tsProtocoloRecebido AS DATETIME
DECLARE @tsProtocoloNFe AS DATETIME
DECLARE @idNFe AS VARCHAR(44)

SELECT @cd_protocolo=MAX(pkey) FROM NFeEnvio.ProtocoloRecebido (NOLOCK)
where
DB_NFeIntegra.$partition.PF_Part_01_Data(timestampReg) =  DB_NFeIntegra.$partition.PF_Part_01_Data(getdate())

SELECT @tsProtocoloRecebido=timestampReg, @idNFe=chNFe FROM NFeEnvio.ProtocoloRecebido (NOLOCK)
WHERE pkey = @cd_protocolo
SELECT TOP 1 @tsProtocoloNFe=timestampReg from NFE_Out.NFeOut.NFe (nolock)
where chNFe = @idNFe

  select 
             CONVERT(varchar(10), (@tsProtocoloRecebido - @tsProtocoloNFe), 8) as ATRASO, 
             MIN(dataModificado) as PRIMEIRO_FILA_LIDO, MAX(dataModificado) as ULTIMO_FILA_LIDO, 
             COUNT(1) as QTD_LIDOS
             --,CONVERT(varchar(10), (MAX(PROT_DT_MODIFIED)- MIN(PROT_DT_MODIFIED)), 8)  as TEMPO_PUMPER
             from NFeEnvio.ProtocoloNFe_queue (NOLOCK)             
  WHERE flagRead = 1

  -- substituindo o Count por algo mais rápido
SELECT 
(SELECT rowcnt FROM sys.sysindexes WHERE id = OBJECT_ID('NFeEnvio.ProtocoloNFe') AND (indid <= 1)) as  'ProtocoloNFe', 
(SELECT rowcnt FROM sys.sysindexes WHERE id = OBJECT_ID('NFeEnvio.ProtocoloNFe_Queue') AND (indid <= 1)) as  'ProtocoloNFe_Queue', 
--(SELECT COUNT(1) FROM NFeEnvio.LogXML (NOLOCK)) AS 'NFe_Envio.LogXML',
(SELECT COUNT(1) FROM NFeEnvio.ProtocoloNFe WITH(NOLOCK) WHERE pkey NOT IN (SELECT pkey FROM NFeEnvio.ProtocoloNFe_Queue (NOLOCK)) 
                                                                                                                                       AND    nroTentativas not in (0, 3)) AS 'Quase PRESOS',
(SELECT COUNT(1) FROM NFeEnvio.ProtocoloNFe WITH(NOLOCK) WHERE pkey NOT IN (SELECT pkey FROM NFeEnvio.ProtocoloNFe_Queue (NOLOCK)) 
                                                                                                                                       AND    nroTentativas = 3) AS 'PRESOS'
--(SELECT CONVERT(varchar(10), (MAX(PROT_DT_MODIFIED)- MIN(PROT_DT_MODIFIED)), 8) FROM dbo.PROTOCOLO_ENVIO_QUEUE (NOLOCK)) as 'TEMPO EM FILA'

/*
select 
(select TOP 1  pkey from NFeEnvio.ProtocoloRecebido  (nolock)
ORDER BY pkey DESC) AS ULTIMO_PROT_RECEBIDO,          
(select TOP 1  timestampReg from NFeEnvio.ProtocoloRecebido  (nolock)
ORDER BY pkey DESC) AS HORA_RECEBIDO,          
(select TOP 1  pkey from NFeEnvio.ProtocoloRejeitado (nolock)
ORDER BY pkey DESC) AS ULTIMO_REJEITADO
*/

/*

INSERT INTO NFeEnvio.ProtocoloNFe_Queue
       (
             pkey,
             flagRead,
             dataModificado 
       )
       SELECT
             pkey,
             0,
             GETDATE()
       FROM
             NFeEnvio.ProtocoloNFe WITH(NOLOCK)
       WHERE
             pkey NOT IN (SELECT pkey FROM NFeEnvio.ProtocoloNFe_Queue (NOLOCK))                                                                                                                         
       AND    nroTentativas = 3

    -- Reset do número máximo de retentativas para documentos que a excederam
       UPDATE NFeEnvio.ProtocoloNFe
       SET    nroTentativas = 0
       WHERE
             nroTentativas = 3

*/
    
       SELECT  
       convert(varchar(20),MIN(dataModificado), 113) AS 'FILA ENVIO - ANTIGO',  
       convert(varchar(20),MAX(dataModificado), 113) AS 'FILA ENVIO - RECENTE', 
       convert(varchar(20),(MAX(dataModificado) - MIN(dataModificado)),108) AS 'TEMPO EM FILA'
       FROM NFeEnvio.ProtocoloNFe_Queue (NOLOCK)             
       
       SELECT  
       convert(varchar(20),MIN(timestampReg), 113) AS 'PROTOCOLO - ANTIGO',  
       convert(varchar(20),MAX(timestampReg), 113) AS 'PROTOCOLO - RECENTE', 
       convert(varchar(20),(MAX(timestampReg) - MIN(timestampReg)),108) AS 'TEMPO EM PROTOCOLO'
       FROM NFeEnvio.ProtocoloNFe (NOLOCK)            


---------------------------------------------------------------------------------------
       SELECT 
             COUNT(1) AS QTD,
             erro,
             MIN(timestampReg) AS PRIMEIRA,
             MAX(timestampReg) AS ULTIMA
       FROM NFeEnvio.LogXML WITH(NOLOCK)
       GROUP BY erro 
       order by MAX(timestampReg) desc
       
       SELECT 
             COUNT(1) AS QTD,
             LOXM_TX_Erro as 'Loxm_tx_erro eventos',
             MIN(LOXM_DT_Timestamp) AS PRIMEIRA,
             MAX(LOXM_DT_Timestamp) AS ULTIMA
       FROM NFeEventos.LOG_XML WITH(NOLOCK)
       WHERE LOXM_DT_Timestamp >= '2012-02-14' 
       GROUP BY LOXM_TX_Erro      
       order by MAX(LOXM_DT_Timestamp) desc    
       


-- substituindo o Count por algo mais rápido
SELECT 
(SELECT rowcnt FROM sys.sysindexes WHERE id = OBJECT_ID('NFeEventos.EVENTO') AND (indid <= 1)) as  'PROTOCOLOEventos', 
(SELECT rowcnt FROM sys.sysindexes WHERE id = OBJECT_ID('NFeEventos.EVENTO_ENVIO_QUEUE') AND (indid <= 1)) as  'ENVIO_QUEUE_TOTAL_Eventos', 
(SELECT rowcnt FROM sys.sysindexes WHERE id = OBJECT_ID('NFeEventos.EVENTO_RETORNO_QUEUE') AND (indid <= 1)) as  'RETORNO_QUEUE_Eventos', 
(SELECT rowcnt FROM sys.sysindexes WHERE id = OBJECT_ID('NFeEventos.LOG_XML') AND (indid <= 1)) as  'LOG_XML_Eventos', 
(SELECT rowcnt FROM sys.sysindexes WHERE id = OBJECT_ID('NFeEventos.ProtocolosTemp_eventos') AND (indid <= 1)) as  'TEMPORÁRIA_Eventos', 
(SELECT COUNT(1) FROM NFeEventos.EVENTO WITH(NOLOCK) WHERE EVT_CD_EVENTO NOT IN (SELECT EVT_CD_EVENTO FROM NFeEventos.EVENTO_ENVIO_QUEUE  (NOLOCK)) 
                                                                                                                                       AND    EVT_NR_TENTATIVAS not in (0, 3)) AS 'Quase PRESOS',
(SELECT COUNT(1) FROM NFeEventos.EVENTO WITH(NOLOCK) WHERE EVT_CD_EVENTO NOT IN (SELECT EVT_CD_EVENTO FROM NFeEventos.EVENTO_ENVIO_QUEUE  (NOLOCK)) 
                                                                                                                                       AND    EVT_NR_TENTATIVAS = 3) AS 'PRESOS'
```

