### Criação de de projetos de aplicação no SSC

Este procedimento tem como objetivo descrever o processo adotado pela Sefaz para criação de aplicação no SSC (Software Security Center) para efetuar o redirecionamento do projeto após realizar scan estático através do ADS.

**Criação de Projeto**

Acesse ao link https://fortify.intra.fazenda.sp.gov.br:8443/ssc/#!/ e coloque seu usuário e senha conforme imagem abaixo:

![image.png](/.attachments/image-fe774c43-b34e-4197-b37c-fb6b13f16030.png)

Agora clique em “+ New Application” no lado direito da tela:
![image.png](/.attachments/image-74d71f7b-5efd-4727-ac0d-656b7dfc0629.png)

Preencha as informações de acordo com o que será configurado no ADS ("Aplication Name" e "Version Name") para que não ocorra problemas no redirecionamento após realização do scan:

Após preenchimento clique em “NEXT”:
![image.png](/.attachments/image-125a1b1d-ac0b-4536-acce-fc6a9140624f.png)

Preencha as informações de acordo com a aplicação desenvolvida e clique em “Next”:
![image.png](/.attachments/image-6846c2ea-67af-4f03-9745-831c971df962.png)

Mantenha o padrão atual de análise de alto risco e clique em “NEXT”.
![image.png](/.attachments/image-b5522feb-f1fe-4d9f-bf5c-9c73f12c4ecc.png)

Atribua as responsabilidades a equipe e clique em Finish:
![image.png](/.attachments/image-cfd9e1cd-31d6-49ed-afc5-7dbc079b5f5a.png)

Aplicação criada com sucesso e aguardando o upload do projeto através do ADS ou realizando manualmente o upload do arquivo ".FPR".
![image.png](/.attachments/image-7a2f1ad0-9478-49a9-95c6-fe7df99ab547.png)

### Integrando o Fortify ao ADS

Configure o Agent-Build "Fortify" no Pipeline para compilar as builds que supostamente serão disparadas.

Clique em Agent Job para buscar e adicionar o plugin do "fortify Satatic Code Analyzer Assessment":

![image.png](/.attachments/image-45a3cbcc-d4f8-412a-b949-a8d0c4a8b7e3.png)

![image.png](/.attachments/image-c152aa17-057f-42fe-8027-a12235c1ac0c.png)

Configure o nome do Build e selecione o SCA verbose e SCA debug, escolha o tipo de aplicação ".NET" e selecione a solution que será analisada.
![image.png](/.attachments/image-88234326-d67f-4fa1-bc5d-4f8e2f3fa209.png)

Habilite a opção de "Upload Results to SSC" e caso não tenha o endpoint criado no ADS adicione as informações clicando em “manage”,  clique em “+ New Service Connection/Generic” e adicione um novo endpoint para a o servidor. Após colocar as informações
corretas clique em OK.

![image.png](/.attachments/image-3ddee912-0338-4aad-974b-c8c198bee5a8.png)
![image.png](/.attachments/image-00458187-5cab-49c3-a668-8a7ce259a85e.png)

Selecione a opção de conexão criada em "Upload Results to SSC" e prossiga com o preenchimento do nome do projeto criado na console do SSC.

As informações deverão ser as mesmas informadas na criação da aplicação no SSC exemplo: SSC Application Name "DVWA" e SSC Application Version "1.0" 
![image.png](/.attachments/image-47c91fe1-3e95-4ac2-b525-f621aa35a576.png)

Salve a configuração e ao rodar certifique-se que foi selecionado corretamente o Agent Job.
![image.png](/.attachments/image-e7e457a6-653e-46ce-b045-8a79ed6d38c1.png)
![image.png](/.attachments/image-ee3ac16a-0891-4e86-a1d6-657acbc4a505.png)

Após a conclusão do scan realizado, acesse a console do SSC e selecione a aplicação que foi criada:
![image.png](/.attachments/image-b448b43c-88a7-46aa-b475-a0ef963d2e8d.png)

Na opção "GROUP BY" podemos escolher diferentes maneiras de visualizção das issues detectadas:

![image.png](/.attachments/image-0e361d15-2a80-453d-975e-cd0689952edc.png)

Na opção "FILTER BY" podemos aplicar o filtro por criticidade:
![image.png](/.attachments/image-5d0391c8-bcbb-4a09-9257-0459a0c495c3.png)


## Classificando as Issues

Após selecionar uma determinada "ISSUE" clique na opção de "Analysis" para seguir com a classificação da vulnerabilidade:

![image.png](/.attachments/image-a0cb6045-faea-47d6-a3aa-3267a5c4194d.png)

Conforme abaixo segue opções:
Falso Positivo: Quando identificado que a vulnerabilidade listada trata-se de um falso positivo.
Má Prática: Normalmente atribuído quando a vulnerabilidades foi apontada devido a utilização de um recurso ruim.
Necessidade de Análise: Não foi possível a identificar a confirmação do problema.
Vulnerabilidade Confirmada: Identificado que a vulnerabilidade existe.
Vulnerabilidade em Correção: Já atribuído ao processo de correção.
Corrigida: Vulnerabilidade corrigida.
![image.png](/.attachments/image-168a61c4-2721-4261-842f-7440b3216c94.png)

Pode-se atribuir a vulnerabilidade a um responsável especifico, clique em "Apply":
![image.png](/.attachments/image-cf0ea3c6-aa19-4f55-9793-0e847f856b80.png)

![image.png](/.attachments/image-e309f0a2-5621-4460-a726-70b22c9b6294.png)

## Configurando a opção de BUG TRACKER

Em "PROFILE"/"BUG TRACKER", pode-se realizar o apontamento das ISSUES diretamente nas Work Itens do projeto:

Para seguir com a configuração selecione a opção de "TFS/VSTS"

![image.png](/.attachments/image-4a93ba14-780f-418f-bbdd-f002045b876b.png)

Após isso aplique as informações abaixo:
```
Bug Tracker URL - É o caminho do ADS ou TFS.
http://tfs.intra.fazenda.sp.gov.br:8080/tfs/

Allowed Values Collection - Caminho da Collection criada do ADS ou TFS
TPC-2016 ou PRODUTOS
 
Default Value Collection - Caminho da Collection criada do ADS ou TFS
TPC-2016 ou PRODUTOS
 
Allowed Values Project - Nome do Projeto
FORTIFY

Allowed Values Work Item Type - Nome da task que será adicionada em "Boards"
Bug
```
Após adicionar as informações, para validação da conexão clique em "Test Connection" e coloque as informações de usuário e senha do ADS ou TFS.
![image.png](/.attachments/image-9dbe2f53-a119-4e22-8954-2804e56ecf1f.png)

Deve-se retornar Sucesso.
![image.png](/.attachments/image-51ec8eec-f82f-4138-a03c-770ff7e31c52.png)

Em seguida clique em "Apply", **NÃO** selecionar o checkbox de "**Clear All**" e "Ok" para finalizar a configuração.
![image.png](/.attachments/image-1d2daa19-1007-4eb0-9cdd-18344cf8727c.png)
![image.png](/.attachments/image-7c53418d-42f4-46f8-8147-f13f1143abe3.png)

Para criar um WORK Item, deve-se selecionar a vulnerabilidade e clicar no ícone de "BUG":
![image.png](/.attachments/image-928d407b-5669-4092-9bb7-23a1941d015d.png)

Adicionar o usuário e senha de conexão:
![image.png](/.attachments/image-71ac5d52-28d4-42ba-8c4b-e401b2a388b7.png)

E escolha as configurações do projeto, classificação da prioridade e severidade do BUG.
![image.png](/.attachments/image-61c7b5b9-3391-4845-80bc-a561f5b1e971.png)
Cliquem em "Submit" para criar o direcionamento para o projeto analisado.

Clique sobre o ícone de "Bug" para ser redirecionado:
![image.png](/.attachments/image-e1f1a214-b863-4e9d-b6b3-f32b2bf40696.png)
![image.png](/.attachments/image-180da0c7-02af-41c8-af59-83eb3540a2f4.png)

