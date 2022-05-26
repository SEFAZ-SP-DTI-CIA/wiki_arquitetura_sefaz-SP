Ao desenvolver sistemas para a Sefaz-SP, atente-se aos padrões recomendados pela Sefaz.
[[_TOC_]]

# Tecnologias adotadas
[Veja as tecnologias recomendadas e suportadas](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Desenvolvimento/Padrões-de-desenvolvimento/Padrões-atualmente-utilizados) pela SEFAZ para novos desenvolvimentos.

#Gestão de produtos
A administração dos sistemas desenvolvidos pela SEFAZ é alinhada ao modelo de [gestão por produto SAFe](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Governança/SAFe-%2D-Scaled-Agile-Framework).

#Azure Devops Server
O [ADS - Azure DevOps Server](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/ADS) é um sistema de gerenciamento de código, artefatos, documentação, dashboards e backlog dos projetos de desenvolvimento adotado pela Sefaz. 

##Process template
A Sefaz adota o [Scrum](http://etc.intra.fazenda.sp.gov.br/sites/suporte_desenv/Documentos%20Compartilhados/Guias%20e%20Manuais/Scrum-Guide-2020-Portugues-BR-2.0.pdf?Web=1) como modelo de processo de desenvolvimento.

##Nuget
As bibliotecas de classes devem ser compartilhadas preferencialmente em [Pacotes NuGet](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/ADS/Recursos-do-Azure-Devops/Artifacts) armazenados no SefazFeed da collection PRODUTOS e ADMIN do ads.

##Administradores dos projetos
Somente os usuários cadastrados no grupo "Project Administrators" podem [alterar as configurações do projeto](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/ADS/Configuração-de-segurança-do-Azure-Devops-Server).
>Os usuários de todos os projetos podem ser consultados nas [planilhas gravadas no repositório Usuarios_ADS](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_git/Usuarios_ADS?path=%2FResultados).

#Segurança

##Desenvolvimento Seguro
O DTI oferece orientações para o [desenvolvimento seguro de aplicações](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Segurança-da-Informação/Desenvolvimento-Seguro).

##Autenticação
O padrão de autenticação é o [SEFAZ Identity](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Recursos/Sefaz-Identity) como STS (Security token service)
- Usuários internos: autenticação com certificado digital
- Usuários externos: login e senha com utilização do recaptcha

##Exposição de informações sensíveis
- Dados reais de pessoas ou de instituições, além de usuários e senhas, não devem ficar expostos nos códigos-fonte das aplicações.

- [Acesso a banco de dados deverá ser realizado com autenticação integrada](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Governança/Acesso-seguro-a-banco-de-dados) (**Windows Authentication**) sempre que possível ou através de quaisquer alternativas seguras.

- Bancos de dados de homologação e desenvolvimento não devem conter dados reais, como identificação de pessoas físicas e jurídicas.

##Contas de usuário para aplicações
Na configuração do IIS em servidores Wev/Windows e em Windows Service, utiliza-se [usuário do tipo **conta de serviço**](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Segurança-da-Informação/Usuários-em-aplicações) para desenvolvimento, uma para homologação e outra produção. 

#Código fonte de programa

##Controlador de versão
O [Git é a ferramenta oficial de versionamento de código de desenvolvimento](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Desenvolvimento/Git) de sistemas na Sefaz-SP.

##Branch
Não há padrão estabelecido, mas uma recomendação de [estratégias de uso de branches em repositórios de código de desenvolvimento](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Desenvolvimento/Git/Estratégias-de-branch).

##Ambientes de execução de aplicações
O DTI oferece três [ambientes para hospedagem de projetos de desenvolvimento interno: DES, HML e PRD](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Operações/Ambientes-de-hospedagem-de-aplicações). 

##Deploy
A Sefaz adota a estratégia de [estratégia de promoção de binários](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/ADS/Deploy-automatizado/Estratégias-de-promoção-de-artefatos) para o desenvolvimento de pipelines de [deploy automatizado](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/ADS/Deploy-automatizado).

#Banco de Dados
Vejas os padrões e melhores práticas em [Gestão e Modelagem de Dados](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/54/Gest%C3%A3o-e-modelagem-de-dados) e [Padrões e Melhores práticas para BD](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/184/Introdu%C3%A7%C3%A3o#documenta%C3%A7%C3%B5es-anexas%3A-padroniza%C3%A7%C3%A3o-e-melhores-pr%C3%A1ticas-adotadas-pela-sefaz-sp.).

#Navegadores
A Sefaz ainda possui diversas aplicações que se utilizam do Internet Explorer da Microsoft, porém ele não é mais suportado pela MS, portanto, orienta-se que os sistemas sejam homologados para utilização no Google Chrome e Microsoft Edge.

#Padrão visual
O Governo do Estado de São Paulo estabelece normas de comunicação institucionais no [Manual de Identidade Visual](https://www.comunicacao.sp.gov.br/comunicacao-visual-digital/).
A Sefaz desenvolve modelos utilizando essas orientações no projeto [Layout Padrão Sefaz](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Layout_Padrao).

#Material de Estudo
 Página com links interessantes para se atualizar com tecnologias de desenvolvimento (patterns, etc).