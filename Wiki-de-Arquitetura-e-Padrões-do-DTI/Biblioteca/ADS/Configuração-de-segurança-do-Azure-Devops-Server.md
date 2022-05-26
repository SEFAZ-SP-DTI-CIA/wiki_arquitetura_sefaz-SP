Configuração de segurança refere-se ao gerenciamento de usuários, times (team project) e grupos, com definição de seus acessos (perfil) aos recursos oferecidos pelo ads.

[[_TOC_]]

# Membros
No contexto do Azure Devops, membro é um objeto, associado a um nome, detentor de permissões.
Um membro pode ser um usuário do AD, um grupo do ads ou um grupo do AD.

>Os projetos que precisam compartilhar grupos de usuários com outros projetos devem utilizar contas corporativas do AD e não grupos de collections do ads.

##Usuário AD
Os usuários utilizados no ads da Sefaz-SP são necessariamente [usuários da intranet](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Segurança-da-Informação/Usuários-em-aplicações), do Active Directory (AD), mantido pela equipe de operações (COI).

Dentre os tipos disponíveis, temos:
  - Conta de usuário - relacionado a pessoas.
  - Conta de serviço - usado por aplicações.
  - Conta Corporativa - conta relacionada a uma relação de outras contas do AD. Os membros de uma Conta Corporativa definem quem serão seus integrantes.

A inclusão ou exclusão de usuários em contas corporativas deve ser solicitada através de email por um de seus membros, assinado, para dtihdesk@fazenda.sp.gov.br.

##Grupo ads
Um grupo ads é uma relação de membros do próprio ads e também do AD.
Há diversos tipos de grupos no ads, com escopos variados.

###Instância
Uma instância do Azure Devops é uma aplicação instalada em algum servidor web.
A Sefaz possui duas instâncias _on premises_ (na repartição):
- ads - https://ads.intra.fazenda.sp.gov.br/tfs
- adshml - https://adshml.intra.fazenda.sp.gov.br/tfs

Para cada uma dessas instâncias há grupos que são disponíveis para todas as suas collections.
O nome desses grupos inicia por [Team Foundation] e eles pode ser adicionados a quaisquer outros grupos da mesma instância do ads, em todas suas collections.

Os membros de instância só podem ser criados e alterados pela equipe dtiwinweb através de RDM normal, com autorização do CIA.

>Os grupos de instância não podem ser modificados pela interface web, apenas pela aplicação "Administration Console" diretamente no servidor do ads.

###Collection
Uma Collection de uma instância é um grupo de projetos.
A maioria dos recursos de uma collection não se comunica com outra, como, por exemplo, seus grupos.

Os [grupos de uma collection](https://ads.intra.fazenda.sp.gov.br/tfs/PRODUTOS/_settings/security?_a=members) são disponíveis para serem utilizados por todos seus projetos.
Seus nomes iniciam com o nome da collection, por exemplo `[PRODUTOS]Readers`.

Grupos de collections são criados e modificados usando a interface web (ads) pelo CIA-NSD, através de [chamado ao Suporte_Devops](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Suporte_DevOps), área "Desenvolvimento".

###Projeto
Os [grupos de projetos](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_settings/security?_a=members) são criados e configurados dentro dos próprios projetos por seus administradores, que são os usuários cadastrados no grupo "Project Administrators".

O DTI **não pode alterar a configuração dos projetos**, mas apenas seus administradores.

>Apenas na criação do projeto ou em quaisquer outras situações em que não houver administradores acessíveis, o DTI-NSD, através de [chamado no Suporte_Devops](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Suporte_DevOps), poderá cadastrar um único usuário no grupo "Project Administrators".

###Team
Um Team Project, ou simplesmente Team, é uma espécie de grupo de projeto do ads. A diferença é que se pode associar a um time os recursos do ads, possibilitando uma visão seletiva, como um filtro. 

---
#Configuração de segurança

A [configuração de segurança do Azure Devops](https://docs.microsoft.com/en-us/azure/devops/organizations/security/permissions?view=azure-devops-2020&tabs=preview-page) pode ser feita em diversos níveis:
- Diretamente no servidor ads
  - Instância - diretamente no servidor através da aplicação ["Administration Console "](https://docs.microsoft.com/pt-br/azure/devops/server/admin/add-administrator?view=azure-devops-2020).

- Através da interface Web
  - [Collection](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/_settings/security?_a=members) - por usuários do grupo "Project Collection Administrators".
  - [Projeto](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_settings/security) - por usuários cadastrados no grupo "Project Administrators".
  - Recurso - por administradores do projeto, como em [repositórios](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_settings/repositories) ou na wiki, por exemplo.

![Wiki_seguranca.png](/.attachments/Wiki_seguranca-15fd948b-2070-4219-903c-6a15ccac6671.png =500x)
Orientações sobre permissionamento podem ser encontradas em:
https://docs.microsoft.com/en-us/azure/devops/organizations/security/permissions?view=azure-devops-2020&tabs=preview-page

> O DTI faz uma consulta sobre usuários e grupos de todos os projetos e disponibiliza nessa Wiki, no repositório Usuarios_ADS.
https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_git/Usuarios_ADS?path=%2FResultados

---
##Administradores do ads
São administradores de uma instância os usuários cadastrados no grupo "[Team Foundation]Team Foundation Administrators". 
Membros desse grupo podem administrar todas as collections e projetos da instância.

São administradores de uma collection os usuários cadastrados no grupo ["Project Collection Administrators"](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/_settings/security?_a=members).
Um membro desse grupo tem visibilidade de todos os projetos da collection e pode gerenciar todos eles.

Para administrar o ads e o adshml, foram criadas as contas corporativas no AD:
- ads
  - SVC_ADS_ROLE_ADMIN  
  . SVC_ADS_ROLE_ADMIN_COI
  . SVC_ADS_ROLE_ADMIN_CIA
  . SVC_ADS_ROLE_ADMIN_CSI
<p>

- adshml
  - SVC_ADS_ROLE_ADMIN_HML 
  . SVC_ADS_ROLE_ADMIN_COI_HML
  . SVC_ADS_ROLE_ADMIN_CIA_HML
  . SVC_ADS_ROLE_ADMIN_CSI_HML

São responsabilidades dos administradores do ads, entre outras:
- configuração das collections
- migração de projetos
- criação de projetos
- configuração de pipelines de deploy em HML e PRD
- execução de deploy automatizado em HML e PRD
- manutenção de agent job (Agent Pool e Deployment Group)
- designação de um project administrator, quando não houver algum disponível no projeto

##Gestores de projetos do ads
Foi criada uma conta corporativa no AD com o nome SVC_ADS_ROLE_READERS contendo membros que devem ter acesso de leitura a todos os projetos do ads e adshml.

Essa conta corporativa deve ser adicionada ao grupo "Readers" de todos os projetos do ads em sua criação.

