[[_TOC_]]

#Sonda Monitoração NFC-e

A Sonda trata-se de um job que roda a cada dois minutos (atualmente está agendado no Control-M) e faz uma leitura ampla de informações contidas na base de dados da NFC-e.

O intervalo de dados lidos fica entre o momento da execução do job e os dois minutos imediatamente anteriores à execução.

O objetivo da sonda é usar as informações recuperada

s para a partir de uma série de comparações comportamentais retornar um bit chamado nfceAtiva.
Esse bit é utilizado pela monitoração como mais uma maneira de constatar que a NFC-e está com comportamento esperado ou não.

#Atualmente são recuperadas as seguintes informações: 

##1) Valores pegos de tabela temporária (últimos minutos)
- [nroLotesTotal] - total de lotes (síncronos e assíncronos)
- [nroLotesSincronos] - total de lotes síncronos
- [nroLotesAssincronos] - total de lotes assíncronos
- [nroLotesAguardando] - total de lotes com cStat = 103, aguardando processamento
- [nroLotesEmProcessamento] - total de lotes com cStat = 105, em processamento
- [nroLotesProcessadosSucesso] - total de lotes com cStat = 104, processados com sucesso
- [nroLotesProcessadosFalhaSchema] - total de lotes com cStat = 225, falha de schema XML
- [nroLotesProcessadosErro] - total de lotes com cStat diferente dos acima ([cStat] NOT IN (103,104,105,225))
- [dataRecepcaoUltimoLote] - data em que o último lote da leitura efetuada pelo job foi recebido
- [tempoMedioAutorizacao] - tempo médio de autorização dos lotes processados (no caso dos lotes assíncronos, o tempo de autorização considerado é a soma tempoRecebimento + tempoProcessamento)
- [nroNFCesAutorizadas] - total de NFC-e com cStat IN (100, 150), ou seja, autorizações normais e autorizações fora de prazo
- [nroNFCesDenegadas] - total de NFC-e com cStat = 301 (uso denegado: irregularidade fiscal do emitente)
- [nroNFCesInutilizadas] - total de NFC-e com cStat = 102
- [nroNFCesRejeitadas] - total de NFC-e que não se encaixa em nenhum outro grupo, ou seja, NOT IN (100, 102, 150, 301)
- [nroEventosCancelamento] - total de eventos de cancelamento registrados dentro do intervalo de corte da sonda
- [dataUltimoProtocoloValido] - data mais recente do um protocolo com cStat IN (100, 102, 150, 301)
- [nroContribuintesFracasso] - total de contribuintes que emitiram protocolo com cStat NOT IN (100, 102, 150, 301)
 
##2) Valores pegos de tabelas normais
- [nroLotesFila] - lotes na fila aguardando processamento (autorização assíncrona)
- [dataUltimaEmissaoProcessada] - data do registro mais recente da tabela NFCe
- [dataUltimaInutilizacaoProcessada] - data do registro mais recente da tabela InutNFCe
- [dataUltimoCancelamentoProcessado] - data do registro mais recente da tabela Evento
 
##3) Valores calculados
- [timestampReg] -  data da execução do job
- [nroNFCesTotal] - somatória de nroNFCesAutorizadas + nroNFCesDenegadas + nroNFCesInutilizadas + nroNFCesRejeitadas
- [ultimaPKeyNFCe] - max(pKey) registrado no intervalo de corte da sonda. Caso seja vazio retorna o último valor válido desse campo
- [nfceAtiva] - bit que resume o estado da NFC-e a partir de algumas comparações realizadas com as informações obtidas pela sonda
Status Atual das Regras do Bit NFCeAtiva
Para determinar o valor do bit NFCeAtiva existem algumas comparações no final da procedure da sonda.
Se qualquer uma das regras listadas abaixo for verdadeira, o bit é setado pra zero, ou seja, a NFC-e aparenta ter comportamento incomum:

#Lista de Regras da Sonda

##Valores que precisariam ser usados para anular as regras:

Obs:

MaxInt = 2147483647, mas devido a limitação do PortalFazendário, estamos usando 99999999 (100 milhões - 1)

MinInt = -2147483648,mas devido a limitação do PortalFazendário, estamos usando -9999999 (10 milhões  - 1)
 
Os valores MinInt e MaxInt anulam as regras "na prática", pois mesmo sendo matematicamente possível ter valores respectivamente menores ou maiores, as restrições de tempo e número de contribuintes limitam as leituras para valores muito abaixo dos limites teóricos.
 
1) Regra 1
@threshold_lotesTotalMinimo = 0
 
2) Regra 2
@threshold_lotesFila = MaxInt
 
3) Regra 3
@threshold_loteSucesso = 0
 
4) Regra 4
@threshold_loteFalhaSchema = 1
 
5) Regra 5
@threshold_ultimoLote = MinInt
 
6) Regra 6
@threshold_tempoMedio = MaxInt
 
7) Regra 7
@threshold_autorizadaSucesso = 0
ou
@threshold_ContribuintesComFalha = MaxInt
 
8) Regra 8
@threshold_protocoloValido = MinInt
 
9) Regra 9
@threshold_emissaoProcessada = MinInt
 
10) Regra 10
@threshold_cancProcessado = MinInt
 
11) Regra 11
@threshold_inutProcessada = MinInt
 
12) Regra 12
@threshold_EmissaoProcessadaNoHorario = ''

13) Regra 13
@threshold_ErrosDownloadLCR= MaxInt

14) Regra 14
@SONDA_THRESHOLD_MIN_PERCENT_TOTAL_LOTES = 0