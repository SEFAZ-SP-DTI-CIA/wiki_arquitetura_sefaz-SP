Um Deployment Pool ou Deployment Group é um recurso criado no ads e um serviço instalado em cada servidor (target) onde serão realizados os deploys da aplicação.

O Deployment Pool é o que existe de fato na instância (ads ou adshml), enquanto que um Deployment Group é uma camada sobre o pool que oferece a sua disponibilidade aos pipelines de **release** um projeto.
Podemos pensar que o Group é um compartilhamento do Pool para um projeto. Na maior parte do tempo, nos referimos a eles como se fossem sinônimos.

[[_TOC_]]

#Criação do Deployment Group/Pool
Se já não existir um deployment group configurado no servidor destinatário do deploy, ele pode ser criado a partir do menu "Pipelines-Deployment groups" ou na tela "Agent pools" configuração da collection.

Ao clicar em "+ New" (ou "Add pool"):
- Deployment group name: prefira usar o nome do servidor ou balance
- Description: adicione informações que auxiliem os usuários a entenderem a configuração desse deployment group.

#Instalação do agent nos targets
Um agent de um deployment group é um serviço instalado em um ou mais servidores (targets). 
O instalador pode ser baixado pelo link contido no script gerado na criação do deployment group, ou então a versão mais atual pode ser obtida no GitHub - https://github.com/Microsoft/azure-pipelines-agent/releases

Em cada servidor (target) desse deployment group, extrai-se o conteúdo do arquivo zip baixado em uma pasta com nome Agent na unidade D:\, que é padrão na Sefaz. Se já houver outro agent configurado com esse mesmo nome de pasta, altera-se o nome da pasta (Agent1, por exemplo).
Executa-se o comando “.\config.cmd” usando o PowerShell em cada servidor, na pasta criada (D:\Agent), como administrador, por exemplo:
```
.\config.cmd --deploymentgroup --deploymentgroupname "[nome_deployment]" --agent $env:COMPUTERNAME --runasservice --work '_work' --url 'https://ads.intra.fazenda.sp.gov.br/tfs/' --collectionname 'PROJETOS' --projectname 'deploy'; 
```

Em “[nome_deployment]” digita-se o nome do DeployGrp.
Na autenticação, digita-se “integrated”.
  
```
>> Connect:
     Enter authentication type (press enter for PAT) > integrated
```
Se ficar travado nesse ponto, pode ser problema de firewall. Nesse caso, solicite a abertura da porta 443 do ads para o servidor do deploy.
Se der certo, deve aparecer:
```
>> Register Agent:
Scanning for tool capabilities.
Connecting to the server.
Enter deployment group tags for agent? (Y/N) (press enter for N) >
Successfully added the agent
Testing agent connection.
2019-11-05 21:06:32Z: Settings Saved.
```

##Compartilhamento com os projetos
Os deployment groups do ads podem ser consultados em:
https://ads.intra.fazenda.sp.gov.br/tfs/PRODUTOS/_settings/deploymentpools 

Para fique disponível, seleciona-se o deployment group. Em seguida, na página "Details", seleciona-se o projeto que irá usá-lo em pipelines de deploy nos servidores configurados.

> Há um bug no ads que impede a atribuição do Deployment Group a um projeto.
Excluindo o grupo ads "Deployment Group Administrators" do projeto, a inclusão funciona e o grupo é recriado.

O compartilhamento pode ser solicitado por chamado ao [[Suporte_DevOps]](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Suporte_DevOps/_workitems/create/Product%20Backlog%20Item?templateId=f2327198-0479-4896-a596-9473509abab7&ownerId=edf1fe16-ac08-4f98-bbbc-e06e32d9686e) (área "Infraestrutura").
