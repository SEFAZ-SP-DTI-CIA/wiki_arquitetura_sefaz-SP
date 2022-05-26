# Desenho alto nível EPEC

![NFCe_EPEC.png](/.attachments/NFCe_EPEC-8ff6b3a3-2425-42db-8482-d091a44c0362.png)

(Clique no link -> [Modelo_AltoNivel_v2.zip](/.attachments/Modelo_AltoNivel_v2-f74e51c7-56ce-4e8a-837d-56c08846bd4f.zip)​ para fazer download do desenho acima para edição no Visio).
Será necessário descompactar, para acessar o arquivo "Modelo_AltoNivel_v2.vsdx".


## I) Envio de EPEC
1) contribuinte submete EPEC​
2) validações:
   2.1) contribuinte não pode ter “cnpjEmit” na tabela [EPEC][ContribuintesBloqueados]
   2.2) não pode existir EPEC de mesmo CNPJ, Nro, Série em tabela [EPEC][EPEC_Constraint]
   2.3) não pode existir NFC-e de mesmo CNPJ, Nro, Série em tabela RO [NFCe_Out_Constraint][NFCe_Constraint]
   2.4) outras, conforme NT
3) Evento é gravado em tabelas EPEC (inclusive [EPEC][ConciliacaoPendente])
4) Dado replicado para site NFC-e (Read Only)


## II) Envio de NFC-e
5) contribuinte submete NFC-e tpEmis = 4 
6) validações:
  6.1) deve existir EPEC com mesmo CNPJ, Nro, Série em [EPEC][EPEC_Constraint] (caso de erro: cStat=468)
  6.2) caso exista EPEC, os valores de vNF, vICMS, dhEmis e destinatário devem ser iguais ao do EPEC na tabela RO [EPEC][DadosEPEC] (caso de erro: cStat=467)
  6.3) outras, conforme NT
7) Nota é gravada em bancos NFC-e
8) Dado replicado para site EPEC (Read Only)


## III) EPEC Windows Service (Conciliação)

_//Obs: a execução desse serviço não irá impactar a autorização de EPEC porque quando o EPEC rodar, a NFC-e estará fora do ar, e vice-versa
//       No caso hipotético de no futuro o EPEC rodar com concorrência intensiva em relação à NFC-e,
//       algumas sugestões de mitigação do stress na tabela Conciliação Pendente:
//       1) não inserir em ConciliacaoPendente durante a autorização (fazer isso no serviço de conciliação, após conciliar as NFC-e recebidas no dia)
//       2) rodar o serviço de conciliação somente quando rodar o serviço de bloqueio/desbloqueio
//       3) para evitar que a lista de EPECs pendentes de conciliação pareça desatualizada, rodar por CNPJ antes de apresentar ao contribuinte ou no CDU04_


### (execução online)
### (rodando em São Paulo)

### Alternativa 1: copiando NFCe_Constraint para base do EPEC


```
9.1) Ler da tabela [EPEC][ConfigSistema]:
    - parâmetro "UltimaNFCeVerificada" (int)
```


```
9.2) Copiar novas notas submetidas ([NFCe_Out_Constraint][NFCe_Constraint] com "fkProtocolo" > "UltimaNFCeVerificada")
       para tabela [EPEC][NFCe_Constraint_Temp]
```

```
9.3) Desmarcar pendência caso haja EPEC conciliado:
       DELETE FROM [EPEC][ConciliacaoPendente] 
       WHERE pKey IN
       (
          SELECT epec.pKey
          FROM [EPEC][NFCe_Constraint_Temp] nfce
          JOIN [EPEC][EPEC_Constraint] epec
          ON  nfce.cnpjEmit   = epec.cnpjEmit
          AND nfce.numero     = epec.numero
          AND nfce.serie      = epec.serie
          AND nfce.idReceptor = epec.idReceptor
          JOIN [EPEC][ConciliacaoPendente] conc
          ON  conc.pKey   = epec.pKey
       )
```


`9.4) Limpar tabela temporária (TRUNCATE TABLE [EPEC][NFCe_Constraint_Temp])`

_9.5) Atualizar tabela [EPEC][ConfigSistema] com novos valores de_ "UltimaNFCeVerificada"

_//* Renata a confirmar: 
1) qual melhor modo de copiar dados da NFC-e constraint para o banco de EPEC_


_* O CDU04 permitirá apagar entradas de [EPEC][ConciliacaoPendente]​_


## IV) EPEC Windows Service (Bloqueio de CNPJ para submissão de EPEC)

### (execução de acordo com a demanda da DI)
### (rodando em São Paulo)

### Alternativa 1: recriando o conteúdo da tabela e usando synonym

### 10.1) Para cada EPEC pendente de conciliação
##    10.1.1) Inserir CNPJ em tabela staging 01 ou 02 alternadamente
         
```
     SELECT epec.cnpjEmit
     FROM  [EPEC] [EPEC_Constraint] epec 
       JOIN [EPEC][ConciliacaoPendente] conc
       ON epec.pKey = conc.pKey
       WHERE epec.timestampReg < DATEADD(day, getdate(),-PrazoEmDiasParaConciliacao)
     INTO [EPEC][ContribuintesBloqueados_01/02]
```


##    10.1.2) Mudar o synonym para apontar para a nova versão e apagar a versão anterior
         
         
```
BEGIN TRAN
  DROP SYNONYM [EPEC][ContribuintesBloqueados]
  CREATE SYNONYM [EPEC][ContribuintesBloqueados] FOR [EPEC][ContribuintesBloqueados_01/02]
COMMIT TRAN

TRUNCATE TABLE [EPEC][ContribuintesBloqueados_02/01]
```

&nbsp;

### Alternativa 2: usando NOT IN 

_//não recomendado pela Microsoft, devido a desempenho ruim (tempo de consulta com NOT IN)
//pode ser viável devido a tabela de CNPJs bloqueados não ser muito grande e rodar só à noite_

&nbsp;

```
10.0) Ler da tabela [EPEC][ConfigSistema]:
    - parâmetro "PrazoEmDiasParaConciliacao" (int)

10.1) Para cada CNPJ na tabela [EPEC][ContribuintesBloqueados]
   10.1.1) remover registro se não existir EPEC com Conciliação Pendente:
       DELETE FROM [EPEC][ContribuintesBloqueados] cb
       WHERE cb.cnpj NOT IN
       (
         SELECT epec.cnpjEmit
         FROM [EPEC] [EPEC_Constraint] epec 
           JOIN [EPEC][ConciliacaoPendente] conc
           ON epec.pKey = conc.pKey
       )
10.2) Para cada EPEC pendente de conciliação
   10.2.1) Inserir CNPJ na tabela [EPEC][ContribuintesBloqueados]
         SELECT epec.cnpjEmit
         FROM [EPEC] [EPEC_Constraint] epec 
           JOIN [EPEC][ConciliacaoPendente] conc
           ON epec.pKey = conc.pKey
           WHERE epec.timestampReg < DATEADD(day, getdate(),-PrazoEmDiasParaConciliacao)​
         INTO [EPEC][ContribuintesBloqueados]
```


&nbsp;

# ​​Modelo de dados EPEC

## Nome_do_Banco

```
|_ Schema.Nome_da_Tabela
|            nomeDaChave
|            nomeDoCampo (comentário)
|            nomeDaForeignKey
|            nomeDoCampo(indice não cluster)
```



## NFCe_EPEC_Constraint

```
|_ EPEC.EPEC_Constraint
|            pKey (mesma de protocolo)
|            cnpjEmit
|            numero
|            serie
|            ​idReceptor
|            ano
|            cStat
|            timestampReg​
|_ EPEC.DadosEPEC
|            pKey (mesma de protocolo)
|            valorTotal
|            dhEmi
|            valorICMS
|            ieEmit
|            idDest
|            tpDest
|            ufDest
|            timestampReg
|            cnpjEmit (fazer join com EPEC_Constraint)
|            numero (fazer join com EPEC_Constraint)
|            serie (fazer join com EPEC_Constraint)
|            idReceptor (fazer join com EPEC_Constraint)
|            modelo (será sempre 65)
|            ufEmit (será sempre 35)
​|_ EPEC.ConfigSistema
|            pKey
|            parametro
|            valor
|            ​descricao
```


## NFCe_EPEC

```
|_ EPEC.ContribuintesBloqueados
|            ​cnpj
|_ EPEC.ConciliacaoPendente
|            pKey (mesma de protocolo)
|_ EPEC.Protocolo
|            pKey
|            idReceptor
|            ufProtocolo
|            ​anoProtocolo
|            ​nroProtocolo
|            tpDocumento (será sempre tipo 2-evento)
|            ​tpAmb
|            ​verAplic
|            ​timestampOffsetReg
|            ​timestampReg
|            ​versao  (adicionado pelas mudanças da NT 2016.002 v1.00
|_ EPEC.Certificado
|            pKey
|            thumbprint
|            dataExpiracao
|            ​pkeyLCR
|_ EPEC.ContribuintesBloqueadosLog
|_ EPEC.LogCadespConsulta
|_ EPEC.DadosTransmissao
```


## NFCe_EPEC_XML

```
|_ EPEC.Certificado
|            ​pKey
|            ​certificado
|            ​timestampReg
|_ EPEC.CaixaDeEntrada (detalhar)
```


&nbsp;

## Bancos da NFC-e (somente leitura)

## NFCe_Out (para consulta de LCR, rever)
**|_ LCR.Certificado**
**|_ LCR.ListaRevogados**

## NFCe_Out_Constraint
**|_ NFCeOut.ContribuintesHabilitados**
**|_ NFCeOut.NFCe_Constraint**
