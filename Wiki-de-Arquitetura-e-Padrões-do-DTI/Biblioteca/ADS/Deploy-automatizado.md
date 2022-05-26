Deploy é uma sequência de tarefas organizada pelos times de desenvolvimento em conjunto com as equipes de operações, com a arbitragem do centro de arquitetura, para implantação ou manutenção de um sistema.  
Automatização é a execução de tarefas por computadores.

Deploy automatizado é uma sequência de tarefas (pipeline) realizadas por computadores que toma o código-fonte desenvolvido (repositório), cria artefatos necessários à execução da aplicação (pipeline de build), faz transformações nos arquivos de configuração, quando necessário, e implanta esses artefatos (pipeline de release - deploy) em servidores de aplicações.

#Ambientes de execução da aplicação
Um mesmo conjunto de artefatos pode ser implantado em diversas infraestruturas, chamadas de ambientes de execução, para atender a variadas finalidades.

Recomenda-se que a mesma aplicação seja utilizada por testadores em uma ou mais infraestruturas (chamadas de ambiente de testes, desenvolvimento ou [QA](https://pt.wikipedia.org/wiki/Garantia_da_qualidade)) separadas daquela utilizada pelo usuário final, para evitar que esses testes prejudiquem as informações reais e também para que aqueles não tenham acesso a informações sigilosas.

Algumas vezes, as diferenças entre o ambiente de produção e de desenvolvimento podem comprometer a eficácia dos testes, seja por causa dos dados acessados ou da própria infraestrutura. 
Além disso, há testes que verificam se a aplicação atende a requisitos de negócio que nem sempre os testadores podem ou conseguem realizar, seja por restrição de acesso ou de conhecimento. 

Para atender a essas restrições, é comum a contribuição de usuários denominados homologadores que apresentam um perfil mais próximo do usuário final, com conhecimento do negócio e autorização para acessar dados sigilosos.

Assim, para esses colaboradores, algumas organizações adotam ambientes intermediários de testes, chamados de homologação, stage ou pré-produção. Em tese, esses ambientes de homologação oferecem recursos iguais ou muito próximos daquele do qual os usuários finais irão usufruir, quando possível. 

Após a finalização dos testes sobre uma versão do sistema, os seus artefatos podem ser promovidos (implantados) para o ambiente de homologação. 
Depois de evidenciar a adequação da aplicação no ambiente de homologação, promovem-se seus artefatos para a utilização pelos usuários finais, em ambiente de produção.
Há diversas técnicas para essa [promoção de artefatos da aplicação](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/ADS/Deploy-automatizado/Estratégias-de-promoção-de-artefatos) entre os ambientes.

A Sefaz adota a estratégia de promoção de "binários" e mantém [três ambientes](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Operações/Ambientes-de-hospedagem-de-aplicações) para o desenvolvimento de [pipelines de deploy](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/ADS/Deploy-automatizado/Release-pipeline).

#Integração e entrega contínua

A [Integração Contínua](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Arquitetura/Maturidade-de-Sistemas/Nível-1/Integração-Contínua-\(CI\)) (CI) consiste na criação automatizada dos artefatos a cada vez que o código do repositório é modificado.

A [Entrega Contínua](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Arquitetura/Maturidade-de-Sistemas/Nível-2/Entrega-Contínua-\(CD\)) (CD) é a execução automatizada do deploy após a alteração dos artefatos (build). Recomenda-se que só se configure a entrega contínua somente em ambiente de desenvolvimento ou de testes, mantendo o disparo manual de deploy em ambientes (stages) homologação e produção.

Essas duas técnicas são pontos de [avaliação de maturidade de sistema](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Arquitetura/Maturidade-de-Sistemas) da Sefaz.

#Pipeline de Deploy 
A sequencia organizada de tarefas é denominada de pipeline.
No ads, um pipeline de deploy se divide em dois tipos:
- [Pipeline (Build)](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/ADS/Deploy-automatizado/Build-pipeline) - Cria os artefatos para o deploy a partir de um código fonte disponível nos repositórios do projeto. 

- [Pipeline de Release](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/ADS/Deploy-automatizado/Release-pipeline) - é o deploy, propriamente. Toma os artefatos gerados na build e os implanta nos ambientes necessários. 

Para gerenciar pipelines de um projeto no ads, acesse https://ads.intra.fazenda.sp.gov.br/tfs, selecione um projeto e clique em “Pipelines”, para build, ou "Pipelines" e depois em "Release", para deploy.

Há diversos tipos possíveis de pipelines. Nessa wiki, encontram-se instruções e orientações para o desenvolvimento de pipelines de aplicações e de bancos de dados. 

#Pipelines de aplicações
##Criação dos pipelines
1. A equipe de desenvolvimento cria os [pipelines de build](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/ADS/Deploy-automatizado/Build-pipeline) para aplicações. 
1. A equipe cria os [pipelines de release](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/ADS/Deploy-automatizado/Release-pipeline) para os ambientes (stages) de desenvolvimento e testes (QA).
1. Funcionando corretamente, através de [RDM para configuração de Deploy Automatizado](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Operações/Solicitações-de-infraestrutura/Requisição-de-Mudanças/RDM-para-configuração-de-Deploy-Automatizado), solicita-se à equipe de operações que configure o pipeline de release para o ambiente de homologação usando o stage de desenvolvimento como referência. 
1. A equipe de desenvolvimento configura um stage para a geração de [RDM para Deploy em HML](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Operações/Solicitações-de-infraestrutura/Requisição-de-Mudanças/RDM-Automatizada), para poder executar o stage de HML.
1. Estando tudo em ordem, uma nova RDM deve ser criada para a configuração do stage de produção.
1. A equipe de desenvolvimento configura um stage para a geração de [RDM ara Deploy em PRD](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Operações/Solicitações-de-infraestrutura/Requisição-de-Mudanças/RDM-Automatizada), para poder executar o stage de PRD.

**Por determinação do DTI, todos os stages de deploy automatizado nos ambientes de homologação e produção devem ser configurados com pre-deployment approvals da equipe dtiwinweb.**

##Execução do deploy
1. Quando houver alteração de código no repositório da aplicação, um novo conjunto de artefatos será criado e um deploy em desenvolvimento será feito.
1. Estando em ordem, uma RDM pode ser criada pela equipe do projeto, usando a task configurada no mesmo pipeline de release, que gera um link na tarefa da equipe de operações.
1. A equipe de operações:
   1. Abre task da RDM.
   1. Acessa o pipeline pelo link registrado na task.
   1. Verifica se o deploy em DEV foi bem sucedido.
   1. Caso negativo, registra na RDM que o deploy não foi realizado porque o stage de DEV não foi bem sucedido.
   1. Se estiver tudo em ordem, executa o deploy em homologação. 
   1. Havendo falha na execução, o analista de operações verifica se consegue identificar e resolver o problema em conjunto com a equipe de desenvolvimento.
   1. Finaliza a RDM.

1. Bem sucedido o deploy em homologação e verificada a boa utilização da aplicação em homologação, o responsável pelo projeto poderá executar o stage de geração de RDM para PRD, que será posteriormente realizado pela equipe de operações.

Mesmo que haja falha no deploy, a RDM será finalizada, com o registro dos problemas encontrados e das tentativas de correção.

Tanto um [roll back](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/ADS/Deploy-automatizado/Release-pipeline/Gerenciamento-de-releases) como um deploy corretivo deverá ser feito com outra RDM gerada em nova release ou por Redeploy em qualquer release.

#Pipelines de banco de dados (provisório)
O deploy automatizado de banco de dados na Sefaz só pode ser feito para SQL Server e com projetos de banco de dados (sqlproj).

**Esses procedimentos ainda estão em estudo.
Ainda não foram definidos com as equipes de operações.**

##Criação dos pipelines
1. A equipe de desenvolvimento cria um projeto de banco de dados (sqlproj).
1. A equipe de desenvolvimento cria um pipelines de build para banco de dados, que vai gerar o arquivo dacpac, como o exemplo na [aplicação modelo Layout Padrão](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Layout_Padrao/_apps/hub/ms.vss-ciworkflow.build-ci-hub?_a=edit-build-definition&id=45). 
1. A equipe DBA do CDS (equipedba@fazenda.sp.gov.br), por RDM, cria os pipelines de release para os ambientes (stages) de desenvolvimento e testes (QA), como o exemplo na [aplicação modelo Layout Padrão](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Layout_Padrao/_releaseDefinition?definitionId=9&_a=definition-tasks&environmentId=32).
    - Essa task exige a utilização de um usuário de banco de dados com role de db_owner, diferente do usuário de acesso ao banco, criado exclusivamente para essa finalidade. 
      > Sugestão de nome: deploy_user_[nome banco de dados]
1. Funcionando corretamente, através de [RDM para configuração de Deploy Automatizado](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Operações/Solicitações-de-infraestrutura/Requisição-de-Mudanças/RDM-para-configuração-de-Deploy-Automatizado), solicita-se à equipe DBA do COI que configure o pipeline de release para o ambiente de homologação usando o stage de desenvolvimento como referência. 
    - Esse stage deve ter configurado um "pre-approval" para a equipe DBA e um "post-approval" para a equipe de desenvolvimento, que verificará se a mudança atingiu seu objetivo.
1. A equipe de desenvolvimento configura um stage para a geração de [RDM para Deploy em HML](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Operações/Solicitações-de-infraestrutura/Requisição-de-Mudanças/RDM-Automatizada), para poder solicitar a execução do stage de HML.
1. Estando tudo em ordem, uma nova RDM deve ser criada para a configuração do stage de produção.
    - Esse stage também deve ter configurado um "pre-approval" para a equipe DBA e um "post-approval" para a equipe de desenvolvimento, que verificará se a mudança atingiu seu objetivo.

1. A equipe de desenvolvimento configura um stage para a geração de [RDM ara Deploy em PRD](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Operações/Solicitações-de-infraestrutura/Requisição-de-Mudanças/RDM-Automatizada), para poder solicitar a execução do stage de PRD.

**O disparo do stage de deploy de BD em HML e PRD só pode ser feito pela equipe DBA.**

##Execução do deploy
1. Quando houver alteração de código no repositório da aplicação, um novo conjunto de artefatos será criado e um deploy em desenvolvimento será feito.
1. Estando em ordem, uma RDM pode ser criada pela equipe do projeto, usando a task configurada no mesmo pipeline de release, que gera um link na tarefa da equipe de operações.
1. A equipe de operações:
   1. Abre task da RDM.
   1. Acessa o pipeline pelo link registrado na task.
   1. Verifica se o deploy em DEV foi bem sucedido.
   1. Caso negativo, registra na RDM que o deploy não foi realizado porque o stage de DEV não foi bem sucedido.
   1. Se estiver tudo em ordem, executa o deploy em homologação. 
   1. Havendo falha na execução, o analista de operações verifica se consegue identificar e resolver o problema em conjunto com a equipe de desenvolvimento.
   1. Finaliza a RDM.

1. Bem sucedido o deploy em homologação e verificada a boa utilização da aplicação em homologação, o responsável pelo projeto poderá executar o stage de geração de RDM para PRD, que será posteriormente realizado pela equipe de operações.

Mesmo que haja falha no deploy, a RDM será finalizada, com o registro dos problemas encontrados e das tentativas de correção.

Tanto um [roll back](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/ADS/Deploy-automatizado/Release-pipeline/Gerenciamento-de-releases) como um deploy corretivo deverá ser feito com outra RDM gerada em nova release ou por Redeploy em qualquer release.

---
#Responsabilidades primárias das equipes
Desenvolvimento:
- Desenvolver pipelines de build.
- Configurar a política de retenção de pipelines e testes.
- Gerar Builds.
- Criar pipeline de release e configurar o stage de DEV/QA para aplicações.
- Fazer deploy nos ambientes de DEV e QA.

DBA-CDS
- Criar pipeline de release e configurar o stage de DEV/QA para banco de dados SQL Server.

Operações:
- Configurar os pipelines de release para os demais ambientes, baseados nas definições do stage DEV ou QA.
- Administrar backups, quando necessário.
- Administrar a segurança (firewall, direitos de usuários etc).
- Especificar a infraestrutura.
- Administrar agent pool e deployment group.
- Fazer deploy nos ambientes de HML e PRD.

Arquitetura:
- Orientar as equipes de desenvolvimento e de operações nas criações de pipelines.
- Zelar pelo armazenamento dos códigos e pipelines.
- Arbitrar a comunicação entre as áreas envolvidas, quando solicitada.

>Essa wiki oferece informações suficientes para a construção de pipelines mas, havendo a necessidade, o CIA poderá auxiliar através de chamados ao [[Suporte_DevOps]](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Suporte_DevOps/_workitems/create/Product%20Backlog%20Item?templateId=f2327198-0479-4896-a596-9473509abab7&ownerId=edf1fe16-ac08-4f98-bbbc-e06e32d9686e) (área Desenvolvimento).

