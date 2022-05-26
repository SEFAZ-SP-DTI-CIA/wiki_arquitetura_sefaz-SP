# Queries usadas para testes dos extratores DW NF-e

Lista não exaustiva de queries SQL Server e Sybase usadas para testes dos extratores.



## No SQL Server:

### 1) Configurações de execução


```
SELECT * FROM ETL_NFE.DWNFe.ConfigSistema
WHERE parametro in ('EXECUTAR_AGORA','EXTRAI_DATA_ESPECIFICA','DATA_INICIAL_ESPECIFICA','DATA_FINAL_ESPECIFICA','EXTRAI_RANGE_ESPECIFICO')
```


Recomenda-se que os valores estejam conforme abaixo:

|parâmetro |valor  |
|--|--|
|EXECUTAR_AGORA |true |
|EXTRAI_DATA_ESPECIFICA	|true |
|DATA_INICIAL_ESPECIFICA  |26/06/2019  |
|DATA_FINAL_ESPECIFICA  |26/06/2019  |
|EXTRAI_RANGE_ESPECIFICO |false  |

A data específica deve ser alterada conforme o exemplo a ser testado:


```
UPDATE ETL_NFE.DWNFe.ConfigSistema
SET valor = '26/06/2019'
where parametro in ('DATA_INICIAL_ESPECIFICA','DATA_FINAL_ESPECIFICA')
```


### 2) Acompanhamento das filas


```
select count(1) as 'Emissao SP'	      FROM [NFe_Out].[Extracao].[DadosNFeQueue] (nolock)
select count(1) as 'Cancelamento SP'  FROM [NFe_Out].[Extracao].[DadosCancNFeQueue] (nolock)
select count(1) as 'Evento SP'	      FROM [NFe_Out].[Extracao].[DadosEventoQueue] (nolock)
select count(1) as 'Inutilizacao SP'  FROM [NFe_Out].[Extracao].[DadosInutNFeQueue] (nolock)

select count(1) as 'Emissao UFs'      FROM [NFe_Out_UFs].Extracao.DadosNFeQueue (nolock)
select count(1) as 'Cancelamento UFs' FROM [NFe_Out_UFs].[Extracao].[DadosCancNFeQueue] (nolock)
select count(1) as 'Evento UFs'	      FROM [NFe_Out_UFs].[Extracao].[DadosEventoQueue] (nolock)
```


_**​(Rodar antes e logo depois do método "PreExecute()" da classe Reader alvo do teste)**_


### 3) Removendo elemento das filas (para simplificação do debug)

3.1) As queries abaixo geram a lista de todos os campos usados na extração:

### EMISSÃO NFe_Out:

```
SELECT *
FROM [NFe_Out].[EXTRACAO].DadosNFeQueue p
INNER JOIN [NFeOut].[NFe_Constraint] AS nc
    ON p.pKey = nc.fkProtocolo
        AND p.timestampReg = nc.timestampReg
    INNER JOIN [DFe_XML].[NFe].[NFeXml] AS nx
    ON p.pKey = nx.pKey
        AND p.timestampReg = nx.timestampReg
    LEFT JOIN [NFeOut].[DadosTransmissao] AS dt
    ON p.pKey = dt.pKey
        AND p.timestampReg = dt.timestampReg
    LEFT JOIN [NFeOut].[DadosTransmissao_AN] AS dtAN
    ON p.pKey = dtAN.pKey
        AND p.timestampReg = dtAN.timestampReg
    LEFT JOIN [NFeOut].[Certificado] AS c
    ON dt.pKeyCertificado = c.pKey
```


### EVENTOS NFe_Out:

```
SELECT *
 FROM Extracao.DadosEventoQueue AS p
    INNER JOIN [NFeEventos].[Evento_Constraint] AS ec
    ON p.pKey = ec.fkProtocolo
        AND p.timestampReg = ec.timestampReg
    INNER JOIN [DFe_XML].[NFe].[EventoXml] AS ex
    ON p.pKey = ex.pKey
        AND p.timestampReg = ex.timestampPart
    LEFT JOIN [NFeOut].[DadosTransmissao] AS dt
    ON p.pKey = dt.pKey
    LEFT JOIN [NFeOut].[DadosTransmissao_AN] AS dtAN
    ON p.pKey = dtAN.pKey
```
​

_**Para os demais tipos de documentos e documentos de outras UFs, seguir o mesmo raciocínio, copiando a query interna das respectivas procs, listadas abaixo:**_
Inutilização SP:          USP_NFE_XML_INUTILIZACAO_SEL.sql
Emissão Outras UFs:  USP_NFE_XML_EMISSAO_UFS_SEL.sql
Eventos Outras UFs:  USP_NFE_XML_EVENTO_SEL.sql

3.2) Após verificar quais registros atendem às necessidades do teste, pode apagar os demais registros, para simplificar o teste:

`DELETE FROM [NFe_Out].Extracao.Dados<Tipo>Queue WHERE pKey not in` (<_lista de pKeys interessantes para o teste_>)

Ex.: 

```
DELETE FROM [NFe_Out].Extracao.DadosNFeQueue WHERE pKey not in (5560672123, 5560672124)
DELETE FROM [NFe_Out].Extracao.DadosEventoQueue WHERE pKey not in (5560672124)
DELETE FROM [NFe_Out].Extracao.DadosInutNFeQueue WHERE pKey not in (5560672125)
```


_________________________________________________________________________________________________________________________________________________________

## No Sybase:


1) Verificação de flags de execução 
Obs: o extrator NFe já limpa as flags quando em Debug


```
commit;
select * from ods.tb_dsod_nfe_controle_execucao  
select * from ods.tb_dsod_nfeuf_controle_execucao
```

_________________________________________________________________________________________________________________________________________________________
