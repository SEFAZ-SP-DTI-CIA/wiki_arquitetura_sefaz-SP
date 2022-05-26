[[_TOC_]]


## Secure Transporte WebService

A configuração do aplicativo deve garantir que o HTTPS seja usado para transportar todas as informações confidenciais.
Certifique-se de que o transporte HTTP esteja desabilitado e, em vez disso, habilite o transporte HTTPS.

Essa associação de transporte usa o HttpsToken:
```
<sp:TransportBinding>
  <wsp:Policy>
    <sp:TransportToken>
      <wsp:Policy>
        <sp:HttpsToken/>
       </wsp:Policy>
    </sp:TransportToken>
    ...
</sp:TransportBinding>
```

```
<sp:AsymmetricBinding>
    <wsp:Policy>
      ...
      <sp:IncludeTimestamp/>
      <sp:ProtectTokens/>
      <sp:OnlySignEntireHeadersAndBody/>
    </wsp:Policy>
  </sp:AsymmetricBinding>
```

Quando a proteção de tokens não está habilitada, não é possível garantir a integridade dos tokens, pois essa proteção impõe a assinatura de tokens.

Qualquer parte de uma mensagem não assinada tem o potencial de ser interceptada e modificada sem o conhecimento do remetente ou do destinatário. Sem uma assinatura, o destinatário não pode verificar  se o conteúdo de uma mensagem foi realmente originado pelo remetente.

A seguinte entrada de política "WS-SecurityPolicy" é um exemplo no qual a proteção de tokens é utilizada:

```
<sp:AsymmetricBinding>
  <wsp:Policy>
    <sp:InitiatorToken>
    ...
    </sp:InitiatorToken>
    <sp:RecipientToken>
    ...
    </sp:RecipientToken>
    <sp:AlgorithmSuite>
    ...
    </sp:AlgorithmSuite>
    <sp:Layout>
    ...
    </sp:Layout>
    <sp:IncludeTimestamp/>
    <sp:ProtectTokens/>
    <sp:OnlySignEntireHeadersAndBody/>
  </wsp:Policy>
</sp:AsymmetricBinding>
```
[Voltar](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/70/Desenvolvimento-Seguro?anchor=recomenda%C3%A7%C3%B5es-m%C3%ADnimas-de-seguran%C3%A7a-para-o-desenvolvimento)

## Secure Transport Weak SSL Protocol

Os protocolos TLS (Transport Layer Security) e SSL (Secure Sockets Layer) fornecem um mecanismo de proteção para garantir a autenticidade, a confidencialidade e a integridade dos dados transmitidos entre um cliente e o servidor Web. 

Os protocolos SSLv2, SSLv2, SSLv3 e TLSv1 contêm várias falhas que os tornam não seguros e, por isso, não devem ser utilizados para transmitir dados confidenciais. 
- Nenhuma proteção contra ataques man-in-the-middle
- Mesma chave usada para autenticação e criptografia
- Controle fraco da autenticação de mensagens
- Nenhuma proteção contra o fechamento de conexões TCP

A presença dessas propriedades pode contribuir para que um invasor intercepte, modifique ou adultere dados confidenciais. 

Recomendação: É altamente recomendável forçar o cliente a usar apenas os protocolos mais seguros. O Exemplo a seguir demonstra como impor a comunicação através do protocolo TLS 1.2
```
HttpClientHandler handler = new HttpClientHandler()
   {
    SslProtocols = System.Security.Authentication.SslProtocols.Tls12
  };
```
[Voltar](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/70/Desenvolvimento-Seguro?anchor=recomenda%C3%A7%C3%B5es-m%C3%ADnimas-de-seguran%C3%A7a-para-o-desenvolvimento)

## Secure Transport: Database

O aplicativo se comunica com seu servidor de banco de dados por meio de canais não criptografados e pode representar um risco de segurança significativo para a empresa e os usuários desse aplicativo. Nesse caso, um invasor pode modificar os dados inseridos pelo usuário ou até mesmo executar comandos SQL arbitrários voltados ao servidor de banco de dados.

A maioria dos servidores de banco de dados oferecem alternativas criptografadas em portas diferentes que usam SSL/TLS para criptografar todos os dados que estão sendo enviados. 

A seguinte configuração faz com que o aplicativo se comunique com seu servidor de banco de dados por meio de canais criptografados:
```
<connectionStrings>
  <add name="Test" connectionString="Data Source=210.10.20.10,1433; Initial Catalog=myDataBase;User ID=myUsername;Password=myPassword; Encrypt=yes;" providerName="System.Data.SqlClient" />
</connectionStrings>
```
[Voltar](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/70/Desenvolvimento-Seguro?anchor=recomenda%C3%A7%C3%B5es-m%C3%ADnimas-de-seguran%C3%A7a-para-o-desenvolvimento)

## Secure Transport: Mail Transmission

Dados confidenciais enviados na rede em formato não criptografado estão sujeitos a serem lidos/modificados por qualquer invasor que possa interceptar o tráfego de rede.

A maioria dos provedores de serviços de e-mail oferecem alternativas diferentes que usam SSL/TLS para criptografar todos os dados enviados na rede ou que permitem o upgrade de uma conexão não criptografada existente para usar SSL/TLS. 

O seguinte SmtpClient está configurado corretamente para usar SSL/TLS a fim de se comunicar com um servidor SMTP:

```
<mailSettings>
  <smtp deliveryMethod="network">
    <network host="smtp.acme.com" port="25" enableSsl="true"/>
  </smtp>
</mailSettings>
```
[Voltar](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/70/Desenvolvimento-Seguro?anchor=recomenda%C3%A7%C3%B5es-m%C3%ADnimas-de-seguran%C3%A7a-para-o-desenvolvimento)

## Segurança em Cabeçalho HTTP

### CSP Content Security Police

Content Security Policy (CSP) é uma camada extra de segurança que ajuda a detectar e mitigar alguns tipos de ataques, incluindo Cross Site Scripting (XSS) e ataques de injeção de dados. O objetivo desses ataques inclui roubo de dados, defacement e distribuição de malware.

Não utilize o atributo [AllowHTML] ou a classe de ajuda @Html.Raw a menos que você realmente saiba que o conteúdo que está sendo escrito para o browser é seguro. 

Habilite uma política de segurança de conteúdo, para prevenir que páginas acessem recursos que não deveriam ser capazes de acessar (como por exemplo, scripts maliciosos). 

Habilite uma política de segurança de conteúdo, conforme exemplo:
```
<system.webServer>
    <httpProtocol>
        <customHeaders>
            <add name="Content-Security-Policy" value="default-src 'self';" />
        </customHeaders>
    </httpProtocol>
</system.webServer>
```
```
<httpProtocol>
<customHeaders>
<add name=”X-XSS-Protection” value=”1; mode=block”/>
<add name=”Content-Security-Policy” value=”default-src ‘self’”/>
<add name=”X-frame-options” value=”SAMEORIGIN”/>
<add name=”X-Content-Type-Options” value=”nosniff”/>
<add name=”Referrer-Policy” value=”strict-origin-when-cross-origin”/>
<remove name=”X-Powered-By”/>
</customHeaders>
</httpProtocol>

https://www.syncfusion.com/blogs/post/shield-your-asp-net-mvc-web-applications-with-content-security-policy-csp.aspx

https://vulncat.fortify.com/en/weakness?q=Content-Security-Policy

```

[Voltar](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/70/Desenvolvimento-Seguro?anchor=seguran%C3%A7a-em-cabe%C3%A7alho-http)

### HSTS HTTP Strict-Transport-Security

O cabeçalho de resposta HTTP Strict-Transport-Security (HSTS) permite que um site informe aos navegadores que ele deve ser acessado apenas por HTTPS, em vez de usar HTTP.
- O HSTS redireciona automaticamente solicitações HTTP para HTTPS para o domínio de destino.
- O HSTS não permite que um usuário substitua a mensagem de certificado inválido.

```
app.UseHsts(hsts => hsts.MaxAge(365).IncludeSubdomains());
app.UseXContentTypeOptions();
app.UseReferrerPolicy(opts => opts.NoReferrer());
app.UseXXssProtection(options => options.EnabledWithBlockMode());
app.UseXfo(options => options.Deny());
app.UseCsp(opts => opts
.BlockAllMixedContent()
.StyleSources(s => s.Self())
.StyleSources(s => s.UnsafeInline())
.FontSources(s => s.Self())
.FormActions(s => s.Self())
.FrameAncestors(s => s.Self())
.ImageSources(s => s.Self())
.ScriptSources(s => s.Self())
);

```

[Voltar](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/70/Desenvolvimento-Seguro?anchor=seguran%C3%A7a-em-cabe%C3%A7alho-http)

### X-Frame-Options

X-FRAME-OPTIONS é um cabeçalho da Web que pode ser usado para permitir ou negar que uma página seja iframed. Habilitando X-Frame-Options o cabeçalho de resposta HTTP fica prevenido contra Cross-Frame Scripting( **Ataques maliciosos que injetam client-side script dentro das páginas web vistas por outros usuários**) e clickjacking( **Roubo de click para executar operações maliciosas**).

Opções de definição do cabeçalho:

- X-FRAME-OPTIONS: DENY
A página não pode enquadrada, não importa quem seja (incluindo o próprio enquadramento do site).

- X-FRAME-OPTIONS: SAMEORIGIN
A página pode ser enquadrada desde que o domínio seja igual. 

- X-FRAME-OPTIONS: ALLOW-FROM https://myotherdomain.com
A página pode ser enquadrada pelos domínios especificados. 

Para implementar essa proteção, deve-se adicionar o X-Frame-Options cabeçalho de resposta HTTP a qualquer página que deseja proteger contra o clique. Uma maneira de fazer isso é adicionar o cabeçalho de resposta HTTP manualmente a todas as páginas. Uma maneira possivelmente mais simples é implementar um filtro que adicione automaticamente o cabeçalho a todas as páginas ou adicione-o no nível WAF ( Firewall de aplicação web ) ou WEBServer( Servidor Web ).

Erros comuns:
As metatags que tentam aplicar a diretiva X-Frame-Options NÃO FUNCIONAM. Por exemplo, `<meta http-equiv="X-Frame-Options" content="deny">` não vai funcionar. Deve-se aplicar a diretiva X-FRAME-OPTIONS como cabeçalho de resposta HTTP, conforme descrito acima.

[Voltar](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/70/Desenvolvimento-Seguro?anchor=seguran%C3%A7a-em-cabe%C3%A7alho-http)

### X-XSS-Protection
O X-XSS-Protection é um cabeçalho que pode ser definido em uma página da web para ativar a proteção XSS "limitada", essa proteção já está habilitada nos navegadores mais novos.

- Configurações de proteção X-XSS
X-XSS-Protection: 0
Desativa a proteção XSS (útil quando testar o XSS por conta própria)
- Proteção X-XSS: 1
Ativa a proteção XSS, se o XSS for detectado, o navegador tentará filtrar ou sanitizar a saída. Mas ainda será possivel que a renderização ocorra.
- Proteção X-XSS: 1; mode = block
Habilita a proteção XSS, se o XSS for detectado, o navegador para de renderizar completamente.
- Proteção X-XSS: 1; report = <reporting-uri> O
relatório funciona apenas em navegadores Chromium (mas pode ser usado para reforçar a proteção em outros navegadores). Pode-se ter um retorno de chamada que informa sobre as tentativas de XSS.

Configurando a proteção X-XSS no código:

```
public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
{
	app.Use(async (context, next) =>
	{
		context.Response.Headers.Add("X-Xss-Protection", "1");
		await next();
	});

	app.UseMvc();
}
```
Definindo a proteção X-Xss no nível do servidor
Segue exemplos de configuração para o IIS, apache e Nginx:

Definindo a proteção X-XSS no IIS
```
Abra o Gerenciador do IIS e, na árvore esquerda, clique com o botão esquerdo no site que deseja gerenciar.
Clique duas vezes no ícone "Cabeçalhos de resposta HTTP".
Clique com o botão direito do mouse na lista de cabeçalhos e selecione "Adicionar"
Para o "nome" escreva "X-Xss-Protection" e para o valor escreva na opção desejada, por exemplo, "1".
```

Configurando a proteção X-XSS no Apache
- No arquivo httpd.conf, deve-se anexar a seguinte linha:
`Header always append X-Xss-Protection 1`

- Setting X-XSS-Protection in htaccess
`Header append X-Xss-Protection "1"`

[Voltar](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/70/Desenvolvimento-Seguro?anchor=seguran%C3%A7a-em-cabe%C3%A7alho-http)

### X-Content-Type-Options
O X-Content-Type-Options é um cabeçalho que diz ao navegador para não tentar "adivinhar" qual o tipo de mimetismo de um recurso e apenas pegar o tipo de mimet que o servidor retornou como fato.

Para atenuar essa constatação, o programador também pode: (1) defini-lo globalmente para todas as páginas do aplicativo no arquivo web.config ou (2) definir o cabeçalho necessário página por página apenas para as páginas que possam incluir conteúdo controlável pelo usuário.

Para defini-lo globalmente, adicione o cabeçalho no arquivo web.config para o aplicativo hospedado pelo IIS:

```
<system.webServer>
  <httpProtocol>
    <customHeaders>
      <add name="X-Content-Type-Options" value="nosniff"/>
    </customHeaders>
  </httpProtocol>
</system.webServer>
```
Os seguintes exemplos mostram como adicionar o cabeçalho ao método global Application_BeginRequest:

```
void Application_BeginRequest(object sender, EventArgs e)
{
    this.Response.Headers["X-Content-Type-Options"] = "nosniff";
}
```

O exemplo a seguir mostra como adicioná-lo a uma página por meio da implementação de um módulo HTTP personalizado usando a interface IHttpModule

```
public class XContentTypeOptionsModule : IHttpModule
{
    ...
    void context_PreSendRequestHeaders(object sender, EventArgs e)
    {
        HttpApplication application = sender as HttpApplication;

        if (application == null) return;
        if (application.Response.Headers["X-Content-Type-Options"] != null) return;
        application.Response.Headers.Add("X-Content-Type-Options", "nosniff");
    }
}
```
[Voltar](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/70/Desenvolvimento-Seguro?anchor=seguran%C3%A7a-em-cabe%C3%A7alho-http)

## Gerenciamento de Sessão

### Secure
O Secure atributo cookie instrui os navegadores da Web a enviar apenas o cookie por meio de uma conexão HTTPS (SSL / TLS) criptografada.

Esse mecanismo de proteção de sessão é obrigatório para impedir a divulgação do ID da sessão através de ataques MitM (Man-in-the-Middle). Ele garante que um invasor não possa simplesmente capturar a ID da sessão do tráfego do navegador da web.

Para fazer isso globalmente, deve-se incluir a seguinte instrução em Web.config:

```
<configuration>
  <system.web>
    <!-- Force secure connections for all Cookies -->
    <httpCookies requireSSL="true" />
  </system.web>
</configuration>
```
Também precisamos garantir que, se estivermos usando a autenticação de formulários, forçaremos conexões seguras para isso também, caso contrário, isso poderá negar o efeito de fazer isso para os cookies, pois os dados precisariam estar disponíveis de forma clara para coincidir com a configuração de autenticação de formulários.
```
<configuration>
  <system.web>
    <authentication mode="Forms">
      <!-- Force secure connections for Forms Authentication -->
      <forms requireSSL="true" />
    </authentication>
  </system.web>
</configuration>
```



Se você estiver criando cookies manualmente, também poderá marcá-los como seguros em C #:
```
Response.Cookies.Add(
    new HttpCookie("key", "value")
    {
        Secure = true,
    });
```
[Voltar](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/70/Desenvolvimento-Seguro?anchor=gerenciamento-de-sess%C3%A3o)

### HTTPOnly
O HttpOnly atributo cookie instrui os navegadores da web a não permitir que scripts (por exemplo, JavaScript ou VBscript) possam acessar os cookies por meio do objeto DOM document.cookie. Essa proteção de ID de sessão é obrigatória para impedir o roubo de ID de sessão através de ataques XSS.

Veja como fazer isso no Web.config:
```
<configuration>
  <system.web>
    <!-- Prevent client script from reading Cookies -->
    <httpCookies httpOnlyCookies="true" />
  </system.web>
</configuration>

```
O valor do httpOnlyCookiesatributo é true neste caso, como no exemplo anterior, HttpOnlytambém pode ser definido no código C#:

```
Response.Cookies.Add(
    new HttpCookie("key", "value")
    {
        HttpOnly = true,
        Secure = true,
    });
```
[Voltar](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/70/Desenvolvimento-Seguro?anchor=gerenciamento-de-sess%C3%A3o)

### SameSite
O SameSite permite que um servidor defina um atributo de cookie, impossibilitando o navegador de enviar esse cookie junto com solicitações entre sites. O objetivo principal é reduzir o risco de vazamento de informações entre origens e fornecer alguma proteção contra ataques de falsificação de solicitações entre sites.

SameSite é um atributo de cookie que informa se seus cookies estão restritos apenas a solicitações de terceiros. Se uma página no domínio domain1.com solicitar uma URL domain1.com e os cookies forem carregados com o SameSite atributo, os cookies serão enviados entre o cliente e o servidor. 

O .NET 4.7.2 e o .NET Core 3.1 suportam o SameSite atributo. 
Mas a implementação mais fácil (IMO) é incluir uma regra de reescrita em Web.config:

```
<system.webServer>
  <rewrite>
    <outboundRules>
      <clear />
      <rule name="Add SameSite" preCondition="No SameSite">
        <match serverVariable="RESPONSE_Set_Cookie" pattern=".*" negate="false" />
        <action type="Rewrite" value="{R:0}; SameSite=lax" />
      </rule>
      <preConditions>
        <preCondition name="No SameSite">
          <add input="{RESPONSE_Set_Cookie}" pattern="." />
          <add input="{RESPONSE_Set_Cookie}" pattern="; SameSite=lax" negate="true" />
        </preCondition>
      </preConditions>
    </outboundRules>
  </rewrite>
  ...
</system.webServer>
```

Exemplo de cookie SameSite para WebForms do ASP.NET 4.7.2:

```
// Create the cookie
HttpCookie sameSiteCookie = new HttpCookie("SameSiteSample");

// Set a value for the cookie
sameSiteCookie.Value = "sample";

// Set the secure flag, which Chrome's changes will require for SameSite none.
// Note this will also require you to be running on HTTPS
sameSiteCookie.Secure = true;

// Set the cookie to HTTP only which is good practice unless you really do need
// to access it client side in scripts.
sameSiteCookie.HttpOnly = true;

// Add the SameSite attribute, this will emit the attribute with a value of none.

// To not emit the attribute at all set the SameSite property to -1.
sameSiteCookie.SameSite = SameSiteMode.None;

// Add the cookie to the response cookie collection
Response.Cookies.Add(sameSiteCookie);

<system.web>
  <authentication mode="Forms">
    <forms name=".ASPXAUTH" loginUrl="~/" cookieSameSite="None" requireSSL="true">
    </forms>
  </authentication>
</system.web>
```



[Voltar](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/70/Desenvolvimento-Seguro?anchor=gerenciamento-de-sess%C3%A3o)

### METHOD HTTP

Os mecanismos de autenticação e autorização de um aplicativo podem ser ignorados com a adulteração de verbos HTTP quando:
- Ele utiliza um controle de segurança que lista verbos HTTP.
- O controle de segurança não consegue bloquear verbos que não estão listados.
- O aplicativo atualiza seu estado com base em solicitações GET ou outros verbos HTTP arbitrários.

Por padrão, o .NET Framework permite todos os verbos HTTP e, portanto, mesmo que essa configuração negue GETs e POSTs para todos os usuários, que não impede solicitações HEAD. Talvez seja possível para um invasor usufruir de funcionalidades administrativas substituindo solicitações GET ou POST por solicitações HEAD. Em outras palavras, esse código satisfaz as condições 1 e 2 mencionadas anteriormente. Para que solicitações HEAD usufruam de funcionalidades administrativas, basta que o aplicativo realize comandos com base em solicitações que utilizam verbos diferentes de POST.

Use uma lista branca especificando uma política de autorização que liste apenas os verbos que devem ser permitidos, negando todos os outros. Como o .NET Framework permite caracteres especiais, isso pode ser feito com o uso do caractere *.

Por exemplo, a seguinte configuração nega todos os verbos HTTP para qualquer usuário além de "admin".
    
```
<authorization>
        <allow verbs="GET,POST" users="admin"/>
        <deny verbs="*" users="*" />
    </authorization>
```
[Voltar](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/70/Desenvolvimento-Seguro?anchor=gerenciamento-de-sess%C3%A3o)

### Política de Cookies
```
//startup.cs
services.ConfigureApplicationCookie(options =>
{
 options.Cookie.HttpOnly = true;
 options.Cookie.Expiration = TimeSpan.FromHours(1)
 options.SlidingExpiration = true;
}); 
```
O exemplo a seguir mostra o MVC ASP.NET configurado com um tempo limite de autenticação de 10 minutos.

```
<configuration>
  <system.web>
  <authentication>
    <forms
      timeout="10" />
  </authentication>
  </system.web>
</configuration>
```
[Voltar](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/70/Desenvolvimento-Seguro?anchor=gerenciamento-de-sess%C3%A3o)

### Proteção de Cookies

Configurações de cookie para um aplicativo ASP.NET são definidas no atributo protection do elemento configuration/system.web/authentication/forms em web.config. Os possíveis valores desse atributo são:

None - não realizar a criptografia ou a validação de dados (menos seguro)
Encryption - proteger os dados no cookie com criptografia
Validation - verificar se o cookie não foi alterado
All - usar criptografia e validação (mais seguro)

Uma declaração exigindo proteções All pode ter a seguinte aparência:

```
<configuration>
  <system.web>
    <authentication>
      <forms protection="All" ... />
     ...
    </authentication>
  </system.web>
</configuration>
```

Configurações de cookie para um aplicativo ASP.NET são definidas no atributo cookieProtection do elemento roleManager em web.config. Os possíveis valores desse atributo são:

```
<configuration>
  <system.web>
    <roleManager
       cookieProtection="All"
       ...
    </roleManager>
  </system.web>
</configuration>
```

###Session Fixation

Autenticar um usuário ou estabelecer uma nova sessão de usuário, sem invalidar nenhum identificador de sessão existente, dá a um invasor a oportunidade de roubar sessões autenticadas.

Os aplicativos da Web devem ignorar qualquer ID de sessão fornecido pelo navegador do usuário no login e devem sempre gerar uma nova sessão na qual o usuário fará login caso seja autenticado com êxito.

```
[HttpPost]
    public ActionResult Index(User model)
    {           
       if (service.Login(model.Email,model.Passwords))//Authenticate
            {                    
                AbandonSession();//Delete any existing sessions
                var newSessionId = CreateSessionId(HttpContext.Current); //Create a new SessionId
                SetSessionId(HttpContext.Current, newSessionId);

                return RedirectToAction("Index", "App");
            }
            else
            {
                ModelState.AddModelError("", "Email or Password Incorrect");
                return View(model);
            }
        }  
    }

public void AbandonSession()
    {
        Session.Clear();
        Session.Abandon();
        Session.RemoveAll();
        if (Request.Cookies["ASP.NET_SessionId"] != null)
        {
            Response.Cookies["ASP.NET_SessionId"].Value = string.Empty;
            Response.Cookies["ASP.NET_SessionId"].Expires = DateTime.Now.AddMonths(-20);
        }
    }

    private static string CreateSessionId(HttpContext httpContext)
    {
        var manager = new SessionIDManager();

        string newSessionId = manager.CreateSessionID(httpContext);

        return newSessionId;
    }

    public static void SetSessionId(HttpContext httpContext, string newSessionId)
    {
        var manager = new SessionIDManager();

        bool redirected;
        bool cookieAdded;

        manager.SaveSessionID(httpContext, newSessionId, out redirected, out cookieAdded);
        
    }
```

Impedir que o invasor obtenha um ID de sessão válido, um aplicativo da web em um sistema estrito só deve emitir IDs de sessão de sessões recém-geradas para os usuários ** após ** eles terem se autenticado com sucesso (ao invés de emiti-los junto com o formulário de login). Isso significa que um invasor que não seja um usuário legítimo do sistema não será capaz de obter uma ID de sessão válida e, portanto, não poderá realizar um ataque de fixação de sessão.

Destruição de Sessão No Logout / Timeout ou quando o usuário fecha a página / navegador / guia, a sessão deve ser 'interrompida'. Isso precisa ser feito no servidor e não apenas no navegador, excluindo o cookie de sessão:

```
public ActionResult LogOut()
    {
        AbandonSession();

        return RedirectToAction("Index");
    }
```
Para fechar o navegador / guia, inclua o seguinte arquivo de layout Javascript:

```
<script type="text/javascript">
    $(document).ready(function () {            

        window.onbeforeunload = function (e) {
            $.ajax({
                url: '@Url.Action("AbandonSession", "Home")'
            });
        };

        $(function () {
            $('a').not('#Logout').click(function () {
                window.onbeforeunload = null;
            });

            $('.btn').click(function () {
                window.onbeforeunload = null;
            });
        });

    });
</script>
```


[Voltar](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/70/Desenvolvimento-Seguro?anchor=gerenciamento-de-sess%C3%A3o)

## Validação de entrada

Embora a validação esteja habilitada por padrão, deve-se torná-la explícita, habilitando a estrutura de validação no seu arquivo Web.config. Um exemplo de uma configuração típica é:

```
<configuration>
    <system.web>
        <pages validateRequest="true" />
    </system.web>
</configuration>
```
[Voltar](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/70/Desenvolvimento-Seguro?anchor=gerenciamento-de-sess%C3%A3o)

## Criptografia 

Protocolo de segurança, algoritmo, Hash e comprimento de chave

### Criptografia na Comunicação: 
O SSL (Secure Sockets Layer) e seu sucessor TLS (Transport Layer Security) são protocolos de criptografia projetados para internet. Permitem a comunicação segura entre os lados cliente e servidor de uma aplicação web.

Recomendações:
Devido diversas vulnerabilidades e obsolescência deve-se suspender o uso dos protocolos de comunicação:
- SSL3.0, TLS 1.0 e TLS 1.1.
Garantir apenas a compatibilidade dos protocolos:
- TLS 1.2 e 1.3.

[Voltar](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/70/Desenvolvimento-Seguro?anchor=criptografia)

### Cifras de bloco simétrica e Assimétrica.

Cifra simétrica: 

Recomendações: 
- Implementação do Advanced Encryption Standard (AES) para novos sistemas compatíveis com comprimento de chave de no mínimo 128 bits porém é possível habilitar AES-192 e AES-256.
- Three-Key Triple Data Encryption Standard é permitido para aplicações e sistemas com o objetivo de migrar o quanto antes para o AES.
- Demais cifras não são aconselhadas o uso devido a obsolescência e falhas RC2, DES, 2-Key 3DES e DESX.

Cifra assimétrica:

Recomendações:
- RSA (Rivest-Shamir-Adleman): deve ser usado para criptografia, troca de chaves e assinaturas.
- Deve ser usado nos modos de preenchimento OAEP ou RSA-PPS.
Para usar o RSA com segurança, o OAEP (Preenchimento de Criptografia Assimétrica Ideal) deve ser utilizado enquanto a criptografia é realizada.

O código a seguir realiza a criptografia com uma chave pública RSA usando o preenchimento OAEP:
```
static public byte[] EncryptWithRSA(byte[] plaintext, RSAParameters key) {
    try {
      RSACryptoServiceProvider rsa = new RSACryptoServiceProvider();
      rsa.ImportParameters(key);
      return rsa.Encrypt(plaintext, true);
    }
    catch(CryptographicException e) {
      Console.WriteLine(e.Message);
      return null;
    }
  }
```
Verifique se as chaves RSA foram implementadas com no mínimo, 2048 bits de comprimento. Aplicativos que exigem criptografia forte nos próximos anos devem usar chaves de pelo menos 4096 bits de comprimento.

O código a seguir usa o algoritmo RSA com uma chave de criptografia de 2048 bits.
```
static public byte[] EncryptWithRSA(byte[] plaintext, RSAParameters key) {
    try {
      RSACryptoServiceProvider rsa = new RSACryptoServiceProvider(2048);
      rsa.ImportParameters(key);
      return rsa.Encrypt(plaintext, true);
    }
    catch(CryptographicException e) {
      Console.WriteLine(e.Message);
      return null;
    }
  }
```
- Diffie-Hellman com chave superior a 2048 bits.

[Voltar](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/70/Desenvolvimento-Seguro?anchor=criptografia)

### Vetor de inicialização
Os vetores de inicialização nunca deverão ser gerados com um valor constante. Segue recomendações sobre como gerar números aleatórios fortes.

Recomendações:
- CNG: Use BCryptGenRandom com o BCRYPT_USE_SYSTEM_PREFERRED_RNG flag.
- CAPI: Use CryptGenRandom para geração de valores randomicos.
- .Net: Use RNGCryptoServiceProvider ou RNGCng.
- Windows Store Apps: use CryptographicBuffer.GenerateRandom ou CryptographicBuffer.GenerateRandomNumber.

NÃO RECOMENDADO: Devido a funções inseguras na geração de números randômicos não recomenda-se o uso de rand, System.Random (.NET), GetTickCount, GetTickCount64 ou algoritmo DUAL_EC_DRBG.

[Voltar](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/70/Desenvolvimento-Seguro?anchor=criptografia)

### Funções Criptográficas de Hash

Recomendações: 
- Usar criptografia de Hash SHA2 de algoritmos de Hash (SHA256, SHA384 e SHA512).
Não Recomendado usar esse Hash com menos de 128 bits.

[Voltar](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/70/Desenvolvimento-Seguro?anchor=criptografia)

### Algoritmos de Hash 

Recomendações:
- Argon2id: É o mais indicado, existem três versões diferentes do algoritmo e a variante de id do Argon2 deve ser usada quando disponível, pois fornece uma abordagem equilibrada para resistir a ataques de canal lateral e baseados em GPU.
- PBKDF2: É recomendado pelo NIST e tem implementações validadas FIPS-140, além disso, ele é compatível com o padrão da estrutura .NET ASP.NET Identity utiliza o PBKDF2 com HMAC-SHA256.
pbkdf2_sha1
pbkdf2_sha256
pbkdf2_sha512

[Voltar](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/70/Desenvolvimento-Seguro?anchor=criptografia)

### Criptografar dados confidenciais antes do armazenamento com DPAPI/DPAPI-NG

Recomendações:
- Para dados que precisam ser persistidos nas reinicializações do sistema:
CryptProtectData
CryptUnprotectData
NCryptProtectSecret (Windows 8 CNG DPAPI)
- Para dados que não precisam ser persistidos nas 
CryptProtectMemory
CryptUnprotectMemory
- Para dados que precisam ser persistidos e acessados por várias contas de domínio e computadores:
NCryptProtectSecret (in CNG DPAPI, available as of Windows 8)
Microsoft Azure KeyVault

[Voltar](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/70/Desenvolvimento-Seguro?anchor=criptografia)

### SQL Server TDE
Pode-se usar o SQL Server Transparent Data Encryption (TDE) para proteger dados confidenciais.

Recomendações:
- Deve-se usar uma chave de criptografia de banco de dados (DEK) TDE que atenda ao algoritmo criptográfico SDL e principais requisitos de resistência. Atualmente, apenas AES_128, AES_192 e AES_256 são recomendados;

Segue recomendações importantes no uso do SQL TDE:
- O SQL Server não oferece suporte à criptografia para dados FILESTREAM, mesmo quando o TDE está habilitado.
- A TDE não fornece criptografia automática para dados em trânsito de ou para o banco de dados; deve-se permitir conexões criptografadas com o banco de dados do SQL Server. Habilitar Conexões criptografadas com o mecanismo de banco de dados (SQL Server Configuration Manager) para orientação
sobre a ativação de conexões criptografadas, consulte o artigo com as informações: "https://docs.microsoft.com/en-us/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine?redirectedfrom=MSDN&view=sql-server-ver15".
- Durante a movimentação de um banco de dados protegido por TDE para uma instância diferente do SQL Server, deve-se mover o certificado que protege a chave de criptografia de dados TDE (DEK) e instalar no banco de dados mestre da instância do SQL Server de destino. Consulte o artigo da TechNet Mover um TDE Banco de dados protegido para outro SQL Server para obter mais detalhes "https://docs.microsoft.com/en-us/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server?redirectedfrom=MSDN&view=sql-server-ver15"

[Voltar](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/70/Desenvolvimento-Seguro?anchor=criptografia)

##Contramedidas Owasp

O OWASP apresenta as classificações e contramedidas de segurança de aplicativos web mais vulneráveis e explorados.
- Injeção de (SQL)
- Injeção de códigos maliciosos
- Quebra de autenticação
- Exposição de dados sensíveis
- Vulnerabilidades em entidades externas de XML
- Definição de referência insegura de objetos
- Falhas de cross-site scripting
- Desserialização insegura
- Falhas de monitoração
- Solicitação forjada CSRF
- Falhas de validação de redirecionamento.

### Injeção de (SQL)
Recomendação:
- Utilizar LINQ que é a biblioteca de acesso ao banco de dados LINQ mais rápida, oferecendo uma camada simples, leve, rápida e com segurança.
- Utilizar ORM Mapeador Relacional de Objetos ou procedimentos armazenados é a maneira mais eficaz de combater a vulnerabilidade de injeção de SQL.
- Implementar instruções preparadas (com consultas parametrizadas) é como todos os desenvolvedores deverão ser ensinados a escreverem consultas no banco de dados. 
- Sempre utilizar Parâmetros para passar pra PROC (SQL PARAMETER) (De preferência parâmetros com a tipagem correta, int, date bool).
- Evitar usar parâmetros Varchar, nvarchar, Text maiores que 10 caracteres (Não é proibido mas se puder evitar).
- Evitar o uso de Procedures Dinâmicas pois facilita um ataque através de algum parâmetro.
- Se precisar usar Procedures Dinâmicas não utilizar o Comando Execute e sim o sp_execute pois ele é mais seguro pra barrar ataques.
- Não retornar Exceptions de BD na tela pro usuário (Pois pode fornecer informações confidencias como nome de tabela, procedures e permissões).
- Validar Campos de Entrada impedindo caracteres especiais como aspas simples, comentários e instruções como INSERT, DELETE, UPDATE, TRUNCATE.
- Essa regra vale tanto para Controlers Text quanto para parâmetros passados via Query String.

As instruções preparadas garantem que um invasor não possa alterar a intenção de uma consulta, mesmo que os comandos SQL sejam inseridos por um invasor. No exemplo seguro abaixo, se um invasor digitar o ID do usuário tom' or '1'='1, a consulta parametrizada não estaria vulnerável e, em vez disso, procuraria um nome de usuário que correspondesse literalmente à sequência inteira tom' or '1'='1.

Exemplo de declaração segura :
```
Usando o recurso interno do .NET

String query = "SELECT account_balance FROM user_data WHERE user_name = ?";
try {
   OleDbCommand command = new OleDbCommand(query, connection);
   command.Parameters.Add(new OleDbParameter("customerName", CustomerName Name.Text));
   OleDbDataReader reader = command.ExecuteReader();
   // …
} catch (OleDbException se) {
   // error handling
}

Using ASP .NET built-in feature

string sql = "SELECT * FROM Customers WHERE CustomerId = @CustomerId";
SqlCommand command = new SqlCommand(sql);
command.Parameters.Add(new SqlParameter("@CustomerId", System.Data.SqlDbType.Int));
command.Parameters["@CustomerId"].Value = 1;

```

Exemplo 2: Para cada campo que exista na Tela (Text, Drop, Check, List, QueryString) e que são enviados ao Banco através de consulta,segue chamada de método validando a entrada.
 
        
```
public static string PrevineInjection(string text)
        {
            if (!string.IsNullOrEmpty(text))
            {
                text = text.ToUpper();
 
                text = text.Replace("'", String.Empty);
                text = text.Replace("\"", String.Empty);
                text = text.Replace("´", String.Empty);
                text = text.Replace(";", String.Empty);
                text = text.Replace("--", String.Empty);
                text = text.Replace("+", String.Empty);
                text = text.Replace("*/", String.Empty);
                text = text.Replace("/*", String.Empty);
                text = text.Replace("<>", String.Empty);
                text = text.Replace("!=", String.Empty);
                text = text.Replace("=", String.Empty);
                text = text.Replace("%", String.Empty);
                text = text.Replace("INSERT", String.Empty);
                text = text.Replace("UNION", String.Empty);
                text = text.Replace("SELECT", String.Empty);
                text = text.Replace("DELETE", String.Empty);
                text = text.Replace("UPDATE", String.Empty);
                text = text.Replace("sp_", String.Empty);
                text = text.Replace("xp_", String.Empty);
                text = text.Replace("DROP", String.Empty);
                text = text.Replace("TRUNCATE", String.Empty);
                text = text.Replace("FROM", String.Empty);
                text = text.Replace("WHERE", String.Empty);
                text = text.Replace("SYS.", String.Empty);
                text = text.Replace("CREATE", String.Empty);
                text = text.Replace("EXECUTE", String.Empty);
                text = text.Replace("-SHUTDOWN", String.Empty);
                text = text.Replace("'OR'1'='1'", String.Empty);
 
            }
 
            return text;
        }
```

Obs: O Método acima pode ser reescrito através de uma Function do SQL ou Oracle e chamado nas Procedures de Consulta, Insert, Update. 

Outra sugestão adicional é colocar uma validação “ValidateRequest” do ASP.NET no Botão de Pesquisa.
`<asp:Button ID="bt33022_Pesquisa" runat="server" Text="Pesquisar" CssClass="botaoPesquisa" OnClick="c_btnPesquisa_Click" ValidateRequestMode="Disabled"/>`


[Voltar](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/70/Desenvolvimento-Seguro?anchor=seguran%C3%A7a-em-chamadas-sql---inje%C3%A7%C3%A3o-de-(sql))

### Injeção de códigos maliciosos
Recomendação:
- Use System.Diagnostics.Process.Start para chamar funções subjacentes do SO.
```
System.Diagnostics.Process process = new System.Diagnostics.Process();
System.Diagnostics.ProcessStartInfo startInfo = new System.Diagnostics.ProcessStartInfo();
startInfo.FileName = "validatedCommand";
startInfo.Arguments = "validatedArg1 validatedArg2 validatedArg3";
process.StartInfo = startInfo;
process.Start();
```
[Voltar](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/70/Desenvolvimento-Seguro?anchor=seguran%C3%A7a-de-par%C3%A2metros-manipulados-pelo-usu%C3%A1rio---inje%C3%A7%C3%A3o-de-c%C3%B3digo)

### Quebra de autenticação
Recomendação:
- Defina uma política de cookies
```
//startup.cs
services.ConfigureApplicationCookie(options =>
{
 options.Cookie.HttpOnly = true;
 options.Cookie.Expiration = TimeSpan.FromHours(1)
 options.SlidingExpiration = true;
});
```
- Utilizar o Identity
Caso não seja possível, considere os seguintes itens:
- Configure uma política de senha segura (aceitando todos os caracteres com pelo menos 12 caracteres) e uma política de cookies.
- Garanta que cookies sejam enviados via HTTPS, proteja os métodos LogOn, registro e redefinição de senha contra ataques de força bruta limitando requisições
- Utilizar ReCaptcha.

[Voltar](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/70/Desenvolvimento-Seguro?anchor=quebra-de-autentica%C3%A7%C3%A3o)

### Exposição de dados sensíveis
Recomendação:
- Aplicar criptografia no armazenamento de senhas
- Use a API de proteção de dados do Windows (DPAPI) para armazenamento local seguro de dados confidenciais.
- Use um algoritmo de hash forte, no .NET (Framework e Core), o algoritmo de hash mais forte para os requisitos gerais de hash é System.Security.Cryptography.SHA512.
- Na estrutura .NET, o algoritmo mais forte para o hash de senha é o PBKDF2, implementado como System.Security.Cryptography.Rfc2898DeriveBytes.
- No .NET Core, o algoritmo mais forte para o hash de senhas é o PBKDF2, implementado como Microsoft.AspNetCore.Cryptography.KeyDerivation.Pbkdf2, que possui várias vantagens significativas Rfc2898DeriveBytes.
- Utilização de criptografia na comunicação no mínimo TLS 1.2.
- Proteção na camada de transporte Web.config:
```
<system.web>
    <httpRuntime enableVersionHeader="false"/>
</system.web>
<system.webServer>
    <security>
        <requestFiltering removeServerHeader="true" />
    </security>
    <httpProtocol>
        <customHeaders>
            <add name="X-Content-Type-Options" value="nosniff" />
            <add name="X-Frame-Options" value="DENY" />
            <add name="X-Permitted-Cross-Domain-Policies" value="master-only"/>
            <add name="X-XSS-Protection" value="0"/>
            <remove name="X-Powered-By"/>
        </customHeaders>
    </httpProtocol>
</system.webServer>
```
[Voltar](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/70/Desenvolvimento-Seguro?anchor=seguran%C3%A7a-na-exposi%C3%A7%C3%A3o-de-dados-sens%C3%ADveis)


### Vulnerabilidades em entidades externas de XML
Recomendação:
A tabela a seguir lista todos os analisadores XML suportados e seus níveis de segurança padrão:
- LINQ para XML
Os objetos XElemente XDocument na System.Xml.Linq biblioteca estão protegidos da injeção XXE por padrão. XElement analisa apenas os elementos no arquivo XML, portanto, as DTDs são totalmente ignoradas. XDocument tem DTDs desativados por padrão e só é inseguro se construído com um analisador XML inseguro diferente.

- XmlDictionaryReade
System.Xml.XmlDictionaryReader é seguro por padrão, como quando tenta analisar o DTD, o compilador lança uma exceção dizendo que "os elementos CData não são válidos no nível superior de um documento XML". Torna-se inseguro se construído com um analisador XML inseguro diferente.

- XmlDocument	
Antes do .NET Framework versão 4.5.2, o System.Xml.XmlDocument não é seguro por padrão. O XmlDocumentobjeto possui um XmlResolverobjeto que precisa ser definido como nulo nas versões anteriores à 4.5.2. Nas versões 4.5.2 e posteriores, isso XmlResolver é definido como nulo por padrão.

O exemplo a seguir mostra como ele é protegido:
```
 static void LoadXML()
 {
   string xxePayload = "<!DOCTYPE doc [<!ENTITY win SYSTEM 'file:///C:/Users/testdata2.txt'>]>"
                     + "<doc>&win;</doc>";
   string xml = "<?xml version='1.0' ?>" + xxePayload;

   XmlDocument xmlDoc = new XmlDocument();
   // Setting this to NULL disables DTDs - Its NOT null by default.
   xmlDoc.XmlResolver = null;
   xmlDoc.LoadXml(xml);
   Console.WriteLine(xmlDoc.InnerText);
   Console.ReadLine();
 }
```
- XmlNodeReader	
System.Xml.XmlNodeReader os objetos são seguros por padrão e ignoram as DTDs mesmo quando construídas com um analisador não seguro ou agrupadas em outro analisador não seguro.

- XmlReader	
System.Xml.XmlReader objetos são seguros por padrão.

Eles são definidos por padrão para ter sua propriedade ProhibitDtd definida como false no .NET Framework versões 4.0 e versões anteriores, ou suas DtdProcessingpropriedades definidas como proibir no .NET versões 4.0 e posteriores.

Além disso, nas versões 4.5.2 e posteriores do .NET, o XmlReaderSettingspertencente ao XmlReadertem seu valor XmlResolverdefinido como nulo por padrão, o que fornece uma camada adicional de segurança.

- XmlTextReader	
System.Xml.XmlTextReader é inseguro por padrão nas versões do .NET Framework anteriores à 4.5.2. Aqui está como torná-lo seguro em várias versões do .NET:

Defina esses valores como true para desativar completamente as DTDs em linha.
```
XmlTextReader reader = new XmlTextReader(stream);
// NEEDED because the default is FALSE!!
reader.ProhibitDtd = true;  
```
.NET 4.0 - .NET 4.5.2
No .NET Framework versão 4.0, o comportamento de análise de DTD foi alterado. 
No entanto, eles não alteraram as configurações padrão, por isso XmlTextReader ainda é vulnerável ao XXE por padrão.

Definir DtdProcessing como Prohibit faz com que o tempo de execução gere uma exceção se um <!DOCTYPE> elemento estiver presente no XML.

Para definir esse valor você mesmo, é assim:
```
XmlTextReader reader = new XmlTextReader(stream);
// NEEDED because the default is Parse!!
reader.DtdProcessing = DtdProcessing.Prohibit;  
```
Como alternativa, você pode definir a DtdProcessing propriedade como `Ignore`, que não lançará uma exceção ao encontrar um <!DOCTYPE>elemento, mas simplesmente o ignorará e não o processará. Por fim, você pode definir DtdProcessing como Parse se deseja permitir e processar DTDs embutidos.

.NET 4.5.2 e posterior
Nas versões do .NET Framework 4.5.2 e superior, XmlTextReader o interno XmlResolver é definido como nulo por padrão, tornando os XmlTextReaderDTDs ignorados por padrão. 

[Voltar](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/70/Desenvolvimento-Seguro?anchor=seguran%C3%A7a-em-entidades-externas-de-xml)

### Definição de referência insegura de objetos
Recomendação
Quando existir um recurso(objeto) que pode ser acessado por uma referência, é necessário garantir a autorização do usuário.


### Falhas de cross-site scripting
Recomendação
- Usar a biblioteca Microsoft AntiXSS "Install-Package AntiXSS" para codificação do conteúdo.

Exemplo 1:
```
<system.web>
<httpRuntime targetFramework="4.5"
enableVersionHeader="false"
encoderType="Microsoft.Security.Application.AntiXssEncoder, AntiXssLibrary"
maxRequestLength="4096" />
```
- NÃO Use o [AllowHTML] atributo ou a classe auxiliar, a @Html.Raw a menos que você realmente saiba que o conteúdo que está gravando no navegador é seguro e escapou adequadamente.
- Habilite uma Política de Segurança de Conteúdo . Isso impedirá que suas páginas acessem ativos que não devem ser capazes de acessar (por exemplo, um script malicioso):

Exemplo 2:
```

<system.webServer>
    <httpProtocol>
        <customHeaders>
            <add name="Content-Security-Policy"
                value="default-src 'none'; style-src 'self'; img-src 'self';
                font-src 'self'; script-src 'self'" />
```
Exemplo 3:

```
Antes:
this._chave = queryString["chave"] ;

Depois:
this._chave = HttpUtility.HtmlEncode(queryString["chave"].ToString()) ;
```

[Voltar](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/70/Desenvolvimento-Seguro?anchor=seguran%C3%A7a-em-execu%C3%A7%C3%A3o-de-comandos-em-aplica%C3%A7%C3%B5es---cross-site-scripting)

### Desserialização insegura
Recomendação:
Esse erro pode incluir uma das soluções a seguir ou o conjunto delas:

- Não aceitar objetos serializados de fontes não confiáveis e execute códigos de desserialização com permissão de acesso limitado.
- Para evitar esse tipo de ataque recomenda-se sempre que possível a utilização DataContractSerializer ou XmlSerializer.
- Evitar a utilização de métodos Public dentro da Camada de Apresentação (ASPX) e utilizar Private sempre que possível
- Evitar a utilização de Scripts Dinâmicos com desserialização de campos e objetos associados a JavaScripts e JQuery onde a requisição possa ser vista e modificada através da função F12 do navegador 
- Encapsular métodos e campos, protegendo dados através das camadas e aplicando regras de design patterns c# recomendados pela Microsoft
- Evitar a utilização de objetos tipo “var” dentro da camada de apresentação. Utilizar a Tipagem correta do construtor. 

Exemplo:

```
De:
var objToken = new Bec.Pregao.Core.Segurancas.AntiForgery(); 
Para:
Bec.Pregao.Core.Segurancas.AntiForgery objToken = new Bec.Pregao.Core.Segurancas.AntiForgery();
```

[Voltar](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/70/Desenvolvimento-Seguro?anchor=desserializa%C3%A7%C3%A3o-insegura)

### Falhas de monitoração 
Recomendação:
- Garanta que todas as falhas de logon, controle de acesso e validação de entrada do servidor possam ser registradas com contexto de usuário suficiente para identificar contas suspeitas ou mal-intencionadas.

- Estabeleça monitoramento e alertas eficazes para que atividades suspeitas sejam detectadas e respondidas em tempo hábil.
- Entrar em contato com a equipe de segurança para que seja avaliado como solução uma das ferramentas a seguir: (WAF, ArcSight, ElasticSearch, IPS, DIPR e ModSecurity).

[Voltar](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/70/Desenvolvimento-Seguro?anchor=falhas-de-monitora%C3%A7%C3%A3o)

### Solicitação forjada CSRF 
Recomendação:
- Nas versões .Net Core 2.0 ou superiores, é possível gerar e verificar automaticamente tokens anti-forgery.
- Embora o viewstate nem sempre seja apropriado para o desenvolvimento da web, usá-lo pode fornecer mitigação do CSRF. Para proteger o ViewState contra ataques de CSRF, é necessário definir o ViewStateUserKey :
```
protected override OnInit(EventArgs e) {
    base.OnInit(e);
    ViewStateUserKey = Session.SessionID;
}
```
Se você não usar o Viewstate, procure na página mestra do modelo padrão do ASP.NET Web Forms por um token anti-CSRF manual usando um cookie de envio duplo.
```
private const string AntiXsrfTokenKey = "__AntiXsrfToken";
private const string AntiXsrfUserNameKey = "__AntiXsrfUserName";
private string _antiXsrfTokenValue;
protected void Page_Init(object sender, EventArgs e)
{
    // The code below helps to protect against XSRF attacks
    var requestCookie = Request.Cookies[AntiXsrfTokenKey];
    Guid requestCookieGuidValue;
    if (requestCookie != null && Guid.TryParse(requestCookie.Value, out requestCookieGuidValue))
    {
       // Use the Anti-XSRF token from the cookie
       _antiXsrfTokenValue = requestCookie.Value;
       Page.ViewStateUserKey = _antiXsrfTokenValue;
    }
    else
    {
       // Generate a new Anti-XSRF token and save to the cookie
       _antiXsrfTokenValue = Guid.NewGuid().ToString("N");
       Page.ViewStateUserKey = _antiXsrfTokenValue;
       var responseCookie = new HttpCookie(AntiXsrfTokenKey)
       {
          HttpOnly = true,
          Value = _antiXsrfTokenValue
       };
       if (FormsAuthentication.RequireSSL && Request.IsSecureConnection)
       {
          responseCookie.Secure = true;
       }
       Response.Cookies.Set(responseCookie);
    }
    Page.PreLoad += master_Page_PreLoad;
}
protected void master_Page_PreLoad(object sender, EventArgs e)
{
    if (!IsPostBack)
    {
       // Set Anti-XSRF token
       ViewState[AntiXsrfTokenKey] = Page.ViewStateUserKey;
       ViewState[AntiXsrfUserNameKey] = Context.User.Identity.Name ?? String.Empty;
    }
    else
    {
       // Validate the Anti-XSRF token

if ((string)ViewState[AntiXsrfTokenKey] != _antiXsrfTokenValue ||
          (string)ViewState[AntiXsrfUserNameKey] != (Context.User.Identity.Name ?? String.Empty))
       {
          throw new InvalidOperationException("Validation of Anti-XSRF token failed.");
       }
    }
}
```
[Voltar](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/70/Desenvolvimento-Seguro?anchor=seguran%C3%A7a-contra-solicita%C3%A7%C3%A3o-forjada---cross-site-request-forgery)
