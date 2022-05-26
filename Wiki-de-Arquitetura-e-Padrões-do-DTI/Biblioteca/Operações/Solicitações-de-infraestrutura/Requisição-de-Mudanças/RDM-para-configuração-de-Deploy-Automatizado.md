Um [pipeline de deploy automatizado](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/ADS/Deploy-automatizado) é uma sequência de tarefas que toma os artefatos de uma aplicação gerados em um [pipeline de build](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/ADS/Deploy-automatizado/Build-pipeline), faz eventuais transformações de links ou chaves, cria ou atualiza uma aplicação ou serviço Web ou um serviço Windows e copia os artefatos para seu local de execução. 

Os procedimentos para a automação de um deploy de uma aplicação no ADS consistem, entre outras atividades, a criação do pipeline pela equipe de desenvolvimento, o levantamento das informações de infraestrutura necessárias à configuração desse pipeline e ao preenchimento da requisição de mudanças, a concordância das equipes envolvidas, a implantação dos recursos e a primeira execução do pipeline em cada ambiente.

[[_TOC_]]

#Criação do pipeline
Em uma estratégia de [promoção de binários](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Arquitetura/Maturidade-de-Sistemas/Nível-1/Políticas-de-branching), com três ambientes de execução da aplicação, cabe à **equipe de desenvolvimento** a [criação do pipeline de release](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/ADS/Deploy-automatizado/Release-pipeline/Criar-release-pipeline) e configuração do primeiro _stage_ para deploy em um ambiente de testes (QA) ou desenvolvimento.

Depois de criado o pipeline e com o stage de desenvolvimento ou testes (QA) funcionando corretamente, 
a equipe de desenvolvimento adiciona dois novos stages com execução manual em homologação:
- RDM HML - um stage que será configurado para a [criação de RDM de Execução do Pipeline](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Operações/Solicitações-de-infraestrutura/Requisição-de-Mudanças/RDM-Automatizada) em homologação
- HML - um clone do stage de DEV/QA que será configurado pela equipe de operações para o deploy automatizado em homologação

Para deploy automatizado em produção:
- RDM PRD - um stage que será configurado para a criação de RDM de Execução do Pipeline em produção
- PRD - um clone do stage de HML que será configurado pela equipe de operações para o deploy automatizado em produção

As variáveis do pipeline devem ser preenchidas, sempre que possível, pela equipe de:
   - desenvolvimento - variáveis de substituição nos arquivos da aplicação (web.config ou appsettings.json) **ANTES** da execução da RDM
   - operações - variáveis para a execução das tasks de deploy, como contas de serviço, senhas, nome e caminho físico do site no IIS etc. ANTES da primeira execução do pipeline

#Levantamento de informações
Para auxiliar no registro das informações necessárias à configuração do deploy automatizado, recomenda-se que se **[crie um chamado no Suporte Devops, área de infraestrutura](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Suporte_DevOps/_workitems/create/Product%20Backlog%20Item?templateId=b2962b9d-3972-4dfe-8e3d-e8e640651cc1&ownerId=bb749b66-483b-420c-b573-f47542c36b8f)** e se compartilhe o link do chamado com as equipes de operações.
Além de centralizar o registro das informações, ainda se podem deixar comentários úteis ao processo.
A cada alteração no chamado, um email será enviado à equipe windowsweb e ao designado.

Antes de criar a RDM, as equipes de desenvolvimento em conjunto com as equipes de operações, através de emails, deverão buscar as informações:

1. Identificação do objeto de deploy
   - Link da aplicação
   - Camada (webapp, serviço, api, interno ou externo)
   - Tipo de acesso (interno, externo)
   - Ambiente
   - Balance - Tipo, nome e apelido do balance - se o balance for exclusivo, pode ser o nome do projeto ou da aplicação. Se for compartilhado, algum nome que seja bem compreendido pela equipe de operações, por exemplo, parte do link (webdesenv, webservices5 etc).
   - Servidores - Relação dos servidores onde será hospedada a aplicação.
     > Os servidores e balance devem estar registrados na topologia, no desenho de solução.
     > Consulte a [relação de servidores compartilhados para .Net](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Recursos/Servidores-compartilhados-para-aplicações-.Net), se necessário.

    
```
     Exemplo 1: aplicação web para acesso externo
       - Aplicação: https://www5.fazenda.sp.gov.br/aplicacao/
       - Camada: WebApp 
       - Acesso: Externo
       - Ambiente: Produção
       - Balance: BigIP - vcmpsp-dmzctb/vcmp2sp-dmzctb - www5
       - Servidores:
         - SRV11800 -IP: 10.216.66.nn0
         - SRV11801 -IP: 10.216.66.nn1
         - SRV11802 -IP: 10.216.66.nn2
         - SRV11803 -IP: 10.216.66.nn3
 
     Exemplo 2: aplicação web para acesso interno
       - Aplicação: https://sefaznet5.intra.fazenda.sp.gov.br/aplicacao
       - Camada: WebApp 
       - Acesso: Interno
       - Ambiente: Produção
       - Balance: BigIP - vcmpsp-appcorp/vcmp2sp-appcorp - sefaznet5
       - Servidores:
         - SRV11786 – IP: 10.216.38.nn8
         - SRV11787– IP: 10.216.38.nn9
 
     Exemplo 3: web api para acesso interno
       - Aplicação: https://webservices5.intra.fazenda.sp.gov.br/webapi/api/servico
       - Camada: WebAPI
       - Acesso: Interno
       - Ambiente: Produção
       - Balance: BigIP - vcmpsp-appcorp - webservices5
       - Servidores:
         - SRV11792 – IP: 10.216.32.n1
         - SRV11793 – IP: 10.216.32.n2
 ```

2. Deployment pool a ser utilizado.
   - Deployment Group: 
      - Se existir - nome do agent 
      - Caso contrário - "Novo deployment group - [Apelido do Balance]"
        - Se for exclusivo para o projeto
Criar o Deployment Group no projeto ADS
        - Se puder ser compartilhado entre mais projetos
Criar um Deployment Pool na Collection do ADS
Compartilhar com o projeto

    >Observações:
Um Deployment Group deve ser configurado em todos os servidores do Balance. 
Em caso de necessidade de uso apartado de servidores no mesmo Deployment Group, deverão ser utilizadas "tags" para identificar cada servidor.

3. Link do Pipeline de Release da aplicação com o Stage para o ambiente (HML ou PRD), já clonado do Stage anterior (DEV ou HML).

4. Regras de firewall entre os servidores da aplicação (origem) e o ADS e o Balance (destino) que deverão ser solicitadas na RDM.
    - ADS: 
Servidores de origem: [relação de servidores]
Destino: https://ads.intra.fazenda.sp.gov.br/ - porta 443
    - Big-IP
Servidores de origem: [relação de servidores]
Destino: [endereço do balance (BigIP ou NLB)] - porta 443

#RDM
A partir disso, as configurações dos stages de HML e PRD devem ser solicitadas por RDM no Remedy, com a utilização do template "Criação do Deploy Automatizado HML" ou "Criação do Deploy Automatizado PRD".

![TemplateRDM.jpg](/.attachments/TemplateRDM-461a09f4-f0e6-4a85-b5e8-be70683b8a82.jpg =600x)



#Concordância
Para essa RDM consulte, por email, a equipe de operações, com antecedência, para obter informações para esse preenchimento e a concordância formal delas para a execução.
- Windows Web - dtiwinweb@fazenda.sp.gov.br - para verificação dos servidores, balance, deployment groups e firewall.
  >Sugestão de mensagem:
"Prezados colegas, solicitamos orientações e concordância sobre o deploy automatizado previsto para o projeto [nome do projeto] em ambiente de homologação.
A aplicação é acessível pelo endereço: [link para a aplicação]
Já está desenvolvido o stage de desenvolvimento no pipeline: [link para o pipeline de deploy automatizado]."

Anexe o email de concordância à RDM.

#Liberação
A RDM a ser criada exigirá uma Liberação no Remedy, que poderá ser utilizada também para as RDMs de execução dos deploys.

Para criar uma liberação, acesse o [BMC Remedy](https://sefaznetvirtual.intra.fazenda.sp.gov.br/arsys/forms/arsapp/SHR:LandingConsole), no menu lateral à esquerda (Aplicações), selecione "Gerenciamento de Liberação" e "Nova Liberação"
>Após a sua criação, recomenda-se que o conteúdo do campo "ID da Liberação" seja anotado para ser informado posteriormente no [stage de criação de RDM](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Operações/Solicitações-de-infraestrutura/Requisição-de-Mudanças/RDM-Automatizada) no pipeline de deploy e também nesta RDM de configuração do pipeline.

Preencha a liberação com informações de identificação do projeto e seu responsável, com algumas sugestões:
- Deixe em branco o campo "Template"
- Sumário: "Deploy automatizado"
- Notas: "Configuração e execução de pipeline para deploy automatizado"
- Justificativa de negócio: "Estratégia Corporativa"
- Tipo de liberação: "Evolutiva"

Avance para o próximo estágio até chegar em "Implantação".
![Liberacao_Implantacao.png](/.attachments/Liberacao_Implantacao-6d6af060-ffc8-424d-9e84-f91b99cd2d49.png =600x)

A liberação está pronta para ser utilizada.

#RDM
Para criar uma RDM, acesse o [BMC Remedy](https://sefaznetvirtual.intra.fazenda.sp.gov.br/arsys/forms/arsapp/SHR:LandingConsole).
Há duas formas de criar a RDM:
- no menu lateral à esquerda (Aplicações), selecione "Gerenciamento de Mudança" e "Nova Mudança"
- na liberação criada, selecione no menu "Criar Solicitação Rel.", no lado esquerdo da página, a opção "Mudança de Infraestrutura":
![Liberacao_Criar_RDM.png](/.attachments/Liberacao_Criar_RDM-0fc0807a-32b9-41b6-bdd8-db012ac06db3.png =600x)

##Identificação
Preencha a RDM com informações de identificação do projeto e seu responsável, com algumas recomendações:
- Grupo do Coordenador: nome do projeto no Remedy
- Coordenador de Mudança: pessoa disponível na lista exibida que responde pelas informações e preenchimento da mudança.
- Serviço: procure o nome do projeto ou da aplicação
- Template: "Criação do Deploy Automatizado HML" (ou PRD)
- Sumário: "Criação do Deploy Automatizado HML"
- Notas: 
"Configuração de pipeline para deploy automatizado
Endereço do pipeline: [colar o link para o pipeline]
Para deploy no servidor: [indicar o nome do servidor destino do deploy]
Acompanhamento: [indicar uma pessoa que poderá esclarecer eventuais dúvidas]"
- Classe: **Normal**
- Motivo da mudança: Nova Funcionalidade
- Gera indisponibilidade: Não
- Acompanhamento: Sim
- Ambiente: "Homologação" ou "Produção"
- Grupo do gerente: nome do projeto no Remedy
- Gerente de Mudança: pessoa disponível na lista exibida que possa autorizar a mudança.

##Informações de trabalho
Algumas informações adicionais são exigidas pelo Remedy, ainda que não sejam necessárias aos procedimentos de configuração de deploy automatizado:
- Localização na BMD 
  - Pasta na BMD: "Azure Pipelines"
  - Endereço do link: link do pipeline de BUILD
- Aceite de Teste (HML) ou de Homologação (PRD) - adicione um texto ou email contendo a autorização do responsável pelo projeto para a utilização de pipelines para deploy automatizado.

![image.png](/.attachments/image-acb46194-85e1-47d1-9dd0-4bab5538557b.png =600x)

Salve a RDM, antes de continuar o preenchimento.

Se a RDM foi criada a partir da liberação, o Remedy retornará para ela.
Nesse caso, clique na página "Relacionamentos" da liberação e observe que já há uma ligação para a RDM, ainda em rascunho.
![Liberacao_Relacionamentos.png](/.attachments/Liberacao_Relacionamentos-569fd3b5-b983-4955-ac40-600c14e536c1.png =500x)
Com um duplo-clique do mouse sobre a mudança, a RDM se abre novamente.

##Análise de risco
Como haverá, necessariamente, a execução do pipeline, o que consiste em um verdadeiro deploy, há um risco inerente a toda execução de promoção de aplicações:
- variáveis de substituição incorretas
- identificação equivocada do balance ou dos servidores
- configuração indevida das tasks do pipeline

Isso poderá causar indisponibilidade do serviço, impactando o trabalho de seus usuários.
Assim, esse risco deverá ser descrito para a comissão de mudanças, espera-se que se faça a devida comunicação para seus usuários e que haja um plano de retorno, caso não se consiga completar o primeiro deploy automatizado.

Para preencher a análise de risco, clique no botão com um ícone de uma balança.
![Analise_de_Risco_botao.png](/.attachments/Analise_de_Risco_botao-326c4fe0-ab5c-4cb2-83fe-ebb69ca0d0a0.png =200x)
Preencha os campos em branco e salve.
![image.png](/.attachments/image-50ed5db8-fc51-4e84-863a-a708227fe09b.png)

##Tarefas
As tarefas exigem o preenchimento do campo "Notas" com as informações levantadas ou a RDM será recusada na reunião de mudanças.

### Criação do template de execução
Para poderem criar o template que será utilizado na automação do deploy, na primeira task do pipeline, a equipe de mudanças precisa saber:
- tipo do template - "criação automatizada de RDM em pipelines de deploy"
- nome do projeto ou da aplicação
- ambiente de execução do serviço

Essas informações devem ser registradas no campo "Notas":
"Criar um template para o projeto [nome do projeto] de criação automatizada de RDM em pipelines de deploy em ambiente de [homologação/produção]."

### Abertura de firewall
A segunda task abre a comunicação entre os servidores da aplicação e o ADS e os servidores do balance. 
- Sumário: "Abertura de firewall dos servidores destinatários do deploy"
- Notas: 
Abertura de firewall para a aplicação hospedada nos servires relacionados na porta 443 do ads e na mesma porta dos servidores de balance (BigIP ou NLB).
 
  - Servidores de Origem: [relacionar os servidores]
  - Servidores de Destino: https://ads.intra.fazenda.sp.gov.br
  - Porta: 443

  Regra B: Acesso ao BIG-IP
 
  - Servidores de Origem: [relacionar os servidores]
  - Servidores de Destino: [endereço do balance (BigIP ou NLB)]
  - Porta: 443"

### Criação de Deployment Group
Se o Deployment Pool desejado para o deploy não existir, ele será criado nessa task.
Ao final, o Deployment Pool será compartilhado com o projeto do ADS que contém o pipeline. 

>O compartilhamento de um Deployment Pool em um projeto é chamado de Deployment Group. Tags são criadas em Deployment Groups e não em Deployment Pools.
> Deployment Pools são acessíveis nas configurações da Collection: https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/_settings/deploymentpools
Os Deployment Groups disponíveis, compartilhados, são acessíveis no menu "Pipelines" do projeto. Por exemplo: https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Layout_Padrao/_machinegroup

Informe o endereço (link) desejado para a aplicação e a relação dos servidores no campo "Notas".
O nome do Deployment Pool será definido pela equipe de operações, mas pode ser sugerido nessa task. Em geral, costuma ser o nome do domínio no link previsto para a aplicação.

Exemplo:

```
Compartilhar com o projeto ADS: layout_padrao
Aplicação: https://webhomolog4.fazenda.sp.gov.br/modelo_core_mvc 
Nome sugerido do Deployment pool: webhomolog4
Servidores: 
. SRV11786 - IP: 10.216.38.182
. SRV11787 - IP: 10.216.38.183
```

Se o Deployment Pool já existir, essa task será finalizada

### Configurar o stage no pipeline
Preencha o campo "Notas" com as informações solicitadas nas tasks do stage do pipeline de release. 
- Notas
Configurar o stage de [homologação/produção] para o pipeline:
[link para o pipeline em modo de 'Edit']

  - Stage (Deployment process)
    - Website name - nome do website no IIS (secure/Seguro)
    - Virtual path - nome da aplicação, que está ao final do link dela.
  - Job (IIS Deployment)
    - Deployment group - nome do Deployment Group solicitado na tarefa anterior.

  - Balance:
Incluir duas tasks "Big-IP Node" configuradas com a operação "Stop node", no começo, e "Start Node", no final do stage. 
    - Endereços dos balances F5: [nome do balance ativo e do passivo]

  - IIS web app manage
    - Physical path - endereço da pasta (física) onde serão gravados os artefatos da aplicação (binários).

>Obs: 
Um balance F5 Big-IP possui dois endereços, um ativo e um passivo. Os dois devem ser digitados nos campos das duas tasks. O usuário e senha solicitado na tarefa deve ser preenchido durante a execução da RDM pelo colaborador da equipe de operações.
Balance do tipo NLB utilizam as tasks "Start Node" e "Stop Node" e não pedem informações adicionais.

###Validação
A última tarefa desse template de mudanças é destinado à própria equipe do projeto, que deverá fazer o primeiro deploy no ambiente e verificar se a aplicação está funcionando de acordo com a expectativa.
Depois que a equipe windows finalizar a quarta tarefa, o Remedy irá designar a equipe de desenvolvimento para adicionar alguma informação de sucesso ou não e finalizar a mudança. Isso vai gerar um email como esse:
![image.png](/.attachments/image-5ef2804a-16c9-4838-b573-cd4a70bab14d.png)

Ao abrir a task, preenchem-se os campos:
- Status: Fechado
- Informações de Trabalho:
  - Data: data do encerramento da RDM
  - Sumário: Verificação
  - Notas: quaisquer observações sobre o funcionamento da aplicação

Por exemplo:
![image.png](/.attachments/image-79f7dcb0-8c65-4317-a225-e1eb062a5dd1.png)


##eMails de concordância
Após o preenchimento das tarefas da RDM, enviam-se emails para as equipes de mudanças (<dtimudancas@fazenda.sp.gov.br>), segurança (<dtiseguranca@fazenda.sp.gov.br>) e windows (<dtiwinweb@fazenda.sp.gov.br>), antes da reunião de mudanças, solicitando a concordância com a execução das tasks da RDM, para verificarem se há algum problema em seu atendimento ou dúvida no preenchimento.

Por exemplo:
- Título: "Deploy Automatizado - Criação de Template de Mudanças - Concordância"
Texto do email: "Solicito a concordância da equipe de mudanças para a execução da mudança CRQ000000192173, em sua primeira tarefa "Deploy Automatizado - Criação de Template de Mudanças".
- Título: "Deploy Automatizado - Liberação de portas - Concordância"
Texto do email: "Solicito a concordância da equipe de segurança para a execução da mudança CRQ000000192173, em sua segunda tarefa "DEPLOY AUTOMATIZADO - ABERTURA DE FIREWALL DOS SERVIDORES".
- Título: "Deploy Automatizado - Criação de Deployment Group e Configuração de stage - Concordância"
Texto do email: "Solicito a concordância da equipe de mudanças para a execução da mudança CRQ000000192173, em sua quarta tarefa "DEPLOY AUTOMATIZADO - CRIAÇÃO DE DEPLOYMENT GROUP" e quinta tarefa "DEPLOY AUTOMATIZADO - CONFIGURAR O STAGE NO PIPELINE DE RELEASE".

##Data/Sistema
A data agendada deve ser, no mínimo, uma semana após a RDM ser preenchida, salva e avançada para o estágio de "Solicitação de Autorização".
Recomenda-se que a data final seja uma semana após o início, para que haja tempo para o atendimento de todas as tarefas e de eventuais testes.

##Itens de configuração
Os servidores destinatários do deploy, relacionados no levantamento de informações, devem ser relacionados na RDM na página "Relacionamentos" da RDM.
Por exemplo:
![image.png](/.attachments/image-4f5fd61a-84a7-4b87-b079-c405d4c9289b.png)
![image.png](/.attachments/image-99a632a5-2846-48d9-85f9-212f3fde3dce.png)

##Aprovação
Após salvar a RDM, ao clicar no botão "Próximo Estágio", estando tudo em ordem, a RDM passa para o estágio de "Solicitação de Autorização".
Um email será enviado automaticamente pelo Remedy ao solicitante:
```
A Mudança de Infraestrutura CRQ000000999999 foi enviada. Criação do Deploy Automatizado HML
...
Confirmação de Recebimento:
A Mudança de Infraestrutura CRQ000000999999 foi enviada em seu nome por .... 
Status: Solicitação de Autorização
Impacto: 4-Secundário/Localizado
Urgência: 4-Baixa
Sumário: Criação do Deploy Automatizado HML

Não responda este e-mail. Se for preciso, entre em contato com o Suporte Técnico.
```

Com isso, ela estará na pauta da próxima reunião da equipe de mudanças, programada semanalmente às quintas-feiras, dez da manhã, se a solicitação for criada com pelo menos três dias de antecedência.

O demandante será convidado a participar da reunião por email. Se não puder participar, a mudança será transferida para a próxima reunião até que uma pessoa designada pela equipe de desenvolvimento compareça para eventuais esclarecimentos.


---
#Implantação
Aprovada a RDM, as tarefas serão executadas na ordem em que estão relacionadas.

- A equipe de mudanças criará um template para o projeto para uso na task de [RDM automatizada](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Operações/Solicitações-de-infraestrutura/Requisição-de-Mudanças/RDM-Automatizada).

- A equipe de segurança irá abrir a porta 443 do ads e do BigIP para os servidores onde serão implantados os Deployment Groups.

- A equipe de operações (Windows ou Linux, conforme o caso), farão a [criação e configuração do Deployment Group](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Operações/Agent-Job/Deployment-Group) para uso do projeto.

- A equipe de desenvolvimento:
    - fará um [clone do stage de QA/Desenvolvimento (ou HML)](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/ADS/Deploy-automatizado/Release-pipeline/Configuração-de-stage), alterando o nome para "Hml" (ou "Prd");
    - fará as devidas [configurações para o deploy](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/ADS/Deploy-automatizado/Release-pipeline/Definição-de-variáveis-de-release), em variáveis e campos das tasks **antes da execução da RDM**.

- A equipe de operações:
    - verificará se o [stage de Dev/QA ou Hml](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/ADS/Deploy-automatizado/Release-pipeline) está em ordem, com pelo menos uma execução bem sucedida;
    - [incluirão as tasks](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/ADS/Deploy-automatizado/Release-pipeline/Inclusão-de-tasks) de Stop Node e Start Node, se necessário;
    - farão as devidas [configurações para o deploy](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/ADS/Deploy-automatizado/Release-pipeline/Definição-de-variáveis-de-release), em variáveis e campos das tasks.
    - incluirá um pre-deployment approvals nos stages de HML e PRD para a equipe dtiwinweb:
![pre-approval.png](/.attachments/pre-approval-a14fc67c-f08e-4c6d-bb56-b0c4087c95a9.png)

- A equipe de operações e a de desenvolvimento juntas:
    - farão [execuções do deploy](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/ADS/Deploy-automatizado/Release-pipeline/Execução-de-releases) e verificações até que tudo ocorra conforme o previsto.

Estando tudo em ordem, a RDM será finalizada.

A configuração de cada stage deve ser objeto de nova RDM. Não se pode configurar Hml e Prd em uma mesma mudança.
 