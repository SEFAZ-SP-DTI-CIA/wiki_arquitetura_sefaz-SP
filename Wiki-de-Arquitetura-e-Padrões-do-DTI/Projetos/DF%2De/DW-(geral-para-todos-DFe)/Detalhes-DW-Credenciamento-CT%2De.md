# Atividades realizadas na migração para cluster novo

1) Cópia do disco do cluster, incuindo diretórios "ExtratorCredenciamentoCTe-Producao" e "FilesExtratorCredenciamentoCTe"

1) Alteração do projeto no [TFS](https://ads.intra.fazenda.sp.gov.br/tfs/TPC-2010/DFeDW/_versionControl?path=%24%2FDFeDW%2FMain%2Fv1.0.0.0%2FCTeCredenciamentoDW) para compilar em plataforma x64 copiar libs específicas do cliente Sybase:

`xcopy /y $(SolutionDir)LIB\dbcon16.dll $(TargetDir)`
`xcopy /y $(SolutionDir)LIB\dblgen16.dll $(TargetDir)`
`xcopy /y $(SolutionDir)LIB\policy.16.0.iAnywhere.Data.SQLAnywhere.v4.0.dll $(TargetDir)`

1) Remoção das referências a "DISK19" no arquivo `O:\ExtratorCredenciamentoCTe-Producao\LayoutExtrator.xml` , que controla a execução do extrator.

1) Cópia da pasta C:\Tools para cada uma das máquinas do cluster

1) Criação de nova tarefa no Task Scheduler do Windows (opção "Create Task"), com as seguintes configurações:

![image.png](/.attachments/image-98efc10c-97fa-4677-b52d-371b9ee6ce55.png)

![image.png](/.attachments/image-79244cc8-79ce-455b-80cc-8ca7c0167bd2.png)

![image.png](/.attachments/image-62148b77-b5a7-403d-8895-5d7353cd69ea.png)

Program: `O:\ExtratorCredenciamentoCTe-Producao\CTeCredenciamentoDW.Extrator.exe`
Arguments: `ExecutaProcessamento XMLExtracao="O:\ExtratorCredenciamentoCTe-Producao\LayoutExtrator.xml" ExtrairTodosLayouts=Sim`

![image.png](/.attachments/image-21025a26-4812-41a5-adcb-b98b415239b6.png)

![image.png](/.attachments/image-d026f4c0-06f5-4987-842e-9b1311eb30e6.png)

1) No cluster antigo, foi desabilitada a execução através da desabilitação do parâmetro `enable` na tarefa "Extrator CredenciamentoCTe" do Task Scheduler

(duplo clique no nome da tarefa)
![image.png](/.attachments/image-5fb3636c-94d6-4737-816c-37fa30045e7a.png)

(duplo clique novamente no nome da tarefa)
![image.png](/.attachments/image-76776c47-4a8f-41e3-952d-522e9180a0f4.png)

(na aba "Trigger", clicar em "Edit...")
![image.png](/.attachments/image-27cd7b46-84ef-40f5-a2c6-4f0130b42fd2.png)

(clicar em "Enlabled" para desabilitar, caso esteja habilitado)
![image.png](/.attachments/image-8ee28ca6-5695-4b06-8295-32fd475c9227.png)



