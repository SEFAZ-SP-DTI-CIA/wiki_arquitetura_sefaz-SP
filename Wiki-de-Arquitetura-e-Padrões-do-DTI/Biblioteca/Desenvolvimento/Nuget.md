Configuração do SefazFeed e Nuget.org por linha de comando:

```
nuget sources add -name SefazFeed -Source https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/_packaging/SefazFeed/nuget/v3/index.json
nuget sources add -name nuget.org -Source https://api.nuget.org/v3/index.json
```

Configuração de Proxy por linha de comando:

```
nuget config -set http_proxy=http://10.217.20.52:8080
nuget config -set http_proxy.user=intra\cs_git_servico
nuget config -set http_proxy.password=git_cia
```

Se você não tiver o nuget instalado, use comandos nuget do dotnet, por exemplo: `dotnet nuget`


Exemplo do arquivo de configurações do Nuget
C:\Users\ <nome do seu usuario> \AppData\Roaming\NuGet\NuGet.Config

```
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <packageSources>
    <add key="SefazFeed" value="https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/_packaging/SefazFeed/nuget/v3/index.json" />
  </packageSources>
  <config>
    <add key="http_proxy" value="http://10.217.20.52:8080" />
    <add key="http_proxy.user" value="intra\cs_git_servico" />
    <add key="http_proxy.password" value="" />
  </config>
</configuration>
```