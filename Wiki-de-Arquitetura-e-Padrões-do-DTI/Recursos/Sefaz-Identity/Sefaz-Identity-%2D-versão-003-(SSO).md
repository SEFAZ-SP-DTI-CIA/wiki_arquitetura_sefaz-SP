# Sefaz Identity V003

## Pontos Importantes

1. Configurar **TipoLogin** implica na obrigatoriedade da validação completa (pela aplicação cliente) de todo e qualquer token de autenticação recebido do Sefaz Identity. É obrigatória a utilização do Serviço de Validação de Tokens do Sefaz Identity para fazer essa validação. Se essa validação não for feita, sua aplicação ficará vulnerável para aceitar tipos de logins indesejados.
1. Configurar o **ClaimSets** para solicitar apenas as claims que são efetivamente utilizadas pela aplicação. Caso não tenha certeza do conjunto de Claims necessárias, ao menos, valide a necessidade de claims CADESP e LDAP que são as mais onerosas para consultar. Para facilitar use ClaimSets = **80F30E33** que devolve todas as claims menos as do CADESP e LDAP.

<br/>

[
Caso algum dia seja possível mostrar arquivos diretamente (ex: PDF), todos os links desta wiki poderão ser alterados.
Por exemplo:
https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/_git/Wiki_Arquitetura?path=%2FSefaz%20Identity%2Fv003%2FUtiliza%C3%A7%C3%A3o%20do%20Sefaz%20Identity%20em%20Aplica%C3%A7%C3%B5es%20.NET%204.5.pdf&version=GBmaster
em vez de
https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/4d6df5f0-2d1f-4431-b769-f063824bd57e/_apis/git/repositories/1885edf5-80ac-4cbb-b3e3-bdf0ea291276/items?path=%2FSefaz%20Identity%2Fv003%2FUtiliza%C3%A7%C3%A3o%20do%20Sefaz%20Identity%20em%20Aplica%C3%A7%C3%B5es%20.NET%204.5.pdf&versionDescriptor%5BversionOptions%5D=0&versionDescriptor%5BversionType%5D=0&versionDescriptor%5Bversion%5D=master&resolveLfs=true&%24format=octetStream&api-version=5.0
]: #

**Utilização do Sefaz Identity**
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**[.NET Framework](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/4d6df5f0-2d1f-4431-b769-f063824bd57e/_apis/git/repositories/1885edf5-80ac-4cbb-b3e3-bdf0ea291276/items?path=%2FSefaz%20Identity%2Fv003%2FUtiliza%C3%A7%C3%A3o%20do%20Sefaz%20Identity%20em%20Aplica%C3%A7%C3%B5es%20.NET%204.5.pdf&versionDescriptor%5BversionOptions%5D=0&versionDescriptor%5BversionType%5D=0&versionDescriptor%5Bversion%5D=master&resolveLfs=true&%24format=octetStream&api-version=5.0)**
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**[.NET Core](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/418/Utiliza%C3%A7%C3%A3o-do-Sefaz-Identity)**

**[Endereços dos Serviços do Sefaz Identity](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/414/Endere%C3%A7os-dos-Servi%C3%A7os-do-Sefaz-Identity)**

**[Exemplos](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/415/Exemplos)**

[Bibliotecas](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/416/Bibliotecas)

[Aplicações para Verificação de Certificados de ACs](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/417/Aplica%C3%A7%C3%B5es-para-Verifica%C3%A7%C3%A3o-de-Certificados-de-ACs)

[Guia Rápido de Migração para a v003 do Sefaz Identity](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/4d6df5f0-2d1f-4431-b769-f063824bd57e/_apis/git/repositories/1885edf5-80ac-4cbb-b3e3-bdf0ea291276/items?path=%2FSefaz%20Identity%2Fv003%2FGuia%20R%C3%A1pido%20de%20Migra%C3%A7%C3%A3o%20para%20a%20v003%20do%20Sefaz%20Identity.pdf&versionDescriptor%5BversionOptions%5D=0&versionDescriptor%5BversionType%5D=0&versionDescriptor%5Bversion%5D=master&resolveLfs=true&%24format=octetStream&api-version=5.0)

<br/>

---

# Dúvidas/Problemas Comuns

<br/>

**Dificuldades para usar o [reCAPTCHA no IE](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/4d6df5f0-2d1f-4431-b769-f063824bd57e/_apis/git/repositories/1885edf5-80ac-4cbb-b3e3-bdf0ea291276/items?path=%2FSefaz%20Identity%2Fv003%2FreCAPTCHA%20no%20IE.pdf&versionDescriptor%5BversionOptions%5D=0&versionDescriptor%5BversionType%5D=0&versionDescriptor%5Bversion%5D=master&resolveLfs=true&%24format=octetStream&api-version=5.0)**

**Dificuldades na [Utilização de Balanceador de Carga](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/4d6df5f0-2d1f-4431-b769-f063824bd57e/_apis/git/repositories/1885edf5-80ac-4cbb-b3e3-bdf0ea291276/items?path=%2FSefaz%20Identity%2Fv003%2FUtiliza%C3%A7%C3%A3o%20de%20Balanceador%20de%20Carga.pdf&versionDescriptor%5BversionOptions%5D=0&versionDescriptor%5BversionType%5D=0&versionDescriptor%5Bversion%5D=master&resolveLfs=true&%24format=octetStream&api-version=5.0)**

**Dificuldades na validação de tokens: [Configuração de Proxy](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/4d6df5f0-2d1f-4431-b769-f063824bd57e/_apis/git/repositories/1885edf5-80ac-4cbb-b3e3-bdf0ea291276/items?path=%2FSefaz%20Identity%2Fv003%2FConfigura%C3%A7%C3%A3o%20de%20Proxy.pdf&versionDescriptor%5BversionOptions%5D=0&versionDescriptor%5BversionType%5D=0&versionDescriptor%5Bversion%5D=master&resolveLfs=true&%24format=octetStream&api-version=5.0) | [Arquivos](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/4d6df5f0-2d1f-4431-b769-f063824bd57e/_apis/git/repositories/1885edf5-80ac-4cbb-b3e3-bdf0ea291276/items?path=%2FSefaz%20Identity%2Fv003%2FConfigura%C3%A7%C3%A3o%20de%20Proxy.zip&versionDescriptor%5BversionOptions%5D=0&versionDescriptor%5BversionType%5D=0&versionDescriptor%5Bversion%5D=master&resolveLfs=true&%24format=octetStream&api-version=5.0)**

**Possíveis causas para o [Erro 403](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/4d6df5f0-2d1f-4431-b769-f063824bd57e/_apis/git/repositories/1885edf5-80ac-4cbb-b3e3-bdf0ea291276/items?path=%2FSefaz%20Identity%2Fv003%2FErro403.pdf&versionDescriptor%5BversionOptions%5D=0&versionDescriptor%5BversionType%5D=0&versionDescriptor%5Bversion%5D=master&resolveLfs=true&%24format=octetStream&api-version=5.0)**

**Como [Exportar Certificado](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/4d6df5f0-2d1f-4431-b769-f063824bd57e/_apis/git/repositories/1885edf5-80ac-4cbb-b3e3-bdf0ea291276/items?path=%2FSefaz%20Identity%2Fv003%2FExportarCertificado.pdf&versionDescriptor%5BversionOptions%5D=0&versionDescriptor%5BversionType%5D=0&versionDescriptor%5Bversion%5D=master&resolveLfs=true&%24format=octetStream&api-version=5.0)**

**Dificuldades com Session/Cookies: [SameSite](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/4d6df5f0-2d1f-4431-b769-f063824bd57e/_apis/git/repositories/1885edf5-80ac-4cbb-b3e3-bdf0ea291276/items?path=%2FSefaz%20Identity%2Fv003%2FSameSite.pdf&versionDescriptor%5BversionOptions%5D=0&versionDescriptor%5BversionType%5D=0&versionDescriptor%5Bversion%5D=master&resolveLfs=true&%24format=octetStream&api-version=5.0)**

**Bad Request - [Erro 400](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/4d6df5f0-2d1f-4431-b769-f063824bd57e/_apis/git/repositories/1885edf5-80ac-4cbb-b3e3-bdf0ea291276/items?path=%2FSefaz%20Identity%2Fv003%2FErro400.pdf&versionDescriptor%5BversionOptions%5D=0&versionDescriptor%5BversionType%5D=0&versionDescriptor%5Bversion%5D=master&resolveLfs=true&%24format=octetStream&api-version=5.0)**

**Dificuldades no uso da v003: [ID3206: A SignInResponse message may only redirect within the current web application](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/4d6df5f0-2d1f-4431-b769-f063824bd57e/_apis/git/repositories/1885edf5-80ac-4cbb-b3e3-bdf0ea291276/items?path=%2FSefaz%20Identity%2Fv003%2FID3206.pdf&versionDescriptor%5BversionOptions%5D=0&versionDescriptor%5BversionType%5D=0&versionDescriptor%5Bversion%5D=master&resolveLfs=true&%24format=octetStream&api-version=5.0)**

**Dificuldades após a [Eliminação de Cifras Vulneráveis](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/4d6df5f0-2d1f-4431-b769-f063824bd57e/_apis/git/repositories/1885edf5-80ac-4cbb-b3e3-bdf0ea291276/items?path=%2FSefaz%20Identity%2Fv003%2FElimina%C3%A7%C3%A3o%20de%20Cifras%20Vulner%C3%A1veis.pdf&versionDescriptor%5BversionOptions%5D=0&versionDescriptor%5BversionType%5D=0&versionDescriptor%5Bversion%5D=master&resolveLfs=true&%24format=octetStream&api-version=5.0)**

**Dificuldades causadas por [Erros de Rede](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/4d6df5f0-2d1f-4431-b769-f063824bd57e/_apis/git/repositories/1885edf5-80ac-4cbb-b3e3-bdf0ea291276/items?path=%2FSefaz%20Identity%2Fv003%2FErros%20de%20Rede.pdf&versionDescriptor%5BversionOptions%5D=0&versionDescriptor%5BversionType%5D=0&versionDescriptor%5Bversion%5D=master&resolveLfs=true&%24format=octetStream&api-version=5.0)**

**Possíveis causas para o [Erro 404](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/4d6df5f0-2d1f-4431-b769-f063824bd57e/_apis/git/repositories/1885edf5-80ac-4cbb-b3e3-bdf0ea291276/items?path=%2FSefaz%20Identity%2Fv003%2FErro404.pdf&versionDescriptor%5BversionOptions%5D=0&versionDescriptor%5BversionType%5D=0&versionDescriptor%5Bversion%5D=master&resolveLfs=true&%24format=octetStream&api-version=5.0)**

**Não é possível acessar esse site [ERR_SSL_CLIENT_AUTH_SIGNATURE_FAILED](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/4d6df5f0-2d1f-4431-b769-f063824bd57e/_apis/git/repositories/1885edf5-80ac-4cbb-b3e3-bdf0ea291276/items?path=%2FSefaz%20Identity%2Fv003%2FERR_SSL_CLIENT_AUTH_SIGNATURE_FAILED.pdf&versionDescriptor%5BversionOptions%5D=0&versionDescriptor%5BversionType%5D=0&versionDescriptor%5Bversion%5D=master&resolveLfs=true&%24format=octetStream&api-version=5.0)**

<br/>

**Se as instruções acima não forem suficientes para resolver o problema: <a href="https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Suporte_DevOps/_workitems/create/Product%20Backlog%20Item?templateId=3987f228-cfa0-4737-a63b-0842ab420ca1&ownerId=9657433a-6110-4733-8620-92a4c2a5d51b" target="_blank">Abrir um PBI</a>**

<br/>

[Sefaz Identity](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/91/Sefaz-Identity)
