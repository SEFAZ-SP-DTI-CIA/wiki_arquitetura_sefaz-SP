O ads oferece repositórios, chamados de _feed_, para armazenamento de pacotes que podem ser utilizados nas aplicações.
[[_TOC_]]

#SefazFeed
Os pacotes disponíveis podem ser vistos ao clicar no menu vertical no item "Artifacts", dentro de um projeto, por exemplo:
![image.png](/.attachments/image-f7891b16-443a-472d-a934-01b3675792c6.png =600x)

A Sefaz criou um feed em cada uma das collections do ads com o mesmo nome "SefazFeed".

Os pacotes Nuget do Sefaz Identity, com sua versão mais atualizada, estão no _feed_ da collection PRODUTOS, que pode ser acessado pelo endereço:
https://ads.intra.fazenda.sp.gov.br/tfs/PRODUTOS/Sefaz_Identity/_packaging?_a=feed&feed=SefazFeed

Os feeds das demais collections serão descontinuados.

##Configuração de segurança
Na configuração de permissão do feed de cada collection, para que possa ser utilizado por:
- pipelines - adiciona-se o projeto com "Allow project-scoped builds"
- desenvolvedores - foi adicionado o grupo AD "Domain Users" como "Contributors"

![FeedSettings.png](/.attachments/FeedSettings-5389e5b4-b4e8-441b-982b-92968742838a.png =600x)

![FeedSettings_users.png](/.attachments/FeedSettings_users-0fe975d6-6843-4533-af7b-a12ae054a604.png =500x)

>Se houver alguma dificuldade de acesso ao feed, solicite apoio através de [chamado no Suporte_Devops](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Suporte_DevOps).

#Nuget 
[NuGet](https://en.wikipedia.org/wiki/NuGet) (lê-se New Get) é um arquivo ZIP (com extensão .nupack ou .nupkg) que contém código dotnet compilado, muito usado como recurso para o Visual Studio e pipelines de build no Azure Devops.
Na maioria das vezes, contém bibliotecas de classes que são compartilhadas entre aplicações.

Há uma aplicação gratuita de código aberto chamada de _[nuget.exe](https://go.microsoft.com/fwlink/?linkid=2099732)_, que pode ser usada em linha de comando, CMD ou Power Shell, para criar e consumir esses pacotes.

Além dos pacotes criados e publicados pela Sefaz, podemos ter pacotes de terceiros em nosso SefazFeed, mas o principal repositório para pacotes Nuget na internet é o [nuget.org](https://www.nuget.org/).
Quando um pipeline no ads busca um pacote no nuget.org, ele pode gravar uma cópia dele no SefazFeed, para poder ser utilizado posteriormente, como se fosse um cache de pacotes, reduzindo a quantidade de buscas na internet e tornando o processo mais ligeiro.

##NuGet.config
Uma aplicação dotnet que utiliza nuget tem os nomes dos pacotes registrados no arquivo .csproj, por exemplo:
```
  <ItemGroup>
    <PackageReference Include="sefaz.identity.core" Version="1.0.0" />
    <PackageReference Include="System.ServiceModel.Http" Version="4.8.1" />
    <PackageReference Include="System.ServiceModel.NetTcp" Version="4.8.1" />
    <PackageReference Include="System.ServiceModel.Security" Version="4.8.1" />
  </ItemGroup>
```
Para sua execução, a aplicação precisa que esses pacotes sejam baixados das suas fontes, que podem ser definidas em um arquivo texto (XML) chamado [_NuGet.config_](https://docs.microsoft.com/en-us/nuget/reference/nuget-config-file).

Esse arquivo pode criado na mesma pasta da solution, para uso específico da aplicação e que fará parte do código, ou na pasta do usuário (`%userprofile%\AppData\Roaming\NuGet`) para ser usado apenas localmente por todas as aplicações no mesmo computador.

Na Sefaz, um arquivo nuget.config básico, que atende a maioria das aplicações e funciona em todas as collections, pode conter apenas:
```
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <packageSources>
    <add key="SefazFeed" value="https://ads.intra.fazenda.sp.gov.br/tfs/PRODUTOS/_packaging/SefazFeed/nuget/v3/index.json" />
    <add key="nuget.org" value="https://api.nuget.org/v3/index.json" protocolVersion="3" />
  </packageSources>
</configuration>
```

O DTI-CIA recomenda que se utilizem arquivos nuget.config nas aplicações, em vez do arquivo local, para que ele seja utilizado no pipeline de build.
No pipeline de build, se não houver esse arquivo, a task restore só poderá usar um único feed e que esteja na própria collection.

##Visual Studio
Para buscar pacotes nuget desenvolvidos e distribuídos pela Sefaz, o Visual Studio precisa ser configurado para acessar os feeds do ads.
Isso pode ser feito com a criação do nuget.config, como descrito acima, ou utilizando o menu "Tools - Nuget Package Manager - Package Manager Settings - Package Sources".

![image.png](/.attachments/image-c377f734-f325-469c-a3ac-5701d8f5770a.png =600x)

Na versão Professional ou Enterprise, o Visual Studio oferece uma interface gráfica para buscar, adicionar e gerenciar os pacotes nuget dos seus projetos, acessível no menu "Tools - Nuget Package Manager - Manage NuGet Packages for Solution...".

![image.png](/.attachments/image-1fba1428-68f3-4444-9270-d1bc50066992.png =600x)

##Nuget por linha de comando
Pacotes nuget podem ser administrados diretamente por linha de comando utilizando-se o _nuget.exe_, disponível em https://www.nuget.org/downloads.

Para conseguir acessar o SefazFeed por linha de comando, é necessário criar um [Personal Access Token (PAT), para todas as organizações](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/_usersSettings/tokens), apenas com permissão de leitura em packaging, e adicionar esse PAT ao arquivo nuget.config do usuário (`%userprofile%\AppData\Roaming\NuGet`) diretamente ou pelo comando:

```
nuget sources update -ValidAuthenticationTypes basic -Name SefazFeed -UserName [username] -Password [PAT]
```

Adiciona-se o SefazFeed da collection PRODUTOS pelo comando:
```
nuget sources add -Name SefazFeed -Source https://ads.intra.fazenda.sp.gov.br/tfs/PRODUTOS/_packaging/SefazFeed/nuget/v3/index.json
```

Pode-se visualizar as origens cadastradas diretamente no arquivo nuget.config ou pelo comando:
```
nuget sources list
```

Para excluir uma origem por linha de comando:
```
nuget sources remove -name nuget.org
```

O arquivo Nuget.Config deve ficar alguma coisa parecida com:

```
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <packageSources>
    <add key="SefazFeed" value="https://ads.intra.fazenda.sp.gov.br/tfs/PRODUTOS/_packaging/SefazFeed/nuget/v3/index.json" />
  </packageSources>
  <packageSourceCredentials>
    <SefazFeed>
        <add key="Username" value="XXXXX" />
        <add key="Password" value="XXXX" /> # precisa ser encriptada a senha OU
        <add key="cleartextpassword" value="xxxx" /> # senha aberta e visível
    </SefazFeed>
  </packageSourceCredentials>
</configuration>
```
Conforme a key "ValidAuthenticationTypes":
- basic - com um personal access token (PAT) criado no ADS no lugar do password
```
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <packageSources>
    <add key="SefazFeed" value="https://ads.intra.fazenda.sp.gov.br/tfs/PRODUTOS/_packaging/SefazFeed/nuget/v3/index.json" />
  </packageSources>

  <packageSourceCredentials>
    <SefazFeed>
        <add key="Username" value="[usuário do PAT]" />
        <add key="Password" value="[PAT]" /> 
        <add key="ValidAuthenticationTypes" value="basic" />
    </SefazFeed>
  </packageSourceCredentials>
</configuration>
```

> [Veja mais...](https://docs.microsoft.com/en-us/nuget/reference/nuget-config-file#packagesourcecredentials)

Configurado o nuget, podem se adicionar os pacotes disponíveis no SefazFeed ao projeto, por exemplo:
```
dotnet add package SefazIdentityTokenValidation
```
