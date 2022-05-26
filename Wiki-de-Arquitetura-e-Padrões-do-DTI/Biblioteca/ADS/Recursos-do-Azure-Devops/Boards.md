O painel «Board» do Azure DevOps oferece acesso aos registros de workitens, que auxiliam a gestão das atividades de desenvolvimento.
>[Process template](https://docs.microsoft.com/en-us/azure/devops/boards/work-items/guidance/choose-process?view=azure-devops&tabs=basic-process) é um conjunto de definições de tipos de workitens.
Por determinação da Sefaz-SP, os projetos de desenvolvimento devem utilizar o template scrum.
[Veja SAFe...](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Governança/SAFe-%2D-Scaled-Agile-Framework)

---
#Workitens
Workitem é um conjunto de descrições e informações do trabalho a ser feito. Esse conjunto varia conforme o tipo do workitem.

O painel «Work itens» da «Board» é utilizado para administrar o backlog do projeto.

Ele oferece diversas visões (filtros) das tarefas.
![Visoes.png](/.attachments/Visoes-615ab5bd-de50-496f-8c31-21d35dcc4135.png =500x)

Também exibe um botão para a criação de novos workitens, nos padrões do template scrum.
![Novo.png](/.attachments/Novo-153f10b9-baeb-49d2-8509-9feb46b2b108.png =500x)

«Epic» é um conjunto de «Feature», que é um conjunto de «Test case», «Bug», «Impediment» e «Product Backlog Item» (PBI). Esses últimos podem ainda agrupar «Task». 

Cada tipo de work item possui um propósito e um conjunto diferente de informações necessárias.
-	Épico – usado por gestores de produto, exige apenas um título e descrição.
-	Feature – também usado por gestores, agrupa PBIs. Exige apenas título e descrição.
-	Product Backlog Iten (PBI) – é a principal unidade de serviço de um projeto. 
São desejáveis os preenchimentos dos campos:
    -	Título
    -	Descrição
    -	Critério de aceitação ou definição de pronto
    -	Discussion ou histórico – espaço para adicionar informações diversas sobre o andamento da atividade, como relatos de falhas durante a sprint, dificuldades ou explicações.
    -	State – indicador de estado do atendimento do PBI, apresenta os valores:
        -	New – Criada uma demanda, porém ele ainda não está bem compreendida pela equipe de desenvolvimento.
        -	Approved – o item de trabalho está bem entendido e está pronto para ser iniciado.
        -	Commited – a tarefa está sob responsabilidade de alguém.
        -	Done – a atividade foi considerada finalizada.
  -	Bug – relato de um defeito observado em uma aplicação.
  -	Task – subdivisão opcional de um PBI ou Bug, devido à sua complexidade ou por exigir a atuação de mais de uma pessoa simultaneamente.
  -	Impedimento – relato de alguma situação que impeça a continuidade do trabalho.
  -	Test case ou teste – descrição de tarefas a serem executadas para verificação dos critérios de aceitação.

Em um workitem, há um campo de preenchimento opcional logo abaixo do título, sem descrição, ao qual podemos chamar de designação (assignment).
Ao clicar nesse campo, alguns usuários podem aparecer listados. Ao se digitar parte do nome ou login desejado, a lista vai sendo filtrada. Se o usuário a ser designado não aparecer na lista, clica-se no ícone com forma de lupa para uma busca na lista de usuários válidos do projeto.

Além do navegador, o MS Excel também pode ser usado para trabalhar com Work Itens. [[Veja Mais...]](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/ADS/Recursos-do-Azure-Devops/Boards/Uso-do-Excel-para-trabalhar-com-Work-Itens)

---
##Backlog
Backlog é um grupo de workitens. Team backlog são todos os workitens de um time. Sprint backlog é um subconjunto de workitens selecionado para ser atendido em um prazo determinado.

O menu Boards oferece diversas opções de visualização de workitens.
- Boards – forma de quadro conhecida como kanban, exibe os workitens em colunas, separados pelo campo “State”: _New, Approved, Commited e Done_. 
- Backlog – exibe os workitens por team.
- Sprints – mostra os workitens por team e sprint.
- Queries – permite a criação de consultas diversas para facilitar a visualização de workitens.
