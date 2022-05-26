## Regras da Sonda NF-e

Obs: 
. todos os @thresholds são lidos da tabela de Config Sistema
. as verificações são feitas com base nos lotes recebidos nas últimas 12 horas


### 1) Regra 1
Nenhum lote recebido
`@nroLotesTotal = 0`
 
### 2) Regra 2
Número de lotes na fila acima de limite
`@nroLotesFila > @threshold_lotesFila` 

### 3) Regra 3
Porcentagem mínima de lotes processados com sucesso ou falha de schema em relação ao total de lotes

```
(@nroLotesProcessadosSucesso + @nroLotesProcessadosFalhaSchema) < (@nroLotesTotal * 
@threshold_loteSucesso)
```

### 4) Regra 4
Porcentagem acima de limite de lotes processados com falha de schema em relação ao total de lotes
`@nroLotesProcessadosFalhaSchema > (@nroLotesTotal * @threshold_loteFalhaSchema)` 
 
### 5) Regra 5
Data de recepção de último lote acima de limite em minutos
`@dataRecepcaoUltimoLote < DATEADD(MINUTE, @threshold_ultimoLote, @timestamp)` 
 
### 6) Regra 6
Tempo médio de autorização acima de limite em segundos
`@tempoMedioAutorizacao > @threshold_tempoMedio` 
 
### 7) Regra 7
Porcentagem de notas rejeitadas acima de limite E número de contribuintes com notas rejeitadas acima de limite:

```
@nroNFesRejeitadas > (@nroNFesTotal * (1 - @threshold_autorizadaSucesso))
AND 
@nroContribuintesFracasso >= (@nroContribuintesSucesso + @nroContribuintesFracasso) * @threshold_ContribuintesComFalha
```

Além disso, a média de rejeições (notas / contribuintes) tem que ser superior a N desvios padrão de valores de mesmo dia/horário de semanas anteriores (N = @threshold_regraRejeitados) 
 
### 8) Regra 8
Data de recepção de último protocolo acima de limite em minutos

```
@dataUltimoProtocoloValido < DATEADD(MINUTE, @threshold_protocoloValido, 
@timestamp)
```

 
### 9) Regra 9
Data de recepção da última emissão acima de limite em minutos

```
@dataUltimaEmissaoProcessada < DATEADD(MINUTE, @threshold_emissaoProcessada, 
@timestamp)
```

 
### 10) Regra 10
Data de recepção do último cancelamento acima de limite em minutos

```
@dataUltimoCancelamentoProcessado < DATEADD(MINUTE, @threshold_cancProcessado, 
@timestamp)
```

 
### 11) Regra 11
Data de recepção da última inutilização acima de limite em minutos

```
@dataUltimaInutilizacaoProcessada < DATEADD(MINUTE, @threshold_inutProcessada, 
@timestamp)
```


### 12) Regra 12
Data de processamento do último evento para SP

```
@dataUltimoEventoSPProcessado < DATEADD(HOUR, @threshold_eventoSPprocessado, 
@timestamp)
```

 

## Queries
Último status da Sonda:

```
SELECT TOP 10 timestampReg, nfeAtiva, regraQueFalhou
FROM NFe_Out.NFeOut.SondaMonitoracaoSefaz WITH (NOLOCK)
ORDER BY PKEY DESC
```


Valores dos thresholds:

```
SELECT parametro, valor, descricao
FROM NFe_Out.NFeOut.ConfigSistema
where parametro like 'SONDA%'
```


## Parâmetros necessários para ativar cada regra

| **regra**                         | **parâmetros necessários para ativar**  |
|-----------------------------------|-----------------------------------------|
| 1 | NA |
| 2 | SONDA_THRESHOLD_LOTESFILA > 0 valor muito grande desliga a regra. |​
| 3 | SONDA_THRESHOLD_LOTESUCESSO entre 0 e 1, sendo que 0 desliga a regra e 1 exige todos os lotes com sucesso para não ativar |​
| 4 | SONDA_THRESHOLD_LOTEFALHASCHEMA entre 0 e 1, sendo que 1 desliga a regra e zero exige nenhuma falha de schema para não ativar |​
| 5 | SONDA_THRESHOLD_ULTIMOLOTE < 0 valor muito alto (em módulo) desliga a regra |​
| 6 | SONDA_THRESHOLD_TEMPOMEDIO > 0 valor muito grande desliga a regra |​
| 7 | SONDA_THRESHOLD_AUTORIZADASUCESSO valor entre 0 e 1, 0 desliga a regra, 1 exige nenhuma nota rejeitada para não ativar |​
|   | SONDA_THRESHOLD_CONTRIBUINTESCOMFALHA valor entre 0 e 1, 1 desliga a regra, 0 exige nenhum contribuinte com falha (nota rejeitada) para não ativar |​
|   | SONDA_THRESHOLD_REGRA_REJEITADOS valor padrão = 3, valor muito alto desliga a regra |​
| 8 | SONDA_THRESHOLD_PROTOCOLOVALIDO < 0 valor muito alto (em módulo) desliga a regra |​
| 9 | SONDA_THRESHOLD_EMISSAOPROCESSADA  < 0 valor muito alto (em módulo) desliga a regra |​ 
| 10 | SONDA_THRESHOLD_CANCELAMENTOPROCESSADO  < 0 valor muito alto (em módulo) desliga a regra|​
| 11 | SONDA_THRESHOLD_INUTILIZACAOPROCESSADA  < 0 valor muito alto (em módulo) desliga a regra |​	
| 12 | SONDA_THRESHOLD_EVENTOSSPPROCESSADO  < 0 valor muito alto (em módulo) desliga a regra |



​
