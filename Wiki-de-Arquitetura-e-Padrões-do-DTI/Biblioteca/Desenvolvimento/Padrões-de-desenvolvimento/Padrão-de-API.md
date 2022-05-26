
[[_TOC_]]

O template pronto esta disponível em: https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Layout_Padrao/_git/template_api_rest

#Criação do projeto

Crie um novo projeto usando o template "ASP.NET Core Web API"

<IMG  src="https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/c9c5b653-736e-4879-9eef-b2f17a26020b/_apis/git/repositories/3018c79f-3616-4365-b952-de2e6cfb45f5/items?path=%2Fimagens%2FItens_001.png&amp;versionDescriptor%5BversionOptions%5D=0&amp;versionDescriptor%5BversionType%5D=0&amp;versionDescriptor%5Bversion%5D=main&amp;resolveLfs=true&amp;%24format=octetStream&amp;api-version=5.0"/>

Dê um nome ao projeto, a sugestão é utilizar o modelo "SefazSP.NomeDoProjeto.Camada". 

<IMG  src="https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/c9c5b653-736e-4879-9eef-b2f17a26020b/_apis/git/repositories/3018c79f-3616-4365-b952-de2e6cfb45f5/items?path=%2Fimagens%2FItens_002.png&amp;versionDescriptor%5BversionOptions%5D=0&amp;versionDescriptor%5BversionType%5D=0&amp;versionDescriptor%5Bversion%5D=main&amp;resolveLfs=true&amp;%24format=octetStream&amp;api-version=5.0"/>

Selecione a versão do framework, neste template utilizamos o .NET Core 3.1, selecione a opção "Configure for HTTPS"

<IMG  src="https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/c9c5b653-736e-4879-9eef-b2f17a26020b/_apis/git/repositories/3018c79f-3616-4365-b952-de2e6cfb45f5/items?path=%2Fimagens%2FItens_003.png&amp;versionDescriptor%5BversionOptions%5D=0&amp;versionDescriptor%5BversionType%5D=0&amp;versionDescriptor%5Bversion%5D=main&amp;resolveLfs=true&amp;%24format=octetStream&amp;api-version=5.0"/>

#Dependências
Instalar os pacotes Nugets:

Para implementação do versionamento de APIs
- Microsoft.AspNetCore.Mvc
- Microsoft.AspNetCode.Mvc.Versioning
- Microsoft.AspNetCode.Mvc.Versioning.ApiExplorer

Para a implementação do Swagger Customizado
- Swashbuckle.AspNetCore

<IMG  src="https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/c9c5b653-736e-4879-9eef-b2f17a26020b/_apis/git/repositories/3018c79f-3616-4365-b952-de2e6cfb45f5/items?path=%2Fimagens%2FItens_004.png&amp;versionDescriptor%5BversionOptions%5D=0&amp;versionDescriptor%5BversionType%5D=0&amp;versionDescriptor%5Bversion%5D=main&amp;resolveLfs=true&amp;%24format=octetStream&amp;api-version=5.0"/>

#Configuração do versionamento de APIs
Crie uma pasta para receber as classes de configuração do projeto e adicione o arquivo ApiConfig.cs

<IMG  src="https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/c9c5b653-736e-4879-9eef-b2f17a26020b/_apis/git/repositories/3018c79f-3616-4365-b952-de2e6cfb45f5/items?path=%2Fimagens%2FItens_005.png&amp;versionDescriptor%5BversionOptions%5D=0&amp;versionDescriptor%5BversionType%5D=0&amp;versionDescriptor%5Bversion%5D=main&amp;resolveLfs=true&amp;%24format=octetStream&amp;api-version=5.0"/>

**Classe ApiConfig.cs**

```
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;

namespace SefazSP.Templates.WebAPI.Configuration
{
    public static class ApiConfig
    {
        public static void AddApiConfiguration(this IServiceCollection services)
        {
            services.AddApiVersioning(options =>
            {
                options.ReportApiVersions = true;
                options.AssumeDefaultVersionWhenUnspecified = true;
                options.DefaultApiVersion = new ApiVersion(1, 1);
            });

            services.AddVersionedApiExplorer(options =>
            {
                options.GroupNameFormat = "'v'VVV";
                options.SubstituteApiVersionInUrl = true;
            });

            services.AddControllers(options => options.EnableEndpointRouting = false);

            services.AddCors(options =>
            {
                options.AddPolicy("AllowCors",
                    builder =>
                        builder
                            .AllowAnyOrigin()
                            .AllowAnyMethod()
                            .AllowAnyHeader());
            });
        }

        public static void UseApiConfiguration(this IApplicationBuilder app, IWebHostEnvironment env)
        {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }

            app.UseHttpsRedirection();

            app.UseRouting();

            app.UseCors("AllowCors");

            app.UseEndpoints(endpoints =>
            {
                endpoints.MapControllers();
            });
        }
    }
}
```

#Configuração do Swagger:

Na mesma pasta onde foi criado o arquivo ApiConfig.cs, crie os arquivos para configuração do Swagger:
- SwaggerDefaultValues.cs
- SwaggerOptions.cs 
- SwaggerConfig.cs.

**Classe  SwaggerDefaultValues.cs**
```

using Microsoft.AspNetCore.Mvc.ApiExplorer;
using Microsoft.OpenApi.Models;
using Swashbuckle.AspNetCore.SwaggerGen;
using System.Linq;
using System.Text.Json;

namespace SefazSP.Templates.WebAPI.Configuration
{
    public class SwaggerDefaultValues : IOperationFilter
    {
        public void Apply(OpenApiOperation operation, OperationFilterContext context)
        {
            var apiDescription = context.ApiDescription;

            operation.Deprecated |= apiDescription.IsDeprecated();

            foreach (var responseType in context.ApiDescription.SupportedResponseTypes)
            {
                var responseKey = responseType.IsDefaultResponse ? "default" : responseType.StatusCode.ToString();
                var response = operation.Responses[responseKey];

                foreach (var contentType in response.Content.Keys)
                {
                    if (!responseType.ApiResponseFormats.Any(x => x.MediaType == contentType))
                    {
                        response.Content.Remove(contentType);
                    }
                }
            }

            if (operation.Parameters == null)
            {
                return;
            }

            foreach (var parameter in operation.Parameters)
            {
                var description = apiDescription.ParameterDescriptions.First(p => p.Name == parameter.Name);

                if (parameter.Description == null)
                {
                    parameter.Description = description.ModelMetadata?.Description;
                }

                if (parameter.Schema.Default == null && description.DefaultValue != null)
                {
                    var json = JsonSerializer.Serialize(description.DefaultValue, description.ModelMetadata.ModelType);
                    parameter.Schema.Default = OpenApiAnyFactory.CreateFromJson(json);
                }

                parameter.Required |= description.IsRequired;
            }
        }
    }
}

```
**Classe SwaggerOptions.cs**
*Configurando o Swagger para considerar o versionamento das APIs*

```
using Microsoft.AspNetCore.Mvc.ApiExplorer;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Options;
using Microsoft.OpenApi.Models;
using Swashbuckle.AspNetCore.SwaggerGen;

namespace SefazSP.Templates.WebAPI.Configuration
{
    public class SwaggerOptions : IConfigureOptions<SwaggerGenOptions>
    {
        readonly IApiVersionDescriptionProvider provider;
        readonly IConfiguration config;

        public SwaggerOptions(IApiVersionDescriptionProvider provider, IConfiguration config)
        {
            this.provider = provider;
            this.config = config;
        }
       
        public void Configure(SwaggerGenOptions options)
        {
            foreach (var description in provider.ApiVersionDescriptions)
            {
                options.SwaggerDoc(description.GroupName, CreateInfoForApiVersion(description));
            }

            options.DocInclusionPredicate((_, api) => !string.IsNullOrWhiteSpace(api.GroupName));

            options.TagActionsBy(api => api.GroupName);

        }

        private OpenApiInfo CreateInfoForApiVersion(ApiVersionDescription description)
        {
            var info = new OpenApiInfo()
            {
                Title = this.config.GetValue<string>("Swagger:Title"),
                Version = description.ApiVersion.ToString(),
                Description = this.config.GetValue<string>("Swagger:Description") //parâmetro do appsettings.json
            };

            if (description.IsDeprecated)
            {
                info.Description += " Esta versão da API foi descontinuada.";
            }

            return info;
        }
    }
}
```
**Classe SwaggerConfig.cs**
*Configuração da autenticação na página Swagger*

```
using System;
using System.IO;
using System.Reflection;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Options;
using Microsoft.OpenApi.Models;
using Swashbuckle.AspNetCore.SwaggerGen;

namespace SefazSP.Templates.WebAPI.Configuration
{
    public static class SwaggerConfig
    {
        public static void AddSwaggerConfiguration(this IServiceCollection services)
        {
            services.AddTransient<IConfigureOptions<SwaggerGenOptions>, SwaggerOptions>();

            services.AddSwaggerGen(options =>
            {
                options.OperationFilter<SwaggerDefaultValues>();

                var xmlFile = $"{Assembly.GetExecutingAssembly().GetName().Name}.xml";
                var xmlPath = Path.Combine(AppContext.BaseDirectory, xmlFile);
                options.IncludeXmlComments(xmlPath);

                //Aqui adicionando ao Swagger o botão para autenticação na API
                options.AddSecurityDefinition("ApiKey", new OpenApiSecurityScheme()
                {
                    Type = SecuritySchemeType.ApiKey,
                    In = ParameterLocation.Header,
                    Name = "smart-key", //Nome do parâmetro que a API esta esperando com o valor da chave
                    Description = "Key",
                });

                options.AddSecurityRequirement(new OpenApiSecurityRequirement
                {
                        {
                            new OpenApiSecurityScheme
                            {
                                Reference = new OpenApiReference { Type = ReferenceType.SecurityScheme, Id = "ApiKey" }
                            },
                            new string[] { }
                        }
                });
            });
        }

    }
}

```

A definição do nome da chave de autenticação está no trecho do código:
```

//Aqui adicionando ao Swagger o botão para autenticação na API
options.AddSecurityDefinition("ApiKey", new OpenApiSecurityScheme()
{
    Type = SecuritySchemeType.ApiKey,
    In = ParameterLocation.Header,
    Name = "smart-key", //Nome do parâmetro que a API esta esperando com o valor da chave
    Description = "Key",
});

```
No parâmetro "Name" definimos o nome da chave que será usada para a autenticação na API, este parâmetro deve ser o mesmo usando no atributo "ApiKey" detalhado no tópico [Autenticação](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/412/Padr%C3%A3o-de-API?anchor=autentica%C3%A7%C3%A3o)



#Adicionando as configurações ao projeto

No arquivo Startup.cs, adicione as linhas:
<IMG  src="https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/c9c5b653-736e-4879-9eef-b2f17a26020b/_apis/git/repositories/3018c79f-3616-4365-b952-de2e6cfb45f5/items?path=%2Fimagens%2FItens_006.png&amp;versionDescriptor%5BversionOptions%5D=0&amp;versionDescriptor%5BversionType%5D=0&amp;versionDescriptor%5Bversion%5D=main&amp;resolveLfs=true&amp;%24format=octetStream&amp;api-version=5.0"/>

<IMG  src="https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/c9c5b653-736e-4879-9eef-b2f17a26020b/_apis/git/repositories/3018c79f-3616-4365-b952-de2e6cfb45f5/items?path=%2Fimagens%2FItens_007.png&amp;versionDescriptor%5BversionOptions%5D=0&amp;versionDescriptor%5BversionType%5D=0&amp;versionDescriptor%5Bversion%5D=main&amp;resolveLfs=true&amp;%24format=octetStream&amp;api-version=5.0"/>
<br/>
<br/>

No código são adicionados os arquivos, swagger.js e swagger.css, que estão disponíveis [aqui](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Layout_Padrao/_git/template_api_rest?path=%2Fprojeto%2FSefaz.Templates%2FSefaz.Templates.WebAPI_REST%2Fwwwroot%2Fext), faça também o download da imagem "logo-sp.png" e salve todos os aquivos em: [pasta_do_projeto]\wwwroot\ext\

<IMG  src="https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/c9c5b653-736e-4879-9eef-b2f17a26020b/_apis/git/repositories/3018c79f-3616-4365-b952-de2e6cfb45f5/items?path=%2Fimagens%2FItens_008.png&amp;versionDescriptor%5BversionOptions%5D=0&amp;versionDescriptor%5BversionType%5D=0&amp;versionDescriptor%5Bversion%5D=main&amp;resolveLfs=true&amp;%24format=octetStream&amp;api-version=5.0"/>

<IMG  src="https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/c9c5b653-736e-4879-9eef-b2f17a26020b/_apis/git/repositories/3018c79f-3616-4365-b952-de2e6cfb45f5/items?path=%2Fimagens%2FItens_009.png&amp;versionDescriptor%5BversionOptions%5D=0&amp;versionDescriptor%5BversionType%5D=0&amp;versionDescriptor%5Bversion%5D=main&amp;resolveLfs=true&amp;%24format=octetStream&amp;api-version=5.0"/>
<br/>
<br/>

Configure o projeto para o Visual Studio no momento do build gere um XML com os detalhes dos parâmetros de entrada e valores de retorno de cada API, para isso edite o arquivo .csproj com ajuda de um editor de texto e adicione as linhas:

```
  <PropertyGroup>
    <TargetFramework>netcoreapp3.1</TargetFramework>
    <GenerateDocumentationFile>true</GenerateDocumentationFile>
    <NoWarn>$(NoWarn);1591</NoWarn>
  </PropertyGroup>
```

<IMG  src="https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/c9c5b653-736e-4879-9eef-b2f17a26020b/_apis/git/repositories/3018c79f-3616-4365-b952-de2e6cfb45f5/items?path=%2Fimagens%2FItens_010.png&amp;versionDescriptor%5BversionOptions%5D=0&amp;versionDescriptor%5BversionType%5D=0&amp;versionDescriptor%5Bversion%5D=main&amp;resolveLfs=true&amp;%24format=octetStream&amp;api-version=5.0"/>


No arquivo appsettings.json, adicione as chaves:

```
 "Swagger": {
    "Title": "Smart CADESP",
    "DocumentTitle": "Smart CADESP",
    "Description": "Módulo de Consulta ao CADESP"
  }
```
_Para utilizar acentuação o arquivo appsettings deve ser convertido para UTF-8, é possível fazer isso com o auxilio de um editor de textos como o Notepad++_

A partir deste ponto a aplicação esta configurada.

#Versionando as APIs

Para versionar adicione o atributo ApiVersion na controller passando como parâmetro o número da versão no formato "1.0", altere a rota para considerar a versão:

Exemplo de url original: https://localhost:44348/weatherforecast
Exemplo de url com versionamento: https://localhost:44348/v1/weatherforecast | https://localhost:44348/v1.1/weatherforecast

<IMG  src="https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/c9c5b653-736e-4879-9eef-b2f17a26020b/_apis/git/repositories/3018c79f-3616-4365-b952-de2e6cfb45f5/items?path=%2Fimagens%2FItens_011.png&amp;versionDescriptor%5BversionOptions%5D=0&amp;versionDescriptor%5BversionType%5D=0&amp;versionDescriptor%5Bversion%5D=main&amp;resolveLfs=true&amp;%24format=octetStream&amp;api-version=5.0"/>
<br />
<br />

Para preparar a api para futuras versões, altere a controller adicionando a versão ao nome, sugestão de formato: "NomeDaController_v1_Controler" onde "NomeDaController" é o nome da sua controller, "v1" a versão, e "Controller" é o sufixo da classe:

<IMG  src="https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/c9c5b653-736e-4879-9eef-b2f17a26020b/_apis/git/repositories/3018c79f-3616-4365-b952-de2e6cfb45f5/items?path=%2Fimagens%2FItens_012.png&amp;versionDescriptor%5BversionOptions%5D=0&amp;versionDescriptor%5BversionType%5D=0&amp;versionDescriptor%5Bversion%5D=main&amp;resolveLfs=true&amp;%24format=octetStream&amp;api-version=5.0"/>
<br />
<br />

A nova versão deve ter o mesmo nome e rota, mudando a versão, exemplo: "NomeDaController_v1_1_Controler" para a versão 1.1

<IMG  src="https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/c9c5b653-736e-4879-9eef-b2f17a26020b/_apis/git/repositories/3018c79f-3616-4365-b952-de2e6cfb45f5/items?path=%2Fimagens%2FItens_013.png&amp;versionDescriptor%5BversionOptions%5D=0&amp;versionDescriptor%5BversionType%5D=0&amp;versionDescriptor%5Bversion%5D=main&amp;resolveLfs=true&amp;%24format=octetStream&amp;api-version=5.0"/>
<br />
<br />

Executando a aplicação o Swagger exibe todas as versões da API

<IMG  src="https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/c9c5b653-736e-4879-9eef-b2f17a26020b/_apis/git/repositories/3018c79f-3616-4365-b952-de2e6cfb45f5/items?path=%2Fimagens%2FItens_014.png&amp;versionDescriptor%5BversionOptions%5D=0&amp;versionDescriptor%5BversionType%5D=0&amp;versionDescriptor%5Bversion%5D=main&amp;resolveLfs=true&amp;%24format=octetStream&amp;api-version=5.0"/>

Habilite a propriedade Deprecated = true nas APIs que estão descontinuadas, esta informação é exibida no Swagger.

<IMG  src="https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/c9c5b653-736e-4879-9eef-b2f17a26020b/_apis/git/repositories/3018c79f-3616-4365-b952-de2e6cfb45f5/items?path=%2Fimagens%2FItens_015.png&amp;versionDescriptor%5BversionOptions%5D=0&amp;versionDescriptor%5BversionType%5D=0&amp;versionDescriptor%5Bversion%5D=main&amp;resolveLfs=true&amp;%24format=octetStream&amp;api-version=5.0"/>
<br/>
<br/>


<IMG  src="https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/c9c5b653-736e-4879-9eef-b2f17a26020b/_apis/git/repositories/3018c79f-3616-4365-b952-de2e6cfb45f5/items?path=%2Fimagens%2FItens_016.png&amp;versionDescriptor%5BversionOptions%5D=0&amp;versionDescriptor%5BversionType%5D=0&amp;versionDescriptor%5Bversion%5D=main&amp;resolveLfs=true&amp;%24format=octetStream&amp;api-version=5.0"/>

---

<br />
<br />

#Autenticação

Configurando a autenticação:

<IMG  src="https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/c9c5b653-736e-4879-9eef-b2f17a26020b/_apis/git/repositories/3018c79f-3616-4365-b952-de2e6cfb45f5/items?path=%2Fimagens%2FItens_017.png&amp;versionDescriptor%5BversionOptions%5D=0&amp;versionDescriptor%5BversionType%5D=0&amp;versionDescriptor%5Bversion%5D=main&amp;resolveLfs=true&amp;%24format=octetStream&amp;api-version=5.0"/>

<br />
<br />

Adicione ao projeto a pasta "Attributes, adicione os arquivos "ApiKeyAttibutes.cs" e "ApiKeyConfig.cs"


Arquivo ApiKeyConfig.cs

```
namespace SefazSP.Templates.WebAPI
{
    public class ApiKeyConfig
    {
        public string KeyName { get; set; }
        public string KeyValue { get; set; }
    }
}
```

Arquivo ApiKeyAttibutes.cs:

```
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Mvc.Filters;

namespace SefazSP.Templates.WebAPI
{
    [AttributeUsage(validOn: AttributeTargets.Class | AttributeTargets.Method)]
    public class ApiKeyAttribute : Attribute, IAsyncActionFilter
    {
        public async Task OnActionExecutionAsync(ActionExecutingContext context, ActionExecutionDelegate next)
        {
             var config = new ApiKeyConfig { KeyName = "PARAMETRO_API_KEY", KeyValue = "VALOR_DA_API_KEY" }; //aqui é o nome e o valor da apiKey
            //neste exemplo o parâmetro com o a chave esta no header da solicitação
            if (!context.HttpContext.Request.Headers.TryGetValue(config.KeyName, out var extractedApiManagerKey))
            {
                context.Result = new ContentResult()
                {
                    StatusCode = 401,
                    Content = "Key não foi informada"
                };
                return;
            }

            if (!config.KeyName.Equals(extractedApiManagerKey))
            {
                context.Result = new ContentResult()
                {
                    StatusCode = 403,
                    Content = "Key inválida, acesso não autorizado"
                };
                return;
            }
           
            await next();
        }
    }
}

```

O valor "PARAMETRO_API_KEY" e "VALOR_DA_API_KEY" devem ser alterados, é recomendado salvar estes parâmetros no banco de dados.
```
var config = new ApiKeyConfig { KeyName = "PARAMETRO_API_KEY", KeyValue = "VALOR_DA_API_KEY" };
```
<br/>

Adicione o atributo "ApiKey" nas controllers onde será necessário autenticação.


<IMG  src="https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/c9c5b653-736e-4879-9eef-b2f17a26020b/_apis/git/repositories/3018c79f-3616-4365-b952-de2e6cfb45f5/items?path=%2Fimagens%2FItens_020.png&amp;versionDescriptor%5BversionOptions%5D=0&amp;versionDescriptor%5BversionType%5D=0&amp;versionDescriptor%5Bversion%5D=main&amp;resolveLfs=true&amp;%24format=octetStream&amp;api-version=5.0"/>
<br/>
<br/>

Com o atributo a api fica protegida e não é possível acessar diretamente.

<IMG  src="https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/c9c5b653-736e-4879-9eef-b2f17a26020b/_apis/git/repositories/3018c79f-3616-4365-b952-de2e6cfb45f5/items?path=%2Fimagens%2Fitens_021.png&amp;versionDescriptor%5BversionOptions%5D=0&amp;versionDescriptor%5BversionType%5D=0&amp;versionDescriptor%5Bversion%5D=main&amp;resolveLfs=true&amp;%24format=octetStream&amp;api-version=5.0"/>
<br/>
<br/>

Na configuração do Swagger foi incluido o botão "Authorize" que abre um modal onde é informado o valor apikey

<IMG  src="https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/c9c5b653-736e-4879-9eef-b2f17a26020b/_apis/git/repositories/3018c79f-3616-4365-b952-de2e6cfb45f5/items?path=%2Fimagens%2Fitens_024.png&amp;versionDescriptor%5BversionOptions%5D=0&amp;versionDescriptor%5BversionType%5D=0&amp;versionDescriptor%5Bversion%5D=main&amp;resolveLfs=true&amp;%24format=octetStream&amp;api-version=5.0"/>
<br/>
<br/>

<IMG  src="https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/c9c5b653-736e-4879-9eef-b2f17a26020b/_apis/git/repositories/3018c79f-3616-4365-b952-de2e6cfb45f5/items?path=%2Fimagens%2Fitens_022.png&amp;versionDescriptor%5BversionOptions%5D=0&amp;versionDescriptor%5BversionType%5D=0&amp;versionDescriptor%5Bversion%5D=main&amp;resolveLfs=true&amp;%24format=octetStream&amp;api-version=5.0"/>
<br/>
<br/>


<IMG  src="https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/c9c5b653-736e-4879-9eef-b2f17a26020b/_apis/git/repositories/3018c79f-3616-4365-b952-de2e6cfb45f5/items?path=%2Fimagens%2Fitens_025.png&amp;versionDescriptor%5BversionOptions%5D=0&amp;versionDescriptor%5BversionType%5D=0&amp;versionDescriptor%5Bversion%5D=main&amp;resolveLfs=true&amp;%24format=octetStream&amp;api-version=5.0"/>

#Padrão de URL


<table aria-label="Definir operações de API em termos de métodos HTTP" class="table table-sm">
<thead>
<tr>
<th><strong>Recurso</strong></th>
<th><strong>POST</strong></th>
<th><strong>GET</strong></th>
<th><strong>PUT</strong></th>
<th><strong>DELETE</strong></th>
</tr>
</thead>
<tbody>
<tr>
<td>/clientes</td>
<td>Criar um novo cliente</td>
<td>Obter todos os clientes</td>
<td>Atualização em massa de clientes</td>
<td>Remover todos os clientes</td>
</tr>
<tr>
<td>/clientes/1</td>
<td>Erro</td>
<td>Obter os detalhes do cliente 1</td>
<td>Atualizar os detalhes do cliente 1 se ele existir</td>
<td>Remover cliente 1</td>
</tr>
<tr>
<td>/clientes/1/pedidos</td>
<td>Criar um novo pedido para o cliente 1</td>
<td>Obter todos os pedidos do cliente 1</td>
<td>Atualização em massa de pedidos do cliente 1</td>
<td>Remover todos os pedidos do cliente 1</td>
</tr>
</tbody>
</table>

Links de referência:

[Design da API Web RESTful](https://docs.microsoft.com/pt-br/azure/architecture/best-practices/api-design)

#Padrão de Resposta

As respostas sempre devem ser no formado JSON e o nome das propriedades no padrão camel case ou snake case:

```
{
   camelCase: true
}

{
   snake_case: true
}
```



Como sugestão, a resposta de da API deste estar encapsulada na classe "Response", neste modelo temos a propriedade "success" que deve informar se a solicitação teve sucesso ou não, em caso de sucesso, o retorno pode ser uma mensagem de "Operação concluída com sucesso!" na propriedade "messages", ou retornar uma lista de objetos na propriedade "data". Caso de erro, a propriedade "success" deve estar como false e os erros na propriedade "messages". 

```
    public class Response
    {
        public Response()
        {

        }

        public Response(ValidationResult validationResult)
        {
            this.success = validationResult.IsValid;

            if (!validationResult.IsValid)
                this.messages = validationResult.Errors.Select(x => x.ErrorMessage).ToArray();
        }

        public Response(ValidationResult validationResult, bool success = false)
        {
            var errors = validationResult.Errors.Select(x => x.ErrorMessage).ToArray();

            this.success = success;
            this.messages = errors;
        }

        public Response(string message, bool success = true) : this(new string[] { message }, success) { }

        public Response(string[] messages, bool success = true)
        {
            this.success = success;
            this.messages = messages;
        }

        public virtual bool success { get; set; }
        public virtual string[] messages { get; set; }

    }

    public class Response<T> : Response
    {
        public Response()
        {

        }

        public Response(ValidationResult validationResult) :
            base(validationResult)
        { }

        public Response(string message, bool success = true) :
            base(message, success) => this.data = new T[] { };

        public Response(string[] messages, bool success = true) :
            base(messages, success) => this.data = new T[] { };

        public Response(IEnumerable<T> data, bool success = true) :
            this(data.ToArray(), success)
        { }

        public Response(ICollection<T> data, bool success = true) :
            this(data.ToArray(), success)
        { }

        public Response(T[] data, bool success = true)
        {
            this.success = success;
            this.data = data;
            this.messages = new string[] { };
        }

        public Response(T data, bool success = true) :
            this(new T[] { data }, success)
        { }

        public override bool success { get; set; }
        public T[] data { get; set; }
        public override string[] messages { get; set; }
    }


```

Exemplos de uso:

Em uma API de pedidos passamos o código, é feito a pesquisa e quando o pedido é encontrado o retorno é do tipo "Response<Pedidos>" que recebe no construtor o pedido encontrado. Quando o pedido não é encontrado o retorno é "Response", e no seu construtor passamos a mensagem "Pedido não foi encontrado".
```
        /// <summary>
        /// Obter pedido
        /// </summary>
        /// <param name="codigo">Código do Pedido</param>
        /// <returns></returns>
        [HttpGet]
        [Route("{codigo}")]
        [ProducesResponseType(200, Type = typeof(Response<Pedido>))]
        public IActionResult Get([FromRoute] string codigo)
        {
            var pedido = pedidos.Where(x => x.codigo == codigo).FirstOrDefault();

            if (pedido != null)
            {
                return Ok(new Response<Pedido>(pedido));
            }
            else
            {
                return Ok(new Response("Pedido não encontrado", false));
                //return BadRequest(new Response("Pedido não encontrado", false));
                //return BadRequest("Pedido não encontrado");
            }
        }
```
