O ads organiza seus recursos em instâncias, que se dividem em collections, que agrupam projects, que podem ser subdivididos em teams.

[[_TOC_]]

# Instância
Uma instalação em um servidor é chamada de "instância" do Azure Devops.
A Sefaz oferece três instâncias do Azure Devops:
- [ads](https://ads.intra.fazenda.sp.gov.br/tfs) - Azure Devops Server é a instância principal da Sefaz.
- [adshml](https://adshml.intra.fazenda.sp.gov.br/tfs/) - Uma cópia do ads utilizado para testar o próprio Azure Devops.
- [ado](https://dev.azure.com/) - Azure Devops Services é uma instância oferecida na nuvem. Ao utilizar um login da Sefaz, o ads exibe os projetos a que o usuário tem acesso.

---

#Collection e Organization
Uma instância do Azure Devops contém uma ou mais coleções de projetos, que são chamadas de "collections" ([_Team Project Collection_](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/_settings/projects)) no Azure Devops Server (ads e adshml) e de "organizations" no Azure Devops Services (ads).

No ads, cada collection é um banco de dados SQL Server independente com características próprias.
Por isso, cada collection se comporta como se fosse uma instância de ads, fazendo com que os projetos e quase todos os recursos em collections distintas não se comuniquem diretamente.

> Collections compartilham apenas  a interface web, feeds e os [Agents Job](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Operações/Agent-Job) configurados. 

Há [dois tipos de collections](https://docs.microsoft.com/en-us/azure/devops/organizations/settings/work/inheritance-versus-hosted-xml?view=azure-devops):
- Hosted XML - antigo modelo usado até o TFS 2015, tem a sua configuração de process template feita através de arquivos XML usando o Visual Studio. 
- Inherited process - modelo atual, permite a configuração de process templates diretamente pela interface web.

Há, na Sefaz, três collections do tipo _hosted xml_:
- [TPC-2008](https://ads.intra.fazenda.sp.gov.br/tfs/TPC-2008) - fora de uso, disponível apenas para leitura
- [TPC-2010](https://ads.intra.fazenda.sp.gov.br/tfs/TPC-2010) - utiliza o antigo process template de Ocorrências. Seus projetos devem ser migrados para o scrum e mudar de collection.
- [TPC-2016](https://ads.intra.fazenda.sp.gov.br/tfs/TPC-2016) - collection atualmente em uso, mas em processo de migração.

Foram criadas duas collections inherited, disponíveis para [migração de projetos](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/ADS/Migração-de-projetos-ADS-para-Collections-Inherited):
- [PRODUTOS](https://ads.intra.fazenda.sp.gov.br/tfs/PRODUTOS) - para projetos de gestão (produtos SAFe) e seus projetos relacionados (links).
- [ADMIN](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN) - para os demais projetos.

---

#Projeto
O conceito “projeto”, no Azure Devops, se refere a um repositório de recursos utilizados no processo de desenvolvimento, implantação e manutenção de aplicações. Não se confunde com o conceito de projeto em engenharia de sistemas. 
Nesse contexto, um projeto pode conter diversas ou nenhuma aplicação.

A área de [Catálogo de Serviços](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Recursos/Catálogo-de-serviços) define serviço como algo parecido com uma aplicação. Muitas vezes, o nome do serviço coincide com o nome do projeto, mas não há necessariamente uma relação biunívoca entre um serviço e um projeto.

Um projeto no Azure DevOps da Sefaz-SP é criado pelo Núcleo de Suporte ao Desenvolvimento do Centro de Inovação e Arquitetura do DTI através de chamado pelo [Suporte_DevOps](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Suporte_DevOps/_wiki/wikis/Suporte_DevOps.wiki/125/Cria%C3%A7%C3%A3o-de-um-novo-projeto-no-ADS), mas toda a sua configuração deve ser mantida por seus administradores. O CIA-NSD apenas cria o projeto, cadastra um administrador, auxilia e orienta a equipe do projeto.

> Ao acessar o ads (https://ads.intra.fazenda.sp.gov.br/tfs), exibem-se os projetos nos quais o usuário está cadastrado. Os administradores do ads têm acesso a todos os projetos.

##Produto SAFe
Esse termo não existe no ads, mas a Sefaz criou o conceito de Produto SAFe, que é um projeto ads com as características:
- Tem seu nome iniciado pela palavra "SAFe"
- Está relacionado a outros projetos no ads por Plans, Queries e workitens
- Sua board usa apenas Epics, Features e Registros de Informação
- Não possui repos, pipelines, test plans e artifacts
- Sua Wiki contém as informações de sua equipe de gestão e uma relação dos projetos

##Times de projetos
No ads, uma equipe é chamada de _Team Project_, um grupo de usuários dentro de um projeto. 
Diferente de um [grupo ads](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/145/Configura%C3%A7%C3%A3o-de-seguran%C3%A7a-do-Azure-Devops?anchor=grupo-de-usu%C3%A1rios-ads), um team é associado a areas, iterations, dashboards, boards e backlogs, além de regras de notificações, de permissionamento e templates de criação de workitens.

Todo projeto novo começa com um team que tem o mesmo nome do projeto.
A criação de mais teams permite que um mesmo projeto gerencie mais de uma aplicação, compartilhando parte de seus recursos e separando outros.

>Para que os membros de um team project possam ter acesso aos repositórios de código-fonte do projeto, o team precisa estar cadastrado no grupo "Contributors" ou "Project Administrators".

##Classification nodes
O ads possui dois recursos para subdividir um projeto, para classificação de teams e workitens, chamados de "classification nodes", _iterations_ e _areas_, criadas em [Project Configuration](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_settings/work).

###Áreas 
Uma area é uma subdivisão de um projeto que pode ser associada a:
- teams - na [tela de configuração de áreas do team](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_settings/work-team?_a=areas).
- workitens - no preenchimento do campo "Area" do item de trabalho.

Com isso, a associação de teams a workitens é feita através das áreas.

###Iterations
Uma iteration é uma subdivisão de um projeto usada na classificação de workitens, usada pelos times para o planejamento de sprints.
Além do nome, cada iteration contém uma data de início e de fim.

Uma iteration é criada para o projeto e depois selecionada na tela de [configuração de iterations do team](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_settings/work-team?_a=iterations).

---

#Configuração de projetos
Somente os usuários cadastrados no grupo "Project Administrators" podem alterar as configurações do projeto.
> O DTI faz uma consulta a todos os projetos e disponibiliza nessa Wiki, no repositório Usuarios_ADS.
https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_git/Usuarios_ADS?path=%2FResultados

O acesso à configuração se faz ao clicar sobre o botão "[Project Settings](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_settings/)", localizado no canto inferior esquerdo da página do projeto.
- Geral
  - Overview - alteração de nome[*] e descrição do projeto, visualização dos serviço e exclusão do projeto.
  - Teams - criação dos times.
  - Security - [gerenciamento de permissões](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/ADS/Configuração-de-segurança-do-Azure-Devops-Server/Configuração-de-segurança-de-grupos).
  - Notifications - criação de regras (subscriptions) para envio de emails
  - Dashboards - configuração de segurança para dashboards
- Boards
  - Project configuration - criação e configuração de áreas e iterations
  - Team configuration - associação de áreas e iterations a teams
- [Repositories](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/26/Reposit%C3%B3rios) - configuração de repositórios

[*] O nome do Team Project não deve conter caracteres especiais, como cedilhas, acentos, espaços e símbolos. São permitidos o uso de hífen e sublinhado.
