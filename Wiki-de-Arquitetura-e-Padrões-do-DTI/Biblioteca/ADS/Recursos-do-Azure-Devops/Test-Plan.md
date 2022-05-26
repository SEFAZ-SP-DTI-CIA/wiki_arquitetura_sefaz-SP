O Azure Devops oferece uma ferramenta para [**Gerenciamento de testes**](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Desenvolvimento/Gerenciamento-de-testes), acessível no menu lateral esquerdo pela opção "Test Plans".

<IMG  src="https://docs.microsoft.com/en-us/azure/devops/test/media/new-test-plans-page/test-plan-overview.png?view=azure-devops-2020"  alt="test plan overview page"/>

Veja a documentação completa sobre a utilização de Test Plan em:
https://docs.microsoft.com/pt-br/azure/devops/test/?view=azure-devops

[[_TOC_]]

#Test Plan
Um [Test Plan](https://docs.microsoft.com/en-us/azure/devops/test/new-test-plans-page?view=azure-devops-2020) é um conjunto de Test Suites, que são grupos de Test Cases, que são listas de atividades de teste de uma aplicação.
Esses elementos são armazenados no projeto ads como [tipos especiais de itens de trabalho](https://docs.microsoft.com/pt-br/azure/devops/test/create-a-test-plan?view=azure-devops#what-are-test-plans-test-suites-and-test-cases).
Diferente de outros tipos de workitens, o Test Plan não pode ser criado em boards, mas apenas na sua página.

![image.png](/.attachments/image-6bd7c246-0c8b-4abb-902a-c366dbb3aef7.png =350x)

Depois de criado, clicando em "Edit test plan", em seu menu de contexto, abre-se a página com todos os campos desse workitem, em especial, os campos "Start Date" e "Finish Date".

Clicando em "Test plan settings", ele pode ser associado a um build ou a algum pipeline de release que faz o deploy da aplicação onde serão realizados os testes.

![image.png](/.attachments/image-059bef58-caee-493a-aa7c-9bc2040e1134.png =500x)

#Test suite
Conjunto de testes ou Test Suite agrupa casos de testes dentro de um Test Plan, identificando um cenário e facilitando sua gestão.

Ao se criar um Test Plan, um Test Suite é criado com o mesmo nome do plano.
Para criar um novo Test Suite, clica-se em no botão de contexto do Test Plan:
![image.png](/.attachments/image-d89ad25c-f7e3-41ea-871b-d04fbc4c44e7.png =450x)

Em um Test Suite, associam-se Test Cases por três meios:
- Novo Test Case - o ads cria um novo workitem desse tipo e associa ela ao Test Suite.
- Adicionar um Test Case existente - exibe-se uma query para pesquisa de Test Cases. Após a execução da consulta, selecionam-se os workitens e clica-se no botão "Add Test Cases".
- Criar Test Cases usando uma tabela - abre-se uma tabela para que se preencha com informações dos Test Cases a serem criados. 

#Test case
Test case é um tipo de workitem contendo, entre outras informações, passos a seguir na execução de um teste de uma aplicação.
Casos de teste validam partes individuais da aplicação ou sua implantação.


#Execução de testes
##Test configuration
Uma [configuração de teste](https://docs.microsoft.com/pt-br/azure/devops/test/test-different-configurations?view=azure-devops#create-configs) é uma combinação de valores de variáveis de configuração que pode ser atribuída a um Test case ou Test Suite. 
As variáveis de configuração podem ser, por exemplo, o sistema operacional, o navegador, o tipo de CPU e o banco de dados. 

##Test point
Um Test Case associado a um Test Configuration é um [Test Point](https://docs.microsoft.com/en-us/azure/devops/test/new-test-plans-page?view=azure-devops#execute-tab), que verifica como a aplicação se comporta sob esse testes em um ambiente definido.
O que o testador executa não é o Test case, mas o Test Point.



