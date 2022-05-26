Um agent pool é um grupo de agents job instalados em um ou mais servidores.
Ao se utilizar um agent pool em um pipeline, o ads tomará um agent job disponível. Se estiverem todos ocupados, o job entrará em fila de execução em algum dos agents.
Assim, para um agent pool com muita utilização, como em pipelines de build, recomenda-se que se adicionem diversos agents job, preferencialmente em mais de um servidor. O agent pool "Visual Studio" no ads, por exemplo, foi configurado em dois servidores com três agentes em cada um.

> A criação e manutenção de agentes no ads é de responsabilidade exclusiva da equipe Windows Web.
 Quaisquer solicitações de alteração ou criação devem ser feitas através de chamado no [[Suporte_DevOps]](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Suporte_DevOps/_workitems/create/Product%20Backlog%20Item?templateId=f2327198-0479-4896-a596-9473509abab7&ownerId=edf1fe16-ac08-4f98-bbbc-e06e32d9686e) (área "Infraestrutura").

#Criação do agent pool
Os agent pools são gerenciados apenas por usuários cadastrados como  "Project Collection Administrators" através da página:
https://adshml.intra.fazenda.sp.gov.br/tfs/PRODUTOS/_settings/agentpools
Para administradores, um botão "Add Pool" permite a criação.
![image.png](/.attachments/image-32abbdf0-4f39-483c-b58d-fccdb07f6a4b.png =400x)

>A pool criada de agentes ficará disponível a todos os projetos do ads, não só da collection.

##Azure Pipeline Agent
Os agents job são administrados na página do ads de um agent pool, por exemplo:
https://adshml.intra.fazenda.sp.gov.br/tfs/PRODUTOS/_settings/agentpools?poolId=45&view=agents

Para um administrador, nessa página, exibe-se um botão para a criação de um agent job. Porém, o agent não é criado no ads e sim nos servidores. 
Ao clicar no botão, exibem-se apenas as instruções de configuração do agente, incluindo um link para obtenção do _AP Agent_ (VSTS agent), a aplicação que será instalada nos servidores dos agent pools para cada agent job.

![image.png](/.attachments/image-b4872d53-f3a9-4111-ae0a-793ecce16e5d.png =500x)

O arquivo zip obtido nesse link, contendo os arquivos de configuração de _AP agents_, pode ser extraído em uma pasta compartilhada, para que se façam diversas instalações de agents job.
Dentro dessa pasta, há os scripts ".\config.cmd" para instalação e ".\run.cmd" para execução do agent job criado.

##Criação de agent job 
Para instalar os agents job, recomenda-se logar nos servidores com a conta de serviço tfs_setup.
Em cada servidor e para cada agent job a ser criado, copia-se o conteúdo da pasta compartilhada com o _AP Agent_ para uma pasta local com um nome esclarecedor, por exemplo:
`D:\AgentPool\AgentJob-01`
Através do PowerShell, chamado nessa pasta como administrador, executa-se o script de instalação do agent job:
```
PS D:\AgentPool\AgentJob-01> .\config.cmd
>> Connect:

Enter server URL > https://adshml.intra.fazenda.sp.gov.br/tfs
Enter authentication type (press enter for PAT) > Integerated
Connecting to server...
 
>> Register agent:
Enter agent pool > Visual Studio
Enter agent name > SRV11728-Agent01-Build
...

Enter run agent as service? (Y/N) > Y
Enter User account  to use for the server > intra\cs_tfs_rm_agent
Enter password for the account intra\cs_tfs_rm_agent > *********
Granting file permitions to 'intra\cs_tfs_rm_agent'.
...
Service ... started successfully
```
Para agents jobs destinados a fazer release (deploy), usam-se contas distintas para cada ambiente:
- intra\cs_tfs_rm_agent_dev para servidores utilizados em testes.
- intra\cs_tfs_rm_agent_hml para servidores destinados a homologação de sistemas.
- intra\cs_tfs_rm_agent para servidores destinados a implantação de sistemas em produção

Na criação de agent job para build, utiliza-se o usuário:
- intra\tfs_build

Estando tudo em ordem, no ads, o agent job deverá aparecer online no agent pool:
https://adshml.intra.fazenda.sp.gov.br/tfs/PRODUTOS/_settings/agentpools?poolId=45&view=agents

>Para mais detalhes nessa operação, consulte o texto "Criacao de Agent pool.docx" junto à equipe Windows Web.




