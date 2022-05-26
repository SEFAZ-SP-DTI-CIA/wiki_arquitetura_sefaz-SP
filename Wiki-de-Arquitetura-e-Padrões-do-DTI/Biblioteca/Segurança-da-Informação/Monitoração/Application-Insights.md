Procedimentos para uso do [Application Insights](https://docs.microsoft.com/en-us/azure/azure-monitor/app/app-insights-overview#what-does-application-insights-monitor) na Sefaz.

[[_TOC_]]

#Solicitação
Um responsável pelo sistema (demandante) solicita por [chamado ao Suporte Devops - Template "Criar chave de Instrumentação"](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Suporte_DevOps/_wiki/wikis/Suporte_DevOps.wiki?pagePath=%2FSuporte%20DevOps%2FApplication%20Insights%20%252D%20Chave%20de%20Instrumenta%C3%A7%C3%A3o)  uma **Chave de Instrumentação** para utilização do Application Insights no [_Azure Devops Services_](https://portal.azure.com/).

#Grupo de recursos
Um membro do grupo "Desenvolvimento" do Suporte Devops toma o chamado e abre um incidente no Remedy solicitando a criação de um Grupo de Recursos, dentro do qual serão criadas as chaves de instrumentação.

```
Nome do Template/Sumário:	Criação de Grupos de Recursos no Azure Devops Service

Conteúdo de notas:	

    Projeto: [NOME DO PROJETO]
    Código da UA:

    Nome (ou sigla) da UA: [Nome UA]
    Nome Equipe: [NOME EQUIPE]

    Assinatura (subscription) e nome do grupo:
    . Desenvolvimento: RG-[Nome UA]-[NOME DO PROJETO]-DEV
    . Homologação: RG-[Nome UA]-[NOME DO PROJETO]-HML
    . Produção: RG-[Nome UA]-[NOME DO PROJETO]

    Roles:
    - Atribuir role de "owner" ao grupo SVC_azure_role_eqp-cap
    - Criar o grupo AD SVC_azure_role_eqp-[NOME EQUIPE]
      . Incluir, nesse grupo criado, os usuários:  [relação de logins]

Seleciona-se o Serviço relacionado ao template*: [procurar o nome do projeto no Remedy]
Cat1:	Aplicação
Cat2:	Acesso
Cat3:	Criar

Usuários Afetados:	Afeta sua Área / Equipe
Situação do Serviço:	Usuário Parcialmente Inoperante/ Um Serviço está Indisponível
```

#Criação da chave de instrumentação
Após a criação dos grupos de recursos, um membro do grupo SVC_azure_role_eqp-cap acessa o Portal Azure para a criação da chave de instrumentação:
https://portal.azure.com/

- Acessa-se "Resource Groups" e seleciona-se todas as subscriptions:
https://portal.azure.com/#blade/HubsExtension/BrowseResourceGroups
![image.png](/.attachments/image-236f61ae-98bf-40cb-9189-f68f01054ff5.png =500x)

- seleciona-se o grupo de recursos desejado e clica em "+ Create", para criar uma chave de instrumentação:
![image.png](/.attachments/image-d93e7204-2837-4411-a1dc-c04be2eb2a44.png =600x)

- Na página de criação de recursos, seleciona-se "Devops", "Application Insights" e clica-se em "Create":
![image.png](/.attachments/image-0947acf0-8305-4143-9993-85ebe5d4c3b6.png =500x)

- Preenchem-se as informações da Chave de Instrumentação:
![image.png](/.attachments/image-f90d0f66-e090-49e3-98b3-a38def78894b.png)

  - O nome da instância deve começar com a palavra "appInsigths-" seguido do nome do serviço que vai utilizar a chave de instrumentação. Nesse nome não pode haver espaços acentos, cedilhas ou caracteres especiais.
  - A Região deve ser qualquer uma no Brasil.

- Clica-se em "Review + Create". Se tudo estiver em ordem, clica-se em "Create".
- Acessa-se o novo recurso, seleciona-se "Access control (IAM)", "Role Assignments" e se verifica se o novo grupo "SVC_azure_role_eqp-[NOME EQUIPE]" está relacionado. Se não, reabre-se o incidente no Remedy, solicitando a inclusão.
- Copia-se e cola o link do recurso no chamado e o encerra.
![image.png](/.attachments/image-57cbe6fc-54b8-4b36-ba38-b5f96c4f72f5.png =700x)

#Configuração na aplicação
A monitoração usando uma chave de instrumentação pode ser feita em duas camadas de uma aplicação, uma no servidor em que está hospedado o serviço e outra na página web que está sendo exibida no navegador do usuário, as quais chamaremos nesse texto de "server" e "client".

Nos dois casos, para usar o Application Insights é necessário obter uma [chave de instrumentação do Application Insights através de chamado no Suporte_Devops](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Suporte_DevOps/_wiki/wikis/Suporte_DevOps.wiki/380/Application-Insights-Chave-de-Instrumenta%C3%A7%C3%A3o).

##Server
Para usar a telemetria em um projeto de desenvolvimento no lado do servidor (csproj), é encessário:
- Adicionar o "Application Insights Telemetry" no projeto
  - Clica-se com botão direito sobre o projeto, seleciona-se Add -> Application Insights Telemetry
  - Configura-se sem conectar ao Azure - Application Insights SDK (Local)
- Inserir a chave de instrumentação no arquivo appsettings.json ou no web.config

```
"ApplicationInsights": {
    "InstrumentationKey": "[chave de instrumentação]"
  },
```

- Alterar a classe ConfigureServices do Startup.cs para indicar a chave de instrumentação:
```
  services.AddApplicationInsightsTelemetry(Configuration["ApplicationInsights:InstrumentationKey"]);
```

Veja mais em:
https://docs.microsoft.com/en-us/azure/azure-monitor/app/app-insights-overview

##Client
Para configurar a aplicação para obter dados de telemetria no client, insere-se o script java nas páginas em que se deseja monitorar:
```
<script type="text/javascript">
!function(T,l,y){var S=T.location,k="script",D="connectionString",C="ingestionendpoint",I="disableExceptionTracking",E="ai.device.",b="toLowerCase",w="crossOrigin",N="POST",e="appInsightsSDK",t=y.name||"appInsights";(y.name||T[e])&&(T[e]=t);var n=T[t]||function(d){var g=!1,f=!1,m={initialize:!0,queue:[],sv:"5",version:2,config:d};function v(e,t){var n={},a="Browser";return n[E+"id"]=a[b](),n[E+"type"]=a,n["ai.operation.name"]=S&&S.pathname||"_unknown_",n["ai.internal.sdkVersion"]="javascript:snippet_"+(m.sv||m.version),{time:function(){var e=new Date;function t(e){var t=""+e;return 1===t.length&&(t="0"+t),t}return e.getUTCFullYear()+"-"+t(1+e.getUTCMonth())+"-"+t(e.getUTCDate())+"T"+t(e.getUTCHours())+":"+t(e.getUTCMinutes())+":"+t(e.getUTCSeconds())+"."+((e.getUTCMilliseconds()/1e3).toFixed(3)+"").slice(2,5)+"Z"}(),name:"Microsoft.ApplicationInsights."+e.replace(/-/g,"")+"."+t,sampleRate:100,tags:n,data:{baseData:{ver:2}}}}var h=d.url||y.src;if(h){function a(e){var t,n,a,i,r,o,s,c,u,p,l;g=!0,m.queue=[],f||(f=!0,t=h,s=function(){var e={},t=d.connectionString;if(t)for(var n=t.split(";"),a=0;a<n.length;a++){var i=n[a].split("=");2===i.length&&(e[i[0][b]()]=i[1])}if(!e[C]){var r=e.endpointsuffix,o=r?e.location:null;e[C]="https://"+(o?o+".":"")+"dc."+(r||"services.visualstudio.com")}return e}(),c=s[D]||d[D]||"",u=s[C],p=u?u+"/v2/track":d.endpointUrl,(l=[]).push((n="SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details)",a=t,i=p,(o=(r=v(c,"Exception")).data).baseType="ExceptionData",o.baseData.exceptions=[{typeName:"SDKLoadFailed",message:n.replace(/\./g,"-"),hasFullStack:!1,stack:n+"\nSnippet failed to load ["+a+"] -- Telemetry is disabled\nHelp Link: https://go.microsoft.com/fwlink/?linkid=2128109\nHost: "+(S&&S.pathname||"_unknown_")+"\nEndpoint: "+i,parsedStack:[]}],r)),l.push(function(e,t,n,a){var i=v(c,"Message"),r=i.data;r.baseType="MessageData";var o=r.baseData;return o.message='AI (Internal): 99 message:"'+("SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details) ("+n+")").replace(/\"/g,"")+'"',o.properties={endpoint:a},i}(0,0,t,p)),function(e,t){if(JSON){var n=T.fetch;if(n&&!y.useXhr)n(t,{method:N,body:JSON.stringify(e),mode:"cors"});else if(XMLHttpRequest){var a=new XMLHttpRequest;a.open(N,t),a.setRequestHeader("Content-type","application/json"),a.send(JSON.stringify(e))}}}(l,p))}function i(e,t){f||setTimeout(function(){!t&&m.core||a()},500)}var e=function(){var n=l.createElement(k);n.src=h;var e=y[w];return!e&&""!==e||"undefined"==n[w]||(n[w]=e),n.onload=i,n.onerror=a,n.onreadystatechange=function(e,t){"loaded"!==n.readyState&&"complete"!==n.readyState||i(0,t)},n}();y.ld<0?l.getElementsByTagName("head")[0].appendChild(e):setTimeout(function(){l.getElementsByTagName(k)[0].parentNode.appendChild(e)},y.ld||0)}try{m.cookie=l.cookie}catch(p){}function t(e){for(;e.length;)!function(t){m[t]=function(){var e=arguments;g||m.queue.push(function(){m[t].apply(m,e)})}}(e.pop())}var n="track",r="TrackPage",o="TrackEvent";t([n+"Event",n+"PageView",n+"Exception",n+"Trace",n+"DependencyData",n+"Metric",n+"PageViewPerformance","start"+r,"stop"+r,"start"+o,"stop"+o,"addTelemetryInitializer","setAuthenticatedUserContext","clearAuthenticatedUserContext","flush"]),m.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4};var s=(d.extensionConfig||{}).ApplicationInsightsAnalytics||{};if(!0!==d[I]&&!0!==s[I]){var c="onerror";t(["_"+c]);var u=T[c];T[c]=function(e,t,n,a,i){var r=u&&u(e,t,n,a,i);return!0!==r&&m["_"+c]({message:e,url:t,lineNumber:n,columnNumber:a,error:i}),r},d.autoExceptionInstrumented=!0}return m}(y.cfg);function a(){y.onInit&&y.onInit(n)}(T[t]=n).queue&&0===n.queue.length?(n.queue.push(a),n.trackPageView({})):a()}(window,document,{
src: "https://js.monitor.azure.com/scripts/b/ai.2.min.js", // The SDK URL Source
// name: "appInsights", // Global SDK Instance name defaults to "appInsights" when not supplied
// ld: 0, // Defines the load delay (in ms) before attempting to load the sdk. -1 = block page load and add to head. (default) = 0ms load after timeout,
// useXhr: 1, // Use XHR instead of fetch to report failures (if available),
crossOrigin: "anonymous", // When supplied this will add the provided value as the cross origin attribute on the script tag
// onInit: null, // Once the application insights instance has loaded and initialized this callback function will be called with 1 argument -- the sdk instance (DO NOT ADD anything to the sdk.queue -- As they won't get called)
cfg: { // Application Insights Configuration
    connectionString: "Copy connection string from Application Insights Resource Overview"
    /* ...Other Configuration Options... */
}});
</script>
```
E a chave de instrumentação é colocada após a palavra `connectionString:`.

> Em uma aplicação .Net (core) MVC com Razor, esse script pode ser inserido na página _Layout.cshtml, que atenderá a todas as páginas.

Veja mais em:
https://docs.microsoft.com/en-us/azure/azure-monitor/app/javascript


