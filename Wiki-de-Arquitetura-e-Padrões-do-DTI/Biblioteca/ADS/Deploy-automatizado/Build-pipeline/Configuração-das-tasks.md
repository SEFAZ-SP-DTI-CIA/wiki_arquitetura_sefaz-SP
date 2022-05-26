Para incluir uma task em um pipeline, na tela de edição de Task, clique no botão + ao lado de “Agent job 1”.
>![Incluir_Task.png](/.attachments/Incluir_Task-a75821eb-bd47-46c1-a4f0-bd369e4a7ec4.png =400x)

#Tasks básicas
Cada tipo de aplicação exige um conjunto diferente de tasks.
##Dotnet Core
Para aplicações dotnet core, há um template que adiciona a task do tipo “.NET Core” diversas vezes, cada uma com uma configuração diferente, que farão a chamada powershell do comando dotnet com argumentos e parâmetros distintos.
- Restore
    - Feeds to use: 
      `Feeds in my Nuget.config`
    - Path to NuGet.config: `D:\Nuget\nuget.config`
- Build
  - Task version: `2.*`
  - Arguments: 
`--configuration $(BuildConfiguration)`
`--no-restore`
- Publish
  - Task version: `2.*`
  - Arguments
`--configuration $(BuildConfiguration)` 
`--output $(build.artifactstagingdirectory)` 
`--no-restore` 
`--no-build`
  - Zip Published Projects: `Marcado`
> [Veja um exemplo em...](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Layout_Padrao/_build)

##Kubernetes (k8s) e linux
Para os artefatos poderem ser utilizados em deploy automatizado em plataforma k8s, é necessário que se incluam os arquivos dockerfile e kubernetes, como no exemplo da aplicação [modelo_core_mvc](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Layout_Padrao/_git/modelo_core_mvc?path=%2Fmodelo_core_mvc%2Fdocker) e [modelo_core_webapi](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Layout_Padrao/_git/modelo_core_webapi?path=%2Fdocker).
No pipeline da [aplicação modelo_core_mvc](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Layout_Padrao/_apps/hub/ms.vss-ciworkflow.build-ci-hub?_a=edit-build-definition&id=36&view=Tab_Tasks), por exemplo, incluem-se uma task "Copy files" e uma task "Replace token" no pipeline de build, além do cadastramento das [variáveis de substituição relativas aos tokens](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Layout_Padrao/_apps/hub/ms.vss-ciworkflow.build-ci-hub?_a=edit-build-definition&id=36&view=Tab_Variables) que estão nos arquivos.


##Dotnet FW
Aplicações asp.net requerem duas tasks para uso do nuget e uma task para build e publish.
- NuGet
  - Task version: `2.*`
  - Path to solution, packages.config, or project.json – Selecione uma solution, não um projeto.
  - Feeds to use: 
      `Feeds in my Nuget.config`
  - Path to NuGet.config: `D:\Nuget\nuget.config`
- Visual Studio Build
  - Solution – Selecione uma solution ou apenas um projeto, conforme a estratégia . Por exemplo:
`src/web/AppPiloto.WebForms/AppPiloto.WebForms.csproj`
`src/api/AppPiloto.Api/AppPiloto.Api.csproj`
`src/web/AppPiloto.Web.Forms/AppPiloto.Web.Forms.vbproj`
  - Platform – Informe a variável:
`$(BuildPlatform)` 
  - Configuration – Digite a variável:
`$(BuildConfiguration)`
  - MSBuild Arguments – Inclua os parâmetros :
`/p:DeployOnBuild=true` 
`/p:WebPublishMethod=Package` 
`/p:PackageAsSingleFile=true` 
`/p:SkipInvalidConfigurations=true` 
`/p:DesktopBuildPackageLocation="$(build.artifactstagingdirectory)\\"` 
`/p:AutoParameterizationWebConfigConnectionStrings=false` 

#Publish Artifact
Qualquer que seja a aplicação, ao final, os arquivos necessários ao deploy, chamados de artefatos, deverão ser copiados para um local acessível ao pipeline de release, que pode ser uma pasta compartilhada de rede ou o próprio banco de dados do Azure, em um local chamado de "Azure pipelines".
A task "Publish Artifact" faz essa cópia dos artefatos.

- Publish Build Artifacts
  - Path to Publish
`$(build.artifactstagingdirectory)`
  - Artifact Name
`drop`
  - Artifact publish location - use, preferencialmente : 
`Azure Pipelines`

> Os artefatos gerados pelo pipeline de build no ADS devem ser publicados em "**Azures Pipelines**", não mais na BMD.
Isso permite um melhor controle de retenção dos artefatos, impede que os arquivos possam ser alterados manualmente e melhora a rastreabilidade de eventuais falhas.

