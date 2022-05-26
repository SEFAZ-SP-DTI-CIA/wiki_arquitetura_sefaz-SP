# Lista de comandos úteis para Cluster DW DFe

## Todos os comandos devem ser executado em Powershell aberto como Admin.

1) Comandos para parar e voltar algum serviço específico no cluster (_<Nome Windows Service>_ está listado na Tabela 1):

**> Stop-ClusterResource** _"<Nome Windows Service>"_
**> Start-ClusterResource** _"<Nome Windows Service>"_

2) Comandos para obter a lista atualizada de Windows Services:

**> Get-ClusterResource** _#esse comando lista TODOS os serviços, não apenas do DW DFe_
**> Get-ClusterResource | Select-String -Pattern Sefaz** _#o Select-String filtra, mostrando apenas os serviços "Sefaz"_

3) Comandos para chavear cluster:

**> Get-ClusterGroup** _#esse comando lista os grupos no cluster, é útil para saber em qual nó está ONLINE:_


```
Name                 OwnerNode State
----                 --------- -----
Available Storage    SRV11336  Online
Cluster Group        SRV11337  Online
EXTRATORDW           SRV11336  Online
SQL Server (NFEDW01) SRV11337  Online
SQL Server (NFPDW01) SRV11337  Online
```

**> Move-ClusterGroup EXTRATORDW** _#move todos os serviços do grupo (Tabela 1) da SRV11336 para SRV11337 e vice-versa_

### i) O chaveamento do cluster pode ser executado de qualquer máquina do cluster, não precisa estar na máquina ativa.
### ii) Nâo precisa passar o nome do servidor como parâmetro, o chaveamento vai para a outra máquina se estiver disponível. Se mantiver na mesma é porque falhou o chaveamento.

- **Tabela 1** (Dados detalhados de cada serviço)


|Nome Sistema  |​Nome Windows Service  |Pathname  |
|--|--|--|
|​CCC |Sefaz.DFeDW.CCCExtratorService |​O:\Disk19\Sefaz.DFeDW.CCCExtratorService |
|​MDF-e |Sefaz.Dw.MDFeService |O:\Disk19\Sefaz.DFeDW.MDFeExtratorService |
|​NF-e |Sefaz.NFe.Dw.Service |​O:\Disk19\Sefaz.DFeDW.NFeExtratorService |
|​CT-e |​Sefaz.ODS.Extratores.CTeExtratorService |​O:\Disk19\Sefaz.ODS.Extratores.CTeExtrator |
|​CT-eOS |​Sefaz.ODS.Extratores.CTeOSExtratorService |​O:\Disk19\Sefaz.ODS.Extratores.CTeOSExtrator |
|​BP-e |BPeExtratorService |O:\Disk19\Sefaz.DFeDW.BPeExtratorService |
|​GTV-e  |	|​​O:\Disk19\Sefaz.ODS.Extratores. |
|​BP-eTM |​	|​O:\Disk19\Sefaz.ODS.Extratores.  |
	
	
