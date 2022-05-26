Agent job é uma instalação de um aplicativo da Microsoft chamado de "VSTS-agent", "Azure Pipeline Agent" ou "AP Agent" em um ou mais servidores.
Essa aplicação pode ser obtida em um site do Azure:
https://vstsagentpackage.azureedge.net/agent/2.170.1/vsts-agent-win-x64-2.170.1.zip 

Ao ser descompactado em uma pasta de um servidor (p.ex. D:\Agents_vsts\Agent1), há um comando `config.cmd` que faz a instalação do agent como um Service em Windows ou Linux.

![image.png](/.attachments/image-3068fbbc-1003-4f31-89de-c2ed5f1303c5.png)

>Podem ser instalados vários agents job em um mesmo servidor, para finalidades distintas.

O agent job faz o papel de operador de um pipeline. Na sua instalação, entre outras informações, é solicitado um usuário existente no AD, tipicamente uma [conta de serviço](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Segurança-da-Informação/Usuários-em-aplicações), que será utilizado pela aplicação para a execução de tarefas.
>A configuração desse usuário deve ser definida **antes** da instalação do agent. Se o seu perfil for alterado posteriormente, o agent deverá ser removido e reinstalado.
Tipicamente, esse usuário deve ser incluído no grupo Administrator do servidor.

##Deployment group
Deployment Group é um tipo de agent usado apenas em pipelines de release para deploys ou tarefas de manutenção de servidores, como atualização de certificados ou de versões de aplicações, por exemplo. 

São criados e administrados pela equipe de operações. Para seu uso em deploys, já existe no webdesenv um deployGrp disponível. Solicite ao [Suporte DevOps](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Suporte_DevOps/_workitems/create/Product%20Backlog%20Item?templateId=f2327198-0479-4896-a596-9473509abab7&ownerId=edf1fe16-ac08-4f98-bbbc-e06e32d9686e) (área Desenvolvimento) o acesso, se necessário. 

Sempre que possível, o Deployment Group deve ser usado em deploys e em manutenções de servidores, por ser mais administrável, seguro e simples. 

>Os deployment groups existentes no ads podem ser consultados por administradores em:
`https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/_settings/deploymentpools` 
Selecionado um deles, exibem-se os seus servidores, aplicações que o utilizam, entre outras informações. 
Há um exemplo, o deployment group, denominado webhomolog, com dois servidores em balance:
`https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/_settings/deploymentpools?view=pool&poolid=28&tab=Targets` 
[Veja como criar deployment group...](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Operações/Agent-Job/Deployment-Group)

##Agent Pool
Usado em build, mas pode também ser usado em release, diversos agents job são instalados em um ou mais servidores (Windows ou Linux), formando um conjunto denominado "Agent Pool". 

Os agents pools são criados e administrados pela equipe de operações.  
Costuma-se dizer que um agent pool é “de build” ou “de release”. Essa denominação orienta sobre como os servidores estão configurados, quais aplicações estão instaladas e que acesso possui aos demais servidores da rede.
Na execução do pipeline, o primeiro agent job disponível no agent pool é usado. Se todos os agents estiverem ocupados, o pipeline aguarda em fila de execução.

Foi criado um agent pool para build e outros para deploy, com restrições de acesso conforme a função, com seis agent jobs em cada agent pool.
- Visual Studio - para build.
- Release DEV - acessa apenas servidores de desenvolvimento.
- Release HML - acessa apenas servidores de homologação.
- Release PRD - somente para deploy em produção.


O Agent "Visual Studio” não têm acesso aos servidores de aplicações. Nos dois servidores dessa pool estão instalados os recursos necessários para criação e testes de artefatos, como o Visual Studio, MSBuild, MSTest, node.js, entre outros. 

>A instalação de novos recursos nesses servidores devem ser solicitados ao CIA, por chamado no [Suporte DevOps](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Suporte_DevOps/_workitems/create/Product%20Backlog%20Item?templateId=f2327198-0479-4896-a596-9473509abab7&ownerId=edf1fe16-ac08-4f98-bbbc-e06e32d9686e) (área Infraestrutura).
[Veja como criar agent pool...](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Operações/Agent-Job/Instalação-de-Agent-Pool)
