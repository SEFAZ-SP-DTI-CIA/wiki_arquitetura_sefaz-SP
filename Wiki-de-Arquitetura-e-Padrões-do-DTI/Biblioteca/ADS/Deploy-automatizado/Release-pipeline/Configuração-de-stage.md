Stages são conjuntos de agents e tasks ligados a um mesmo artefato. 

Cada stage é um deploy com configurações próprias. 
![Stages.png](/.attachments/Stages-7288117a-9f20-49f5-9b23-f74f857fc1bb.png =600x)

Tipicamente, stages diferentes são usados para fazer o deploy do mesmo conjunto de artefatos em servidores diferentes, com configurações distintas. 
#Criação de stage
O primeiro stage é adicionado logo na criação do pipeline.
Para incluir um novo stage dentro do pipeline, há dois caminhos:
- New stage - cria um stage sem tasks.
- Clone stage - duplica o stage selecionado, incluindo as tasks e as variáveis do escopo desse stage. 
![New_stage.png](/.attachments/New_stage-30193861-2bcb-44a7-9d70-7edf41a8eba7.png =200x)

Sugere-se que o nome do stage indique o ambiente de execução (Des, QA, Hml, Prd).
>Por costume, um nome somente com o ambiente indica que se trata de deploy. Para outros tipos de stages, o nome começa com o objeto seguido do ambiente (p. exe. RDM Hml).

A Sefaz costuma criar três stages (DEV ou QA, HML e PRD) com início automático apenas no stage de desenvolvimento ou testes (QA), além de dois stages para a criação automatizada de RDM para deploy do próximo stage (RDM Hml e RDM Prd).

Por determinação da Sefaz, o deploy automatizado em homologação só pode ser executado em pipelines com stage de DEV/QA precedendo o stage de HML, com deploy em desenvolvimento bem sucedido na mesma release. 
O deploy em produção exige que haja o stage em HML com execução bem sucedida na mesma release.

#Configuração
Um stage possui três painéis de configuração, exibidos à direita da janela ao clicar sobre o stage ou sobre um de seus ícones.
![Stage_config.png](/.attachments/Stage_config-229f2d9c-717e-4bb3-90e1-edccc366ab71.png =300x)

##Identificação
Ao clicar sobre o meio do stage, o painel lateral direito oferece um campo para atribuir um nome a ele.

##Pre-deployment conditions
Clicando sobre o ícone à esquerda do stage, o painel direito permite a configuração da forma de execução das tarefas desse stage.

###Trigger
A configuração do trigger (disparo) indica ao pipeline como será iniciado o stage:
- After release - O stage é executado na criação da release.
- After stage - O stage é executado na após a execução de um outro stage indicado.
- Manual only - O stage é executado após a criação da release por ação humana.

Havendo vários stages, há diversas técnicas de execução do pipeline:
- Início automático - Técnica mais recomendada, dentro de  uma estratégia de Entrega Contínua (CD), o primeiro stage é executado na criação da release, os demais stages são acionados manualmente.
![Inicio_automatico.png](/.attachments/Inicio_automatico-5b73be63-6ffd-429d-b480-f58d8d60a1fa.png =200x)

- Sequência - O primeiro stage é executado na criação da release, os demais stages aguardam a execução com sucesso do anterior para começar. Pode ser alternativa à técnica de início automático se houver condições de pré ou pós aprovação que impeçam o deploy posterior a um deploy em que se observaram falhas na execução da aplicação (teste de fumaça, por exemplo).
![Em_sequencia.png](/.attachments/Em_sequencia-fc37e193-1a07-4038-86dc-da20beee3e3f.png =500x)

- Paralela - Com alguma utilidade para testes ou manutenção de grupos de servidores, por exemplo, todos os stages são executados na criação da release. A falha em um stage não impede a execução no outro. Não é uma técnica apropriada para deploys.
![Paralelamente.png](/.attachments/Paralelamente-29b0cacf-2bcc-4b7c-933d-06ecc7fa272a.png =350x)

- Manual - Recomendável para pipelines que atingem apenas ambientes de QA ([Quality Assurance](https://pt.wikipedia.org/wiki/Garantia_da_qualidade)), para evitar interrupções dos testes, após a criação da release, nenhum stage é executado. O pipeline espera a execução manual de cada stage.
![Manualmente.png](/.attachments/Manualmente-8c3a3aec-fa36-4ae9-acb4-34e1e200eebb.png =340x)

###Pre-deployment approvals
Esse painel permite a especificação de usuários que poderão verificar se o stage está em ordem **ANTES** de ser iniciado.
Um email é envidado aos usuários relacionados solicitando sua aprovação antes de executar o stage. Se não aprovado, o deploy é finalizado.
**Por determinação do DTI, todos os stages de deploy automatizado nos ambientes de homologação e produção devem ser configurados com pre-deployment approvals da equipe dtiwinweb.**

##Post-deployment conditions
Clicando sobre o ícone à direita do stage, o painel direito permite a configuração de ações posteriores à execução desse stage.

###Post-deployment approvals
Esse painel permite a especificação de usuários que poderão verificar se a execução bem sucedida do stage foi satisfatória.
Um email é envidado aos usuários relacionados solicitando sua aprovação **APÓS** a execução do stage. Se não aprovado, os stages configurados para execução após esse não serão iniciados.

