# Deploy dos Windows Services de Extrator DW em Produção



## POLÍTICA DE BRANCHES E VERSIONAMENTO:

###     Mudanças não-emergenciais:

1) Desenvolver em branch dev
2) Antes de fazer o merge para branch main, atualizar a versão em todos os arquivos  AssemblyInfo.cs
   Atualizar a versão da seguinte forma:

       <mudança de plataforma>.<mudança épica>.<mudança incremental>.<mudança menor/bug fix emergencial>

3) Fazer merge para branch main

### Mudanças emergenciais (no caso de precisar desenvolver direto em branch main):
1) Desenvolver em branch main
2) Atualizar somente o último dígito da versão (<mudança menor/bug fix emergencial>), e somente onde necessário para rastreabilidade
3) Fazer merge-back para branch dev, assim que possível

### DEPLOY NOS SERVIDORES DE PRODUÇÃO 
(Os passos para o deploy são similares para os projetos: **NF-e, MDF-e, CCC, CT-e, CTeOS, BP-e):**

1) Logar na máquina SRV11336 ou SRV11337 (cluster SRVCS119)

2) Verificar qual máquina está ativa no Cluster (o jeito mais rápido é procurar o Drive `"O:\MountPoint\Disk19"`, que só aparece na máquina ativa do Cluster)

3) Ir para a pasta em `<Pathname>` (ver **Tabela 1**​); criar sub-pasta `<Pathname>\Backup\<AnoMesDia>\` (Ex.: ​​​`O:\Disk19\Sefaz.DFeDW.NFeExtratorService\Backup\20190627`)

4) Copiar os arquivos que serão alterados de `<Pathname>\` para `<Pathname>\Backup\<AnoMesDia>\`

5) Copiar o arquivo _log.txt_ (se houver) para a pasta `<Pathname>\logs\` ​, renomear para _log_<AnoMesDia>.txt_ e compactar

6) No POWERSHELL (como Admin), executar o comando abaixo (`<Nome Windows Service>` está listado na **Tabela 1**):

`> Stop-ClusterResource ”<Nome Windows Service>”`

7) Após confirmar que o serviço parou, copiar os novos arquivos para `<Pathname>\` 

8) No POWERSHELL (como Admin)​, executar o comando abaixo:

`> Start-ClusterResource ”<Nome Windows Service>​”`

9) Confirmar que o serviço voltou a funcionar (pelo Event Viewer ou log.txt, dependendo do serviço) 

- **Tabela 1** (Dados detalhados de cada serviço)


|**Nome Sistema**  |​**Nome Windows Service**  |**Pathname**  |
|--|--|--|
|CCC  |​​Sefaz.DFeDW.CCCExtratorService  |O:\Disk19\Sefaz.DFeDW.CCCExtratorService  |
|MDF-e  |​Sefaz.Dw.MDFeService  |O:\Disk19\Sefaz.DFeDW.MDFeExtratorService  |
|​NF-e  |Sefaz.NFe.Dw.Service  |O:\Disk19\Sefaz.DFeDW.NFeExtratorService  |
|​CT-e|Sefaz.ODS.Extratores.CTeExtratorService|O:\Disk19\Sefaz.ODS.Extratores.CTeExtrator|
|​CT-eOS|​Sefaz.ODS.Extratores.CTeOSExtratorService|​O:\Disk19\Sefaz.ODS.Extratores.CTeOSExtrator|
|​BP-e|​BPeExtratorService|​​​O:\Disk19\Sefaz.DFeDW.BPeExtratorService|

Obs: para obter a lista atualizada de Windows Services, executar o comando Powershell abaixo:
**> Get-ClusterResource** _#esse comando lista TODOS os serviços, não apenas do DW DFe_
**> Get-ClusterResource | Select-String -Pattern Sefaz** _#o Select-String filtra, mostrando apenas os serviços "Sefaz"_
​

## REEXTRATORES DW

Pathname no disco O:\Disk19


|Nome Sistema  |​Windows Service​​  |Windows Form  |
|--|--|--|
|CCC  |NA  |NA  |
|MDF-e |O:\Disk19\ReExtrator_NFeDW\WindowsFormO:\Disk19\ReExtrator_MDFeDW |O:\Disk19\ReExtrator_MDFeDW\MDFe_Reextrator_Cockpit |
|NF-e |O:\Disk19\ReExtrator_NFeDW\WindowsService |O:\Disk19\ReExtrator_NFeDW\WindowsForm |
|CT-e |O:\Disk19\ReExtrator_CTeDW |O:\Disk19\ReExtrator_CTeDW\Cockpit  |
|​CT-eOS |NA |NA  |
	
Os reextratores DW são bem diferentes para cada projeto (NFe/MDFe/CT-e/etc). Alguns detalhes abaixo:

### Reextrator NF-e

- Logs: log de execução na tabela [ETL_NFE].[DWNFe].[Log] (essa mesma tabela é usada pelo extrator normal)

- Lista de chaves pendentes: [NFe_Out].[Extracao].[ChavesSybase] (essa tabela contém cópia das tabelas "ods.tb_dsod_nfe_reextracao" e "ods.tb_dsod_nfeuf_reextracao" do Sybase); o campo 'latest' tem os seguintes valores: 0 (pendente de processamento); 1 (em processamento); 2 (processado); Os registros com 'latest' = 0 e 'dataExtracao' preenchida não serão processados, pois a dataExtração indica que vieram do Sybase já com histórico de carga (foram carregados antes da cópia para o SQL Server).

### Reextrator MDF-e

- Logs: O:\Disk19\ReExtrator_MDFeDW\logs.txt

- Lista de chaves pendentes: NÃO USA (a carga é feita por faixa de dias, usando o Windows Form)

### Reextrator CT-e

- Logs: logs de execução na tabela [CTe_Out].[DW].[LogExecucao] (mesma tabela usada pelo extrator normal)

- Lista de chaves pendentes:  pode ser tanto por faixa de datas (no Windows Form) quanto por lista de chaves na tabela do Sybase : [ods].[tb_dsod_cte_reextracao]