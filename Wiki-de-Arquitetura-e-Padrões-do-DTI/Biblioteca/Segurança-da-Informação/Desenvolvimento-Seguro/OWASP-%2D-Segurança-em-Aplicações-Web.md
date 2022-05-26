[Voltar](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/70/Desenvolvimento-Seguro?anchor=owasp)

[[_TOC_]]


## Open Web Application Security Project

O OWASP (Open Web Application Security Project), ou Projeto Aberto de Segurança em Aplicações Web, é uma comunidade online que cria e disponibiliza de forma gratuita artigos, metodologias, documentação, ferramentas e tecnologias no seguimento de segurança.

### OWASP TOP 10-2017
Relatório com as 10 ameaças de segurança de aplicativos da Web mais impactantes, juntamente com os métodos mais eficazes para lidar com cada ameaça.
- A1:2017-Injection 
- A2:2017-Broken Authentication 
- A3:2017-Sensitive Data Exposure 
- A4:2017-XML External Entities (XXE)
- A5:2017-Broken Access Control 
- A6:2017-Security Misconfiguration 
- A7:2017-Cross-Site Scripting (XSS) 
- A8:2017-Insecure Deserialization 
- A9:2017-Using Components with Known Vulnerabilities 
- A10:2017-Insufficient Logging & Monitoring

### A1:Injection – Injeção
SQL Injection: É uma vulnerabilidade onde o invasor pode inserir ou manipular consultas criadas pela aplicação, que são enviadas diretamente para o banco de dados ou ao sistema operacional.

![image.png](/.attachments/image-9df76d5c-48b5-48bb-b32d-d2a79dfcd583.png)

- Vulnerabilidade
Os dados fornecidos pelo utilizador não são validados, filtrados ou limpos pela aplicação.

- Prevenção
Prevenir as injeções requer que os dados estejam separados dos comandos e das consultas.
Validação dos dados de entrada do lado do servidor usando whitelists.

- Ação preventiva WAF F5
Controla assinaturas de ataques de injeção.
Restrição de tentativas de execução de caracteres.
Restrição de tamanhos de parâmetros.

[Recomendação](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/71/CSHARP-VB.NET-e-ASP.NET?anchor=inje%C3%A7%C3%A3o-de-(sql))

[Voltar](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/70/Desenvolvimento-Seguro?anchor=seguran%C3%A7a-em-chamadas-sql---inje%C3%A7%C3%A3o-de-(sql))

### A2:Broken Authentication  

Quebra de Autenticação: É uma vulnerabilidade onde o invasor consegue a confirmação da identidade do usuário, autenticação ou  gestão da sessão.

![image.png](/.attachments/image-ba739905-4a02-40f9-9ad4-b1d9989a69b3.png)

- Vulnerabilidade
A aplicação permite ataque de brute-force.
A aplicação possui senha padrões.
Processo de reset de senha fraco.
Não possui duplo fator de autenticação.

- Prevenção
Utilizar recurso de autenticação Identity.
Sempre que possível habilitar duplo fato de autenticação.
Limitar a quantidade de erros de credenciais da mesma origem.
Habilitar última versão de recaptha.

- Ação preventiva WAF F5
Proteção contra brute force.
Proteção contra enumeração de usuários.
Controle de sessão.
Proteção contra sequestro ou adulteração de cookies da aplicação.

[Recomendação](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/71/CSHARP-VB.NET-e-ASP.NET?anchor=quebra-de-autentica%C3%A7%C3%A3o)

[Voltar](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/70/Desenvolvimento-Seguro?anchor=quebra-de-autentica%C3%A7%C3%A3o)

### A3:2017-Sensitive Data Exposure 
Exposição de Dados Sensíveis: É a falta de  proteção dos dados em trânsito e quando em repouso.

![image.png](/.attachments/image-e71a609a-ca77-462b-bf35-6e1eba9c0331.png)

- Vulnerabilidade
Dados da aplicação são transmitidos por protocolos sem criptografia (HTTP).
Dados sensíveis armazenado em texto claro.
Utilização de algoritmos de criptografia não seguros.

- Prevenção
Classificar as informações para identificar quais são sensíveis.
Não armazenar dados sensíveis desnecessariamente.
Utilizar sempre protocolos com criptografia no mínimo TLS 1.2 ou superior.
Criptografar dados no banco de dados.

- Ação preventiva WAF F5
Proteção contra tentativas de roubo de informação.

[Recomendação](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/71/CSHARP-VB.NET-e-ASP.NET?anchor=exposi%C3%A7%C3%A3o-de-dados-sens%C3%ADveis)

[Voltar](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/70/Desenvolvimento-Seguro?anchor=seguran%C3%A7a-na-exposi%C3%A7%C3%A3o-de-dados-sens%C3%ADveis)

### A4:2017-XML External Entities (XXE)
Entidades Externas de XML: Essa vulnerabilidade atua em aplicações que transmitem XML, na qual o atacante consegue manipular o arquivo XML. 

![image.png](/.attachments/image-f01dd161-17e1-4d08-9865-415cc6cceb15.png)

- Vulnerabilidade
A aplicação aceita XML diretamente ou carregamentos de XML.
Qualquer um dos processadores de XML em uso na aplicação ou em serviços web baseados em SOAP permite Definição de Tipo de Documento.
Aplicação usa protocolo SOAP anterior à versão 1.2.

- Prevenção
Sanitização de entradas de XML

- Ação preventiva WAF F5
Proteção baseada em assinatura de ataques XXE

[Recomendação](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/71/CSHARP-VB.NET-e-ASP.NET?anchor=vulnerabilidades-em-entidades-externas-de-xml)

[Voltar](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/70/Desenvolvimento-Seguro?anchor=seguran%C3%A7a-em-entidades-externas-de-xml)

### A5:2017-Broken Access Control 
Quebra no controle de acesso: Essa vulnerabilidade está ligada em divulgação não autorizada de informação, modificação ou destruição de todos os dados, ou execução de funções de negócio fora dos limites do utilizador.

![image.png](/.attachments/image-ddf69585-3bac-4d56-bdaa-75e02866a8b8.png)

- Vulnerabilidade
Realizar by-pass das verificações de controle de acesso modificando o URL ou através de parâmetros da API.
Permitir a alteração da chave primária para um registo de outro usuário, permitindo visualizar ou editar a conta deste.
Forçar a navegação para páginas autenticadas como um utilizador não autenticado.

- Prevenção
Com a exceção dos recursos públicos, o acesso deve ser negado por default.
Modelar controle de acesso que assegure a propriedade dos registos.
Limitar o acesso à API e controladores de forma que minimize o impacto de ataque com recurso a ferramentas automatizadas.
Invalidar cookies de sessão após logout.
Não utilizar cookies de sessão permanentes.

- Ação preventiva WAF F5
Proteção de controle de arquivos expostos.
Proteção de blacklist e Whitelist.
Proteção com camada de autenticação.
Proteção contra sequestro de sessão.
Proteção baseada em assinatura de ataques LFI.

[Recomendação](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Segurança-da-Informação/Desenvolvimento-Seguro/Padrão-%2D-Requisito-de-Gerenciamento-De-Sessão)

[Voltar](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/70/Desenvolvimento-Seguro?anchor=quebra-do-controle-de-acesso)

### A6:2017-Security Misconfiguration 
Configuração de Segurança Incorreta: Essa vulnerabilidade está ligada a falta de requisitos de segurança na configuração de serviços e protocolos nas aplicações.

![image.png](/.attachments/image-e13af443-8709-4ce2-b465-d78be760a7d7.png)

- Vulnerabilidade
Falta medidas apropriadas de segurança em alguma parte da camada da aplicação.
Funcionalidades desnecessárias estão ativas ou instaladas.
Mensagens de erro exposta sem necessidade.
Funcionalidades de segurança encontram-se desativadas ou configuradas de forma incorreta.

- Prevenção
Habilitar Security Headers na aplicação.
Desabilitar recursos desnecessários.
Realizar Hardening no servidor de aplicação.
Desabilitar Banners de Webservice.

- Ação preventiva WAF F5
Proteção baseada em assinaturas de ataque.
Controle de métodos permitidos em requisição.

[Recomendação: Apache-Tomcat](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki?wikiVersion=GBwikiMaster&pagePath=%2FWiki%20de%20Arquitetura%20e%20Padr%C3%B5es%20do%20DTI%2FBiblioteca%2FSeguran%C3%A7a%20da%20Informa%C3%A7%C3%A3o%2FDesenvolvimento%20Seguro%2FPadr%C3%A3o%20%252D%20Requisito%20de%20Configura%C3%A7%C3%A3o%20Apache%252DTomcat)
[Recomendação: IIS](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki?wikiVersion=GBwikiMaster&pagePath=%2FWiki%20de%20Arquitetura%20e%20Padr%C3%B5es%20do%20DTI%2FBiblioteca%2FSeguran%C3%A7a%20da%20Informa%C3%A7%C3%A3o%2FDesenvolvimento%20Seguro%2FPadr%C3%A3o%20%252D%20Requisito%20de%20Configura%C3%A7%C3%A3o%20IIS)
[Recomendação-Docker:](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki?wikiVersion=GBwikiMaster&pagePath=%2FWiki%20de%20Arquitetura%20e%20Padr%C3%B5es%20do%20DTI%2FBiblioteca%2FSeguran%C3%A7a%20da%20Informa%C3%A7%C3%A3o%2FDesenvolvimento%20Seguro%2FPadr%C3%A3o%20%252D%20Requisito%20de%20Configura%C3%A7%C3%A3o%20Docker)

[Voltar](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/70/Desenvolvimento-Seguro?anchor=configura%C3%A7%C3%A3o-de-webserver)

### A7:2017-Cross-Site Scripting (XSS) 
Cross site Script: Essa vulnerabilidade permite que o atacante  ative client-side script dentro das páginas web vista por outros usuários.

![image.png](/.attachments/image-06ef570d-4b62-4e3f-8671-9a477dd627ff.png)

- Vulnerabilidade
A aplicação ou API incluem dados de entrada do usuário como parte do HTML da resposta.
A aplicação ou API armazenam dados de entrada do usuário  de forma não tratada.

- Prevenção
Habilitar Security Headers Content Security Policy
Realizar sanitização de campos antes de enviar a resposta ao usuário.

- Ação preventiva WAF F5
Proteção baseada em assinaturas de ataque XSS.
Controle de parâmetros enviados como tentativa de inserção de código.
Aplicação de atributo de cookie HTTPOnly.

[Recomendação](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/71/CSHARP-VB.NET-e-ASP.NET?anchor=falhas-de-cross-site-scripting)

[Voltar](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/70/Desenvolvimento-Seguro?anchor=seguran%C3%A7a-em-execu%C3%A7%C3%A3o-de-comandos-em-aplica%C3%A7%C3%B5es---cross-site-scripting)

### A8:2017-Insecure Deserialization 
Desserialização Insegura: Essa vulnerabilidade permite que o atacante através da desserialização de objetos acesse outros recursos que não estão disponíveis.
![image.png](/.attachments/image-8720b5fb-e51d-47a5-bad6-80c8aed2e448.png)

- Vulnerabilidade
Ataques relacionados com objetos e estruturas de dados em que o atacante consegue modificar lógica aplicacional ou executar remotamente código.
A aplicação ou API armazenam dados de entrada do usuário  de forma não tratada.

- Prevenção
Implementar verificações de integridade como assinatura digital nos objetos serializados.
Isolar e correr a lógica de desserialização, sempre que possível.

- Ação preventiva WAF F5
Proteção baseada em assinaturas de ataques do tipo de injeção de código no servidor da aplicação.

[Recomendação](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/71/CSHARP-VB.NET-e-ASP.NET?anchor=desserializa%C3%A7%C3%A3o-insegura)

[Voltar](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/70/Desenvolvimento-Seguro?anchor=desserializa%C3%A7%C3%A3o-insegura)

### A9:2017-Using Components with Known Vulnerabilities 
Usando componentes com vulnerabilidades conhecidas: Essa vulnerabilidade acontece quando á aplicação utilizar componentes de terceiros vulneráveis permitindo o comprometimento da aplicação.

![image.png](/.attachments/image-02b7edba-03a3-4573-b8c6-d6060845e9d5.png)

- Vulnerabilidade
Não conhecer as versões de todos os componentes.
Não examinar regularmente os componentes que utiliza.
Os programadores não testarem a compatibilidade com as novas versões.

- Prevenção
Remover dependências não utilizadas assim como funcionalidades.
Realizar inventário das versões.
Sempre que possível utilizar a versão mais nova de componentes.

- Ação preventiva WAF F5
Proteção baseada em assinatura.
Integrado analise dinâmica informando sobre a vulnerabilidade.


### A10:2017-Insufficient Logging & Monitoring
Registro e Monitorização Insuficiente: Essa vulnerabilidade acontece quando á uma insuficiência do registo, detecção, monitorização. 

![image.png](/.attachments/image-8baaa975-5ea6-488f-853c-b06622baffb2.png)

- Vulnerabilidade
Eventos auditáveis como autenticação, falhas e transações de valores relevantes não são registados.
Registos das aplicações e APIs não são monitorizados para detecção de atividade suspeita.
Registos são armazenados localmente. 

- Prevenção
Assegurar que todas as autenticações são registradas.
Assegurar que os registos usam um formato que possa ser facilmente consumido por uma solução de gestão de registos centralizada.
Definir processos de monitorização e alerta capazes de detectar atividade suspeita.
Utilização do LogAudit.

- Ação preventiva WAF F5
Controle de solicitação e resposta de logs.
Execução de bloqueio em casos de ataque.
Integração com Siem.
Correlação de eventos.

[Recomendação](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/71/CSHARP-VB.NET-e-ASP.NET?anchor=falhas-de-monitora%C3%A7%C3%A3o)

[Voltar](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/70/Desenvolvimento-Seguro?anchor=falhas-de-monitora%C3%A7%C3%A3o)
























