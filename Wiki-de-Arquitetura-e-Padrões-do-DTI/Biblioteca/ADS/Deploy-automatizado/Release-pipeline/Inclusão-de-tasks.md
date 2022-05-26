Após a criação e configuração, segue-se a inclusão de tasks e de agents dentro de cada stage.
Para abrir um stage, há duas possibilidades:
- Clicar sobre a opção "Tasks" no Menu horizontal
![menu_tasks.png](/.attachments/menu_tasks-2e783521-b052-4c26-a980-5dd828146c33.png =250x)

- Clicar sobre o link dentro do retângulo que representa o stage ("[1 job, 1 task](#)", por exemplo)
![Link_tasks.png](/.attachments/Link_tasks-feebe998-d65c-4187-b61d-15c0a8e7a248.png =200x)

O painel de configuração do stage exibe diversas linhas que, em sua execução, são verificadas em sequência na ordem de cima para baixo.

#Deployment process
A primeira linha, intitulada com o nome do stage e com a descrição "Deployment Process", faz a iniciação do deploy com a preparação do ambiente para execução das tarefas.

O Deployment Process solicita três parâmetros:
![stage_parameters.png](/.attachments/stage_parameters-b3f5d228-3f68-438d-9238-cc7e8cf7f625.png =250x)

- Configuration type - deve **sempre** ser configurado com "IIS Web Application". 
  >A Sefaz não permite a configuração Web Site usando pipelines, pois isso pode prejudicar todas as demais aplicações que se utilizam dos mesmos servidores.
- Website name - nome dado ao website configurado no IIS do servidor destino. Em geral, HTTP recebe o nome "Commom" e HTTPS costuma ser chamado de "Secure". 
- Virtual path - nome da aplicação no IIS, precedida por uma barra inversa. Ao digitar no navegador o endereço do servidor web seguido do virtual path, estando tudo mais em ordem, a aplicação é executada.

No título desses três campos aparece um ícone com um desenho que sugere um corrente com dois aros. Esse ícone indica que há campos em tarefas dentro desse stage que têm o mesmo nome e, portanto, receberão seu mesmo conteúdo.
O título "Parameters" exibe à sua direita um ícone para "quebrar" esses links.
>Veja na [Aplicação Modelo](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_release?_a=releases) um exemplo de configuração de Deploy IIS de Web App. 


##Agent job
Nessa primeira linha, há um botão com o desenho de reticências que permite a inclusão de **[agentes de execução](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Operações/Agent-Job).
![Add_Agent.png](/.attachments/Add_Agent-780896e1-9e42-4159-ae63-fd4d62865f9a.png =300x)**

Se já não houver algum, clique em "Add a Deployment group job" e depois exclua a linha "Agent Job" para a maioria dos deploys em servidores Windows IIS.

>Consulte o [Suporte_DevOps](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Suporte_DevOps/_workitems/create/Product%20Backlog%20Item?templateId=f2327198-0479-4896-a596-9473509abab7&ownerId=edf1fe16-ac08-4f98-bbbc-e06e32d9686e), área Infraestrutura, para informações sobre os nomes e caminhos dos servidores web ou para conhecer agentes disponíveis.

#Run on - IIS Deployment/Agent job
Na segunda linha do stage, há a linha que especifica o [agent job](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/8/Agent-Job) que fará o deploy.

A primeira ação do agent, por padrão, é o "Download Artifacts", que faz carregamento dos arquivos gerados na build, se houver, para uma pasta no local ([System.DefaultWorkingDirectory](https://docs.microsoft.com/en-us/azure/devops/pipelines/release/variables?view=azure-devops&tabs=batch#default-variables---system)) onde está instalado o agent job.

Ao clicar na linha do agent job, no painel direito se exibem: 
- [Deployment group ou Agent Pool](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Operações/Agent-Job) - procure na lista e selecione o agent desejado para a execução das tasks. Clique no ícone de engrenagem para verificar quais agentes estão disponíveis para seu projeto.
- Targets to deploy to in parallel - utilizando um Deployment Group com diversos servidores configurados, selecione "Multiple" para que todos os servidores executem as tarefas ao mesmo tempo. Se houver problema, todos os servidores serão afetados.
Selecione "One target at a time" para que se execute um servidor por vez. Assim, havendo falha no primeiro, os demais não são afetados.
- Skip download of artifacts - não precisando dos artefatos na execução das tarefas, seu carregamento pode ser desligado, tornando a execução mais rápida.

#Tasks
Sob cada agente, adicionam-se as tarefas a serem executadas clicando-se no sinal de adição à direita dessa linha.
![stage_add_task.png](/.attachments/stage_add_task-e0a41d1f-8e82-48d1-bbfc-75b45ae9256c.png =450x)
Um pipeline de release pode fazer praticamente qualquer atividade que uma pessoa consegue realizar por linhas de comando. Assim, não dá para descrever todas as possibilidades de inclusão e configuração de tasks.
Ao selecionar um template na criação do stage, algumas tasks são inseridas automaticamente.

Nos deploys mais comuns, o stage pode conter, entre outras, tasks para:
- configurar o servidor de aplicação.
- transformações nos artefatos para implantação em cada ambiente de destino.
- cópia dos artefatos para os servidores de aplicação.
- verificar os arquivos de configuração da aplicação.

##Deploy de aplicação web com IIS
Nesse template, são adicionas as tasks de configuração do site e de deploy da aplicação.
Para deploy em servidores em balance, podem ser adicionadas tasks para retirar e reativar um servidor durante o deploy, evitando-se indisponibilidade.

###Stop node/Big-IP Stop Node
Retira o servidor do balance.

###IIS web app manage
Configura o servidor IIS. Essa tarefa pode também ser feita manualmente, mas a tarefa assegura que o servidor estará devidamente ajustado ou indicará erro, evitando um deploy quebrado. 
Campos obrigatórios:
- Physical path - identificação da pasta local do servidor web. Na Sefaz, costuma ficar em:
`D:\inetpub\wwwroot\[nome do webapp] para http`
`D:\inetpub\secure\[nome do webapp] para https`

- Create or update app pool - cria uma app pool no IIS, se não existir, ou atualiza, se houver alguma alteração. 
O DTI recomenda que cada aplicação tenha um app pool.
    - Name - Preferencialmente o nome da aplicação
    - .NET version - Para aplicações em dotnet core, selecione `No managed code`.
    - Managed pipeline mode - `Integrated`
    - Identity - `Custom account` para aplicações que acessam banco de dados SQL Server com Windows Authentication.
    - Username - [Conta de serviço](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Segurança-da-Informação/Usuários-em-aplicações) para acesso a banco de dados.
    - Password - Senha da conta de serviço, se não for do tipo gMSA.

- Advanced: Additional appcmd.exe commands - Quando há algum erro na configuração da app pool, ela para, dando erro 503. Para reiniciá-la, após a correção das variáveis, sem precisar de uma intervenção manual diretamente no servidor destino, acrescenta-se o comando:
`start apppool /apppool.name:[Nome_Aplicacao]`

> Ponto de atenção: banco de dados Oracle
>- No servidor de aplicação há clients do Oracle Instalados em versões 32 e 64 bits
>    - A versão 32 bits está em Português
>   - A versão 64 bits está sem especificação de configurações de idioma, usando a do servidor (US)
>- Na Application Pool do IIS é possível indicar qual versão se deseja utilizar. O campo "Enable 32 bits application" é criado com valor "false" por padrão.

###IIS web app deploy
Essa task faz o deploy, propriamente, copiando os artefatos para seu destino de acordo com os campos informados no painel.
- Website Name - é a mesma informação do campo de mesmo nome no painel do Deployment Process.
- Virtual Application – nome da aplicação
- Package or Folder – é o local onde estão os artefatos para o deploy. Clique no botão de reticências para localizar o arquivo zip de instalação.
- File Transforms & Variable Substitution Options - faz a transformação de arquivos de configuração para adequação ao ambiente do deploy, **[substituindo chaves ou valores](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/ADS/Deploy-automatizado/Release-pipeline/Definição-de-variáveis-de-release)** desses arquivos por variáveis definidas no pipeline.
 
###Start node/Big-IP Start Node
Adiciona o servidor ao balance.

##Criação automatizada de RDM 
Um stage de criação automatizada de RDM usa um template vazio (Empty Job).
Recomenda-se que esse stage fique entre os stages de deploys. A execução deve ser configurada para "Manual only".

Na configuração do Agent Pool, utilize:
- Release HML - para criação de RDM no Remedy de testes.
- Release PRD - Para criação de RDM de HML ou PRD no Remedy de produção.

Sob esse agent, coloque apenas a [task de criação de RDM](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Operações/Solicitações-de-infraestrutura/Requisição-de-Mudanças/RDM-Automatizada).


