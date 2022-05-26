# Queries usadas para testes dos extratores DW MDF-e

Lista não exaustiva de queries SQL Server e Sybase usadas para testes dos extratores.

## No App.config:

Recomenda-se que os valores estejam conforme abaixo, sendo as datas <inicial> e <final> configuradas de acordo com os registros da SRVNFEDES01 que forem interessantes para o teste em questão:

|parâmetro |valor |
|--|--|
|EXECUTAR_AGORA |true |
|EXTRAI_DATA_ESPECIFICA |true |
|DATA_INICIAL_ESPECIFICA |<inicial> |
|DATA_FINAL_ESPECIFICA | <final> |
|EXTRAI_RANGE_ESPECIFICO |false |

## No SQL Server:

### 1) Acompanhamento das filas


```
select count(1) as 'MDFe'   FROM [MDFe_Out].[Extracao].[DadosMDFeQueue​]   (nolock)
select count(1) as 'Evento' FROM [MDFe_Out].[Extracao].[DadosEventoQueue] (nolock)
```

_**​(Rodar antes e logo depois do método "PreExecute()" da classe Reader alvo do teste)**_


## 3) Removendo elemento das filas (para simplificação do debug)

3.1) As queries abaixo geram a lista de todos os campos usados na extração:

### MDF-e:

```
SELECT *
FROM [MDFe_Out].[Extracao].[DadosMDFeQueue​] p
INNER JOIN [MDFe].[MDFe_Constraint] AS mc
ON p.pKey = mc.pKey
INNER JOIN [MDFe_XML].[MDFe].[MDFe_Xml] AS mx
ON p.pKey          = mx.pKey
AND p.timestampReg = mx.timestampReg
```


**EVENTOS MDF-e:**

```
SELECT *
 FROM ​[MDFe_Out].[Extracao].[DadosEventoQueue]​ p
INNER JOIN [MDFe].[Evento_Constraint] AS ec
ON p.pKey = ec.pKey
INNER JOIN [MDFe_XML].[MDFe].[EventoXml] AS ex
ON p.pKey          = ex.pKey
AND p.timestampReg = ex.timestampReg
```


3.2) Após verificar quais registros atendem às necessidades do teste, pode apagar os demais registros, para simplificar o teste:

Ex.: 

```
DELETE FROM [MDFe_Out].[Extracao].[DadosMDFeQueue​]   WHERE pKey not in (x, y, z)
DELETE FROM [MDFe_Out].[Extracao].[DadosEventoQueue] WHERE pKey not in (x, y, z)
```

_________________________________________________________________________________________________________________________________________________________

## No Sybase:


1) Verificação de flags de execução 
Obs: o extrator já limpa as flags quando em Debug


```
commit;
select * from ods.tb_dsod_mdfe_controle_execucao
```


_________________________________________________________________________________________________________________________________________________________