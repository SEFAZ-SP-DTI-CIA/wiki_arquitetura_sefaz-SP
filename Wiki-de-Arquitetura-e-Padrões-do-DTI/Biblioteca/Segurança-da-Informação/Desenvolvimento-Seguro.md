  O DTI, dentro de suas atribuições, com o objetivo de orientar a construção de aplicações com um mínimo aceitável de segurança, durante o ciclo de vida do desenvolvimento, especifica um conjunto de boas práticas de segurança de acordo com indicações do [Projeto  Aberto de Segurança em Aplicações Web - OWASP](https://pt.wikipedia.org/wiki/OWASP).

[[_TOC_]]

## FORTIFY
A ferramenta FORTIFY foi avaliada para atender a demanda de desenvolvimento seguro na secretaria da fazenda em conjunto com as boas práticas da OWASP.

Fortify é uma solução de segurança líder de mercado que possibilita aos desenvolvedores realizarem testes de segurança durante o ciclo de desenvolvimento, prevenindo falhas e vulnerabilidades antes da aplicação ser disponibilizada para consumo.

Atualmente sua integração ocorre no Azure Devops através da configuração de tarefas no pipeline e repositório do código fonte da aplicação, esse processo é classificado CI - Continuos Integration.

[Veja mais:](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/38/SAST-Static-Application-Security-Testing) 

## OWASP
Open Web Application Security Project ou Projeto Aberto de Segurança em Aplicações Web, é uma comunidade online que cria e disponibiliza de forma gratuita artigos, metodologias, documentação, ferramentas e tecnologias no campo da segurança de aplicações Web.

O OWASP Top 10 é um documento de conscientização para a segurança de aplicações web. 

[Veja mais:](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/72/OWASP-Seguran%C3%A7a-em-Aplica%C3%A7%C3%B5es-Web) 

## Conversão do .Net Framework
A comunicação estabelecida entre o cliente e o Servidor deve trafegar com o protocolo que garante a criptografia da comunicação de no mínimo TLS v1.2 ou superior. Sendo assim é extremamente importante a eliminação e desativação das cifras de comunicação obsoletas e vulneráveis as versões SSL 1.0, SSL 1.1, SSL 1.2, TLS 1.0 e TLS 1.1. 

[Recomendação:](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Segurança-da-Informação/Desenvolvimento-Seguro/Procedimento-%2D-Conversão-Framework)

## Recomendações mínimas de Segurança para o desenvolvimento

**Segurança em comunicação WebService**

Confidencialidade
- Comunicação segura com HTTPS
- Utilização do TLS 1.3 ou no mínimo 1.2

Autenticação
- Utilizar o certificado da aplicação para garantia da autenticação entre aplicação e webservice. O consumidor do serviço deve verificar se o certificado do servidor foi emitido por um provedor confiável, não expirou, não foi revogado, corresponde ao nome de domínio do serviço e se o servidor provou que possui a chave privada associada ao certificado de chave pública 
- Utilizar windows authetication

[Recomendação:](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/71/CSHARP-VB.NET-e-ASP.NET?anchor=secure-transporte-webservice)

**Segurança em comunicação SSH/SFTP**
- Utilizar SSH v2
- Utilizar chave de 2048 bits
- Uso de senhas fortes de no mínimo 14 caracteres com letras, números e caracteres especiais

**Segurança em comunicação com certificados digitais**
- Utilizar chave de 2048 bits
- Algoritmo SHA224, SHA256, SHA384 ou SHA512.

**NÃO** utilizar esses protocolos inseguros para a comunicação entre aplicações, sistemas, ambientes ou qualquer forma de integração:
- FTP
- Telnet
- POP3
- SNMP v1 e v2
- SSL
- SSH v1
- TLS 1.0 e 1.1
- Nunca permitir que fique habilitado a opção de acesso pelo usuário anônimo

**Segurança em comunicação WEB**
- Protocolo: TLS 1.2 e 1.3
- Gerar certificado com chave de 2048 bits
- Cifra: AES 128/256
- Hash: SHA 256/384/512
- Key: ECDH/PKCS/Diffie-Hellman

[Recomendação:](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/71/CSHARP-VB.NET-e-ASP.NET?anchor=secure-transport-weak-ssl-protocol)

**Segurança em comunicação de E-mail**

Dados confidenciais enviados na rede em formato não criptografado estão sujeitos a serem lidos/modificados por qualquer invasor que possa interceptar o tráfego de rede.

A maioria dos provedores de serviços de e-mail modernos oferecem alternativas criptografadas diferentes que usam SSL/TLS para criptografar todos os dados enviados na rede ou que permitem o upgrade de uma conexão não criptografada existente para usar SSL/TLS.

[Recomendação:](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/71/CSHARP-VB.NET-e-ASP.NET?anchor=secure-transport%3A-mail-transmission)

**Segurança em comunicação de Banco de Dados**

O aplicativo se comunica com seu servidor de banco de dados por meio de canais não criptografados e pode representar um risco de segurança significativo para a empresa e os usuários desse aplicativo. Nesse caso, um invasor pode modificar os dados inseridos pelo usuário ou até mesmo executar comandos SQL arbitrários voltados ao servidor de banco de dados.

A maioria dos servidores de banco de dados oferecem alternativas criptografadas em portas diferentes que usam SSL/TLS para criptografar todos os dados que estão sendo enviados.

[Recomendação:](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/71/CSHARP-VB.NET-e-ASP.NET?anchor=secure-transport%3A-database)

Para validação de êxito após configuração dos requisitos de segurança na comunicação deve-se testar no site: https://www.ssllabs.com/ssltest/

### Autenticação Segura
- Deve-se optar pela integração com IDENTITY que já cobre os requisitos mínimos de segurança para autenticação
- Duplo fator de autenticação
- Utilização de captcha nas aplicações
- Utilização de senha complexa superior a 14 caracteres
- Habilitar criptografia de senhas no banco de dados.

### Segurança em cabeçalho HTTP
O cabeçalho HTTP permite que o cliente e o servidor passem informações adicionais com a solicitação ou a resposta HTTP. 

De acordo com a adoção de boas práticas de segurança durante o desenvolvimento da aplicação os seguintes cabeçalhos deverão ser utilizados para minimizar riscos de vulnerabilidades e ataques:

- Content-Security-Policy( CSP )
O cabeçalho de resposta HTTP Content-Security-Policy permite um certo controle sobre os recursos que o agente do usuário é permitido carregar para uma certa página. Com algumas pequenas exceções e políticas que envolvem especificar as origens do servidor e pontos de acessos dos scripts. Isso ajuda na remediação contra o ataque de Cross-site scripting entre sites (XSS).

[Recomendação:](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/71/CSHARP-VB.NET-e-ASP.NET?anchor=seguran%C3%A7a-em-cabe%C3%A7alho-http)

- Strict-Transport-Security (HSTS)
O cabeçalho de resposta HTTP Strict-Transport-Security (HSTS) permite que um site informe aos navegadores que ele deve ser acessado apenas por HTTPS, em vez de usar HTTP.

[Recomendação:](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/71/CSHARP-VB.NET-e-ASP.NET?anchor=hsts-http-strict-transport-security)

- X-Frame-Options
O HTTP cabeçalho de resposta pode ser usado para indicar ou não se um navegador deve ser autorizado a processar uma página. Os sites usarão isso para evitar ataques de clickjacking (Roubo de click), garantindo que seu conteúdo não seja incorporado a outros sites.

[Recomendação:](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/71/CSHARP-VB.NET-e-ASP.NET?anchor=x-frame-options)

- X-XSS-Protection
O X-XSS-Protection cabeçalho de resposta HTTP é um recurso do Internet Explorer, Chrome e Safari que impede o carregamento de páginas quando detectam ataques refletidos por scripts entre sites ( XSS ). Embora essas proteções sejam desnecessárias em navegadores modernos quando os sites implementam um forte Content-Security-Policy que desativa o uso de JavaScript embutido ( 'unsafe-inline'), eles ainda podem fornecer proteções para usuários de navegadores da Web mais antigos que ainda não oferecem suporte ao CSP.

[Recomendação:](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/71/CSHARP-VB.NET-e-ASP.NET?anchor=x-xss-protection)

- X-Content-Type-Options
O header de resposta HTTP X-Content-Type-Options é um marcador usado pelo servidor para indicar que os MIME types enviados pelos headers Content-Type não devem ser alterados e seguidos. Isto permite que o usuário opte por não participar do chamado MIME Type Sniffing ou, em outras palavras, é uma forma de dizer que os webmasters estão vendo o que você está fazendo.

[Recomendação:](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/71/CSHARP-VB.NET-e-ASP.NET?anchor=x-content-type-options)

Para validação após configurações dos cabeçalhos deve-se testar no site: https://securityheaders.com/

### Criação de contato de apoio
Security.txt é um padrão proposto que permite que sites definam políticas de segurança. 

O arquivo security.txt define diretrizes claras para os pesquisadores de segurança sobre como relatar problemas de segurança, segue sugestão do caminho para salvar o contato csirt@fazenda.sp.gov.br.
- /.well-known/security.txt

[Recomendação:](https://tools.ietf.org/html/draft-foudil-securitytxt-09)

### Gerenciamento de sessão
Uma sessão da web é uma sequência de transações de solicitações e respostas HTTP das redes associadas ao mesmo usuário.

As aplicações web podem criar sessões para acompanhar usuários após a primeira solicitação, gravando as informações escolhidas pelo usuário de forma pré e pós autenticado.

Para que isso ocorra é necessário a utilização de cookies, basicamente o cookie é um arquivo de texto cuja composição depende diretamente do conteúdo do endereço web visitado, seu funcionamento ocorre ao visitar um site, este envia um cookie como resposta para seu navegador, contendo as suas preferências em formado de texto, o arquivo é armazenado no computador.

Enquanto o cookie estiver salvo no computador, toda vez que digitar o endereço do site seu navegador irá enviar este arquivo para o site contendo suas informações básicas do primeiro acesso. Devido sua importância e frequência de ocorrência de ataques relacionados ao assunto, é de extrema importância a utilização de alguns requisitos de segurança durante a criação, sendo eles:
- Utilizar o protocolo TLS para criptografia de sessão
- Implemente HSTS [HTTP Strict Transport Security](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/71/CSHARP-VB.NET-e-ASP.NET?anchor=hsts-http-strict-transport-security) para impor conexões seguras HTTPS.

Realizar a implementação de alguns atributos:

- Secure: 
O Secure atributo cookie instrui os navegadores da Web a enviar apenas o cookie por meio de uma conexão HTTPS (SSL / TLS) criptografada.
Esse mecanismo de proteção de sessão é obrigatório para impedir a divulgação do ID da sessão através de ataques MitM (Man-in-the-Middle). Ele garante que um invasor não possa simplesmente capturar a ID da sessão do tráfego do navegador da web.

[Recomendação:](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/71/CSHARP-VB.NET-e-ASP.NET?anchor=secure)

- HTTPOnly:
O HttpOnly atributo cookie instrui os navegadores da web a não permitirem que scripts (por exemplo, JavaScript ou VBscript) possam acessar os cookies por meio do objeto DOM document.cookie. Essa proteção de ID de sessão é obrigatória para impedir o roubo de ID de sessão através de ataques XSS.

[Recomendação:](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/71/CSHARP-VB.NET-e-ASP.NET?anchor=httponly)

- SameSite:
O SameSite permite que um servidor defina um atributo de cookie, impossibilitando o navegador de enviar esse cookie junto com solicitações entre sites. O objetivo principal é reduzir o risco de vazamento de informações entre origens e fornece alguma proteção contra ataques de falsificação de solicitações entre sites.

[Recomendação:](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/71/CSHARP-VB.NET-e-ASP.NET?anchor=samesite)

- Method HTTP:
Por padrão, o .NET Framework permite todos os verbos HTTP e, portanto, mesmo que essa configuração negue GETs e POSTs para todos os usuários, que não impede solicitações HEAD. Talvez seja possível para um invasor usufruir de funcionalidades administrativas substituindo solicitações GET ou POST por solicitações HEAD. 

[Recomendação:](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/71/CSHARP-VB.NET-e-ASP.NET?anchor=method-http)

- Expiração da Sessão:
Os mecanismos de gerenciamento de sessões baseados em cookies podem fazer uso de dois tipos de cookies, cookies não persistentes (ou de sessão) e cookies persistentes. Portanto, é altamente recomendável usar cookies não persistentes para fins de gerenciamento de sessões, para que o ID da sessão não permaneça no cache do cliente da web por longos períodos de tempo, de onde um invasor pode obtê-lo.

Aplicar intervalos de tempo limite, normalmente são de 2 a 5 minutos para aplicativos de alto valor e 15 a 30 minutos para aplicativos de baixo risco, quando uma sessão expirar o aplicativo deverá invalidar a sessão nos dois lados, cliente e servidor.

[Recomendação:](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/71/CSHARP-VB.NET-e-ASP.NET?anchor=politica-de-cookies)

- Validação de entrada
Entradas não verificadas são a principal causa de vulnerabilidades em aplicativos ASP.NET. Entradas não verificadas podem resultar em muitas vulnerabilidades, incluindo cross-site scripting (XSS), process control e SQL injection. No caso do XSS, vetores que permitem a execução indevida de scripts estão listados nesse [cheat sheet](https://portswigger.net/web-security/cross-site-scripting/cheat-sheet).

Embora a validação esteja habilitada por padrão, você deve torná-la explícita, habilitando a estrutura de validação no seu arquivo Web.config. Um exemplo de uma configuração típica é:

[Recomendação:](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/71/CSHARP-VB.NET-e-ASP.NET?anchor=valida%C3%A7%C3%A3o-de-entrada)


### Criptografia
O Objetivo principal por trás da criptografia está em transformar uma informação original em outra não inteligível num processo que pode ser revertido, ou seja, a informação poderá voltar à sua forma inicial desde que combinada com sua chave. Sem a chave, a decodificação não é possível, o que significa restrição de acesso, característica muito interessante para sistemas de segurança de informação
Devido a essa necessidade segue algumas recomendações para diferentes tipos de criptografia em diferentes cenários de uso desde a comunicação até o armazenamento de dados com segurança.

[Recomendação:](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/71/CSHARP-VB.NET-e-ASP.NET?anchor=criptografia)


O OWASP apresenta as classificações e contramedidas de segurança de aplicativos web mais vulneráveis e explorados.
- Injeção de (SQL)
- Injeção de códigos maliciosos
- Quebra de autenticação
- Exposição de dados sensíveis
- Vulnerabilidades em entidades externas de XML
- Falhas de cross-site scripting
- Desserialização insegura
- Falhas de monitoração
- Solicitação forjada CSRF
- Falhas de validação de redirecionamento.


### Segurança em chamadas SQL - Injeção de (SQL)
- Esse é um tipo de falha em que comandos SQL são injetados em parâmetros manipuláveis pelo usuário com o objetivo de executar comandos SQL pré-definidos.

[Explicação:](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/72/OWASP-Seguran%C3%A7a-em-Aplica%C3%A7%C3%B5es-Web?anchor=a1%3Ainjection-%E2%80%93-inje%C3%A7%C3%A3o)
[Recomendação:](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/71/CSHARP-VB.NET-e-ASP.NET?anchor=inje%C3%A7%C3%A3o-de-(sql))

### Segurança de parâmetros manipulados pelo usuário - Injeção de Código
- Esse tipo de falha é caracterizado pela execução de código não autorizado na aplicação ou no ambiente em que ela se encontra. Essa vulnerabilidade ocorre devido à falta de sanitização dos parâmetros manipuláveis pelo usuário.

[Recomendação:](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/71/CSHARP-VB.NET-e-ASP.NET?anchor=inje%C3%A7%C3%A3o-de-c%C3%B3digos-maliciosos)

### Quebra de Autenticação
- É uma vulnerabilidade onde o invasor consegue a confirmação da identidade do usuário, autenticação ou gestão da sessão. 

[Explicação:](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/72/OWASP-Seguran%C3%A7a-em-Aplica%C3%A7%C3%B5es-Web?anchor=a2%3Abroken-authentication)
[Recomendação:](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/71/CSHARP-VB.NET-e-ASP.NET?anchor=quebra-de-autentica%C3%A7%C3%A3o)

### Segurança na exposição de dados sensíveis
- Exposição de informações podem facilitar o entendimento sobre o funcionamento da aplicação e das tecnologias envolvidas, buscando informações muitas vezes disponíveis em texto claro, ou sem a criptografia adequada.

[Explicação:](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/72/OWASP-Seguran%C3%A7a-em-Aplica%C3%A7%C3%B5es-Web?anchor=a3%3A2017-sensitive-data-exposure)
[Recomendação:](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/71/CSHARP-VB.NET-e-ASP.NET?anchor=exposi%C3%A7%C3%A3o-de-dados-sens%C3%ADveis)

### Segurança em entidades externas de XML
- Esse tipo de falha ocorre quando um parse XML não processa adequadamente a entrada do usuário que contém uma declaração de entidade externa no tipo de documento de pacote XML.

[Explicação:](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/72/OWASP-Seguran%C3%A7a-em-Aplica%C3%A7%C3%B5es-Web?anchor=a4%3A2017-xml-external-entities-(xxe))
[Recomendação:](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/71/CSHARP-VB.NET-e-ASP.NET?anchor=vulnerabilidades-em-entidades-externas-de-xml)

### Quebra do Controle de Acesso
- Quebra no controle de acesso: Essa vulnerabilidade está ligada em divulgação não autorizada de informação, modificação ou destruição de todos os dados, ou execução de funções de negócio fora dos limites do utilizador.

[Explicação:](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/72/OWASP-Seguran%C3%A7a-em-Aplica%C3%A7%C3%B5es-Web?anchor=a5%3A2017-broken-access-control)
[Recomendação:](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Segurança-da-Informação/Desenvolvimento-Seguro/Padrão-%2D-Requisito-de-Gerenciamento-De-Sessão)

### Configuração de WebServer
- Configuração de Segurança Incorreta: Essa vulnerabilidade está ligada a falta de requisitos de segurança na configuração de serviços e protocolos nas aplicações.

[Explicação:](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/72/OWASP-Seguran%C3%A7a-em-Aplica%C3%A7%C3%B5es-Web?anchor=a6%3A2017-security-misconfiguration)
[Recomendação-Apache-Tomcat:](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Segurança-da-Informação/Desenvolvimento-Seguro/Padrão-%2D-Requisito-de-Configuração-Apache%2DTomcat)
[Recomendação-IIS:](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Segurança-da-Informação/Desenvolvimento-Seguro/Padrão-%2D-Requisito-de-Configuração-IIS)
[Recomendação-Docker:](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Segurança-da-Informação/Desenvolvimento-Seguro/Padrão-%2D-Requisito-de-Configuração-Docker)

### Segurança em execução de comandos em aplicações - Cross-Site Scripting
- Caracterizado pela existência de pontos de injeção que possibilitam a um eventual usuário inserir dados capazes de interferir não somente o conteúdo das páginas HTML mas também em sua estrutura. Esses dados são então renderizados na interface do cliente sem um devido tratamento, ocasionando efetivamente o problema.

[Explicação:](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/72/OWASP-Seguran%C3%A7a-em-Aplica%C3%A7%C3%B5es-Web?anchor=a7%3A2017-cross-site-scripting-(xss))
[Recomendação:](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/71/CSHARP-VB.NET-e-ASP.NET?anchor=falhas-de-cross-site-scripting)

### Desserialização insegura
- Desserialização consiste em pegar dados estruturados de algum formato (dados serializados) e reconstruindo-os em um objeto. Estas funcionalidades podem ser exploradas para efeitos maliciosos quando se opera com dados não confiáveis.

[Explicação:](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/72/OWASP-Seguran%C3%A7a-em-Aplica%C3%A7%C3%B5es-Web?anchor=a8%3A2017-insecure-deserialization)
[Recomendação:](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/71/CSHARP-VB.NET-e-ASP.NET?anchor=desserializa%C3%A7%C3%A3o-insegura)

### Falhas de monitoração 
- A falha acontece quando um sistema não grava logs suficientes, ou quando não tem um sistema de monitoramento adequado e integro para registro de informações e devido a essa falta de registro toda a segurança de um sistema pode ser comprometida. 

[Explicação:](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/72/OWASP-Seguran%C3%A7a-em-Aplica%C3%A7%C3%B5es-Web?anchor=a10%3A2017-insufficient-logging-%26-monitoring)
[Recomendação:](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/71/CSHARP-VB.NET-e-ASP.NET?anchor=falhas-de-monitora%C3%A7%C3%A3o)

### Segurança contra solicitação forjada - Cross-Site Request Forgery
- A solicitação entre sites forjada (também conhecida como XSRF ou CSRF) é um ataque contra aplicativos hospedados na Web, nos quais um aplicativo Web mal-intencionado pode influenciar a interação entre um navegador cliente e um aplicativo Web que confia nesse navegador. Esses ataques são possíveis porque os navegadores da Web enviam automaticamente alguns tipos de tokens de autenticação com cada solicitação a um site.

[Recomendação:](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/71/CSHARP-VB.NET-e-ASP.NET?anchor=solicita%C3%A7%C3%A3o-forjada-csrf)