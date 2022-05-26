Um pipeline de build tem duas formas de execução:
- Integração contínua
- Manual

Em uma estratégia de integração contínua, configura-se um pipeline para que seja disparada uma nova build quando houver alteração no repositório. 
Para ativar esse recurso, no menu horizontal do painel de edição do pipeline de build, há uma opção «Triggers». No painel de triggers há uma caixa de verificação para ativação da integração contínua. Estando habilitada, toda vez que o repositório de código sofrer uma atualização, uma nova build será criada automaticamente.
![Integracao_continua.png](/.attachments/Integracao_continua-06ea9d89-f192-4ffa-9c0c-692c24d77ecf.png =600x)

Uma build pode ser criada também manualmente. Seleciona-se a build definition desejada, clica-se no botão  «Queue», na tela de edição do pipeline, ou em «Run pipeline», no canto superior direito da tela de exibição de builds do pipeline:
![Run_pipeline.png](/.attachments/Run_pipeline-2e03a463-7c80-4693-8a31-04accfb1c2da.png =500x)
E novamente em «Run»:
![run.png](/.attachments/run-961963af-c7e6-474b-9d23-8f58dafb3e50.png =400x)

Para visualizar o andamento da build, clique na linha "Agent Job" na tela que aparecerá após sua criação ou na linha. A tela console de build será exibida com o andamento do processo, na sequência configurada na build definition.
![Run_job.png](/.attachments/Run_job-258d06ce-8731-40ec-8568-676bc6a33f22.png =200x)

Se o código estiver com algum erro, a build vai falhar (quebrar).
Para investigar as falhas, indicadas pela bolinha vermelha, clique sobre a build e navegue pelas informações da tela.
![Build_quebrada.png](/.attachments/Build_quebrada-a30dc723-440c-4019-8c57-394e2f9630fb.png =500x)

#Verificar os artefatos
Para verificar os arquivos gerados na build:
![image.png](/.attachments/image-b1937d7b-5064-4d7c-8c94-f69e7bfa8603.png)

>**Erro no Nuget** 
Se aparecer um erro contendo o texto:
"Response status code does not indicate success: 403 (Forbidden..."
Acesse Artifacts - Feed Settings - Permissions - ... (More commands) e clique sobre "Allow project-scoped builds", se já não estiver configurado.
 