Uma RDM de execução de deploy automatizado é uma requisição específica para utilização em pipelines, criadas por tasks.

Foram desenvolvidas três tasks de geração de RDM para utilização exclusiva em [pipelines de deploy automatizado](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/ADS/Deploy-automatizado):
- RDM Homologacao - Cria uma RDM para Deploy em Homologação no [Remedy de Produção.](https://sefaznetvirtual.intra.fazenda.sp.gov.br/arsys)
- RDM Producao - Cria uma RDM para Deploy em Produção no Remedy de Produção.
- RDM Homologação em ambiente de testes - Cria uma RDM para Homologação no [Remedy de Testes.](http://srv36132:8080/arsys/forms)

As duas primeiras geram tarefas para as equipes de operações fazerem o deploy, que só podem ser finalizadas ou canceladas por eles mesmos.
Na última, a RDM criada pode ser abandonada, pois se destina apenas a testar a criação de RDMs.

Veja um exemplo na aplicação [Layout_Padrao](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Layout_Padrao/_releaseDefinition?definitionId=3&_a=environments-editor-preview).

#Configuração da task
A task de criação de RDM deve ser incluída em um **stage separado** das tasks de deploy e com execução manual.
![RDM_HML_Trigger.png](/.attachments/RDM_HML_Trigger-762b63c5-f97b-4809-91fb-7aac37548856.png =500x)

Dentro do stage, o "Deployment process" deve ser configurado para execução com o agent pool:
- Release HML - para gerar uma RDM de deploy em homologação
- Release PRD - para deploy em produção

Abaixo do agent job, adiciona-se a task "RDM Homolocacao".
![RDM_Inclusao.png](/.attachments/RDM_Inclusao-f07e1618-2852-4849-87a5-45438fdb167c.png =450x)

Para a utilização dessa tarefa, é necessário informar, entre outros dados:
- [Codigo da Liberacao no Remedy](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Operações/Solicitações-de-infraestrutura/Requisição-de-Mudanças/RDM-para-configuração-de-Deploy-Automatizado) - Crie uma liberação no Remedy ou solicite a alguém responsável por sua criação. Esse código pode ser digitado diretamente nesse campo ou em uma variável no pipeline. Uma liberação é suficiente para todo o desenvolvimento e manutenção do projeto.
- Template da mudança - Em uma [RDM de Criação de deploy automatizado](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Operações/Solicitações-de-infraestrutura/Requisição-de-Mudanças/RDM-para-configuração-de-Deploy-Automatizado), utilizando-se do template, há uma tarefa de "Criação de template de mudanças". Após a criação, seu nome deve ser digitado nesse campo ou em uma variável do pipeline.
- Datas de inicio e finalização de execução - **Deixe em branco** para que a RDM seja executada na melhor oportunidade entre uma hora após sua criação e 24 horas depois. Só preencha as datas se for realmente necessário especificar um horário.

> Para o Remedy de testes, podem ser informados os mesmos valores usados na aplicação [Layout_Padrao](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Layout_Padrao/_releaseDefinition?definitionId=3&_a=environments-editor-preview)

#Criação da RDM 
Após a criação de uma release em DEV bem sucedida, de acordo com o planejamento da equipe do projeto, faz-se a criação da RDM selecionando-se a release e clicando sobre o botão "Deploy", que aparece na tela ao passar o ponteiro do mouse sobre a a task "RDM HML".
![RDM_HML.png](/.attachments/RDM_HML-b2bbd709-26b7-41fc-82e9-9359fefb366b.png =300x)

Após a criação da RDM, seu código pode ser verificado no log da execução.
![RDM_Codigo.png](/.attachments/RDM_Codigo-7f51c1e3-ac6b-44ed-b8fa-c7df635939a5.png =400x)
![RDM_Codigo2.png](/.attachments/RDM_Codigo2-1280fc77-ed97-4b98-bb13-1f0ca6dfb503.png =400x)

#Execução da RDM
Após a execução bem sucedida da task, uma RDM é criada no Remedy com uma tarefa para a equipe Windows Web, contendo um link para a release e com instruções para clicar em "Deploy" na task "Hml".
![Task_Remedy.png](/.attachments/Task_Remedy-bcf35933-6674-42ee-adeb-fdc0986b5455.png =500x)

O colaborador da equipe de operações designado para atender à mudança verificará as datas da tarefa. Estando em ordem, irá clicar no botão "Exibir" no painel com o link para o pipeline.

Ao se exibir a tela do pipeline, o operador deverá verificar se o stage anterior foi bem sucedido.
Também poderá analisar, a seu critério, as variáveis e o histórico do pipeline para verificar se houve alguma alteração desde o último deploy que possa afetar a execução.

Estando tudo em ordem, o analista irá passar o ponteiro do mouse sobre o stage a ser executado e clicará sobre o botão "Deploy".
![image.png](/.attachments/image-a69e7d02-4889-4bdb-9131-89728e18f468.png)



>Observação: 
Qualquer pessoa que tenha acesso à release consegue fazer o deploy em HML, porém, essa atribuição é da **equipe de operações**.
Se outra pessoa o fizer, essa informação ficará registrada no log do ads e o executor designado da equipe Windows deverá anotar na tarefa, em "Informações de trabalho", que a execução foi feita por outra pessoa.
A equipe de operações não se responsabiliza por execuções feitas por outras pessoas.

Se o deploy for bem sucedido ou não, nos dois casos, a RDM deverá ser finalizada. Havendo necessidade de novo deploy, cria-se uma nova release.




