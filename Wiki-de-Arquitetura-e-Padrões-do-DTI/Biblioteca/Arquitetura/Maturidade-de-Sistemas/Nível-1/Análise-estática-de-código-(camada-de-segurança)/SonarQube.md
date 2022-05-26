# Em desenvolvimento

## Executando o SonarQube Localmente

Para analisar aplicações através do "SonarScanner for MSBUILD" deve-se fazer o download dos componentes de acordo com as configurações do projeto, no exemplo a seguir será realizado o teste com .NET Framework 4.6+.

Segue link do download: https://docs.sonarqube.org/latest/analysis/scan/sonarscanner-for-msbuild/ 

![image.png](/.attachments/image-fdd01a4f-6840-453b-a6ac-818dd92d8724.png)

Após realizar o download do SonarScanner, abra a aba de linguagens para realizar o download do plugin de acordo com a linguagem utilizada no projeto, o exemplo a seguir foi realizado com C# na ultima versão.

![image.png](/.attachments/image-f640c74b-2618-4e91-8b62-18cbe36dcbc3.png)

Após realizar o download dos componentes deve-se incluir os plugins dentro da pasta sonnar-scanner.

![image.png](/.attachments/image-872b3680-1c56-4ed5-96fa-2fd95bbddd1e.png)

Adicione o caminho da pasta Sonar-Scanner das configurações de "editar as variáveis de desenvolvimento do sistema", edite a opção path e adicione o caminho mapeado da pasta Sonar-Scanner:
![image.png](/.attachments/image-42cc8b8e-c5ad-4e97-b745-cf86b781c4b3.png)

Clique em OK
![image.png](/.attachments/image-587279d6-2ad6-44bf-9629-ad221ca7d483.png)

Acesse a console do SonnarQube através do link: http://srv11648:9000/sonar/

Crie uma chave de autenticação para realizar a análise de forma autenticada.
![image.png](/.attachments/image-737f73ef-d78d-45c3-ab07-95d841f84761.png)

Clique na aba "Security" e "Generate Tokens", insira uma informação e clique em "Generate", salve a chave.
![image.png](/.attachments/image-bf93f8ec-acc5-4ff8-9ab3-325e61bac510.png)

Abra o PROMPT CMD e mapeie o caminho do projeto:
![image.png](/.attachments/image-29868749-1740-49ad-8ef9-d7d64ad86330.png)

Insira o seguintes comando com a chave de autenticação do projeto no cmd:

`SonarQube.Scanner.MSBuild.exe begin /k:"MVC-Layout" /d:sonar.host.url="http://srv11648:9000/sonar/" /d:sonar.login="a34d0cdcc5b4d79aba6bac20f8f5b7168d82d5f5"  /d:sonar.verbose=true`

/k"": Nome do projeto
/d:sonar.host.url="": Url do SonnarQube
/d:sonar.login="": Chave gerada de autenticação do projeto
/d:sonar.verbose=: Modo verboso na tela
![image.png](/.attachments/image-4524d02a-3653-4036-8cfd-bd1dc2f5265e.png)

Insira novamente os comandos de Rebuild, o exemplo abaixo foi executado com o visual studio 2019:
"C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\MSBuild\Current\Bin\MSBuild.exe" /t:Rebuild
![image.png](/.attachments/image-b3dbf5fc-9888-4f7a-a862-c6d97791dfe8.png)
![image.png](/.attachments/image-48119551-6d5b-417d-9a90-74b664ee586b.png)

SonarScanner.MSBuild.exe end /d:sonar.login="a34d0cdcc5b4d79aba6bac20f8f5b7168d82d5f5"
![image.png](/.attachments/image-64f2c0d7-be6c-46a3-afa0-eafbad3ed730.png)
![image.png](/.attachments/image-5c2dfd96-e781-4296-9b9d-0c4bb9926c00.png)

dotnet restore 
nuget  restore