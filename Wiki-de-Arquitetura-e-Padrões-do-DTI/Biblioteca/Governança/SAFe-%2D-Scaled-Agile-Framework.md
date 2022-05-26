O [SAFe - Scaled Agile Framework](https://www.scaledagileframework.com/) é um framework que se baseia em técnicas do desenvolvimento ágil para toda a organização. 

O desenvolvimento de sistemas da SEFAZ é realizado através do modelo de gestão por produto do SAFe, onde cada time de desenvolvimento trabalha de forma alinhada com o time de gestão do produto.

## Produtos SAFe no ads
Os projetos de desenvolvimento cadastrados no ads da Sefaz foram agrupados em [**Produtos SAFe** _(consulte aqui os Produtos e equipes)_](
http://srv36235:8081/AdminConsole/sharereport.jsp/RunReport.i4?reportUUID=c8ac6ca1-e7c7-4962-a582-46726ee39790&primaryOrg=1&clientOrg=13147&bookmarkUUID=6bd654ec-b962-4f9b-a7ad-153455a79d4b&arhost=arsapp&port=3000&midtier=sefaznetvirtual.intra.fazenda.sp.gov.br&protocol=https)

No ads não existe esse conceito explícito. Por isso, cada produto é cadastrado como um projeto ads, com seu nome iniciado pela palavra "SAFe", com os repositórios de código, pipelines, planos de testes e artefatos desabilitados.

Esses _Produtos_ possuem boards, wikis, dashboards e plans, que auxiliam a ligação entre os projetos, que devem necessariamente estar na mesma collection.

## Equipe e planejamento do trabalho
A equipe do produto é formada pelo PM, RTE, Arquitetos e GP, com BO direcionando os objetivos do negócio.
A equipe de cada sistema é composta pelo PO, SM e demais integrantes do desenvolvimento.

O trabalho no âmbito do produto é acompanhado por PIs (Program Increments). Cada PI tem duração de 3 a 4 meses. O trabalho no âmbito do sistema é acompanhado por iterações (sprints). Cada sprint tem duração de 3 a 4 semanas.

## Itens de trabalho

No contexto de implementação SAFe, de acordo com o texto ["Scale Agile to Large Teams"](https://docs.microsoft.com/en-us/azure/devops/learn/agile/scale-agile-large-teams?view=azure-devops), da Microsoft, são de utilização comum os itens de trabalho dos tipos:
- Epics - são quebrados em features e representam iniciativas importantes para a organização. Pode se referir ao trabalho de diversos times de desenvolvimento e a diversas sprints. É um trabalho que terá duração maior que um PI (Program Increment).
- Features - divididas em stories, funcionalidade é alguma coisa completa, construída para o usuário. Cada Feature deve "caber" dentro de um PI, ou seja, se for um trabalho maior, deve ser quebrado em mais de uma Feature.
- Stories - são incrementos das features que acrescentam algum valor para a entrega. Podem ser divididas em tasks, se necessário. No ads, são chamados de Product Backlog Item (PBI).  Cada PBI deve "caber" dentro de uma Iteração (Sprint).
- Tasks - qualquer tarefa necessária para completar um PBI.

>Algumas organizações utilizam um outro tipo de workitem denominado Initiatives, que agrupa epics, com uma semântica própria. A SEFAZ ainda não utiliza Initiatives.

Em geral, Epics e Features são criados no Team Project do Produto, cujo backlog é administrado pela equipe do Produto (PM e RTE), enquanto que os PBIs e Tasks são criadas no Team Project do Sistema e utilizados pela equipe de desenvolvimento (o backlog é administrado pel o PO e SM).

Os PBIs no Team Project do Sistema deve ser associados como "filhos" das Features no Team Project do Produto.

