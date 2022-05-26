Na página de pipelines do projeto, criam-se builds pipelines ao clicar em «New pipeline». 
Recomenda-se que se utilize o editor clássico do Azure, clicando-se no link “Use the classic editor to create a pipeline without YAML”.

>![New_Build.png](/.attachments/New_Build-7dd85b65-1067-430a-aff0-dff97e4c7768.png =400x)

Seleciona-se o repositório e clica-se em «Continue». 
>![Repositorio_New_Build.PNG](/.attachments/Repositorio_New_Build-89e47cd3-a068-4419-90e3-98e00d38e885.PNG =400x)

A página oferecerá diversos modelos de definições de build para que você não precise começar do zero. Escolha um template que mais se aproxima do seu tipo de projeto ou clique em “Empty Job”.
>![Template_build.png](/.attachments/Template_build-123b4f20-f3f9-4360-9bfe-2348efc8165a.png =500x)
_Essa wiki aborda pipelines de build do template “ASP.NET” e “ASP.NETCore”._

Criado o build pipeline, selecione:
1. Agent Pool: Visual Studio
1. Project(s) to restore and build: Selecione uma solution (ASP.NET) ou um projeto (dotnet core). 
O que for selecionado aqui ficará ligado a algumas das tasks do pipeline.
>![Pipeline_build.PNG](/.attachments/Pipeline_build-5efe85db-da63-4ca3-adfe-ecf931254c6c.PNG)

Para alterar o nome da build, posicione o cursor do mouse sobre o nome e clique sobre o ícone que aparece:
>![Rename_Pipeline.png](/.attachments/Rename_Pipeline-fe897433-83e0-4adc-baf8-c72d20ab6f76.png =400x)

Se precisar alterar o repositório, modifique os valores da página "Get sources".
Após a criação do pipeline, segue-se a configuração das tasks.
 
