
```
USE CTe_Out
GO
-----------------------------------------------------
-- Documentos armazenados mais recentes

SELECT TOP 10 * 
  FROM [SVD].[NSU] (NOLOCK)
 --WHERE NSU_TIPO_REGISTRO = 1 // 1=CT-e 2=CT-eOS 3=Inutilizacao 4=Evento 5=GTV-e
 ORDER BY SNSU_PKEY DESC

-----------------------------------------------------
-- Requisições mais recentes do SVD-RS chamando o serviço de Distribuição

-- Quaisquer requisições
SELECT TOP 10 * 
  FROM [SVD].[LOG_ENVIO] (NOLOCK)
 ORDER BY LOE_PKEY DESC

-- Requisições que retornaram dados
SELECT TOP 10 * 
  FROM [SVD].[LOG_ENVIO] (NOLOCK)
 WHERE LOE_STATUS = 118
 ORDER BY LOE_PKEY DESC

-----------------------------------------------------
-- Monitoração de últimos recebimentos

-- Autorização de documentos
SELECT TOP 1 DATEDIFF(MINUTE, NSU_DATA_RECEPCAO, GETDATE()) as 'Minutos sem autorizar documentos para distribuição', *
  FROM [SVD].[NSU] (NOLOCK)
 ORDER BY SNSU_PKEY DESC

-- Chamadas do SVD-RS ao serviço de distribuição
SELECT TOP 1 DATEDIFF(MINUTE, LOE_DATA_RECEPCAO, GETDATE()) as 'Minutos sem SVD-RS chamar serviço de Distribuição', *
  FROM [SVD].[LOG_ENVIO] (NOLOCK)
 ORDER BY LOE_PKEY DESC

-- Horário de autorização do NSU mais recente sendo distribuído para SVD-RS
DECLARE @maiorNSUPedido BIGINT = (SELECT TOP 1 LOE_NSU_INICIO
                                    FROM [SVD].[LOG_ENVIO] (NOLOCK)
                                   WHERE LOE_STATUS = 118
                                   ORDER BY LOE_PKEY DESC)

SELECT TOP 1 DATEDIFF(MINUTE, NSU_DATA_RECEPCAO, GETDATE()) as 'Atraso em minutos entre autorização e RS-SVD vir buscar pelo serviço de Distribuição', * 
  FROM [SVD].[NSU] (NOLOCK)
 WHERE SNSU_PKEY <= @maiorNSUPedido
 ORDER BY SNSU_PKEY DESC
```
