A Microsoft recomenda a utilização do modelo de processo [**Inheritance**](https://docs.microsoft.com/en-us/azure/devops/organizations/settings/work/inheritance-versus-hosted-xml?view=azure-devops&viewFallbackFrom=azure-devops-2020) na [criação de collections](https://docs.microsoft.com/en-us/azure/devops/server/admin/manage-project-collections?view=azure-devops-2020) no [Azure DevOps Server](https://docs.microsoft.com/pt-br/azure/devops/server/release-notes/azuredevops2020?view=azure-devops) (ads), compatível com as [organizations](https://docs.microsoft.com/pt-br/azure/devops/organizations/accounts/organization-management?view=azure-devops) do [Azure DevOps Service](https://azure.microsoft.com/pt-br/overview/) (cloud).

O DTI determinou que os projetos que estão na collection TPC-2016 e TPC-2010 precisam ser replicados nas novas.
[[_TOC_]]

---

#Novas Collections - inherited
Foram criadas duas novas Collections do tipo inherited no ads e no adshml.

- PRODUTOS
Collection padrão para projetos de desenvolvimento e gestão, em dois ambientes:
  - ads - https://ads.intra.fazenda.sp.gov.br/tfs/PRODUTOS
  - adshml - https://adshml.intra.fazenda.sp.gov.br/tfs/PRODUTOS

- ADMIN
Collection para projetos administrativos, testes e provas de conceito (PoC), também nos dois ambientes:
  - ads - https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN
  - adshml - https://adshml.intra.fazenda.sp.gov.br/tfs/ADMIN

# Antigas Collections - XML
Existem três Team Project Collections (TPC), na Sefaz, criados no antigo TFS, que se utilizava do formato XML.
- [TPC-2008](https://ads.intra.fazenda.sp.gov.br/tfs/TPC-2008) - foram retirados todos os usuários dos times dos projetos dessa collection, mas seus acessos podem ser solicitados através de abertura de chamado no [Suporte_Devops](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Suporte_DevOps/_wiki/wikis/Suporte_DevOps.wiki/126/Solicita%C3%A7%C3%A3o-de-acesso-a-projeto-no-ADS).
- [TPC-2010](https://ads.intra.fazenda.sp.gov.br/tfs/TPC-2010) - ainda possui diversos projetos ativos, que precisariam ter sido migrados para o TPC-2016.
- [TPC-2016](https://ads.intra.fazenda.sp.gov.br/tfs/TPC-2016) - collection atualmente em uso, já possui diversos projetos migrados para a nova.

---

# Process templates
O ADS define o conceito de [Process Template](https://docs.microsoft.com/pt-br/azure/devops/boards/work-items/guidance/choose-process?view=azure-devops&tabs=basic-process) como um conjunto de definições de itens de trabalho e seu ciclo de vida.
A Sefaz mudou o seu process template padrão duas vezes, desde a adoção do antigo TFS:
- Agile - usado nos projetos do TPC-2008.
- Ocorrências - criado pela extinta DI (Diretoria de Informações da CAT), foi utilizada no TPC-2010. Utilizava tipos de itens de trabalho inspirados no agile, mas aportuguesados e bastante modificados, com a chamada "Ocorrência" como seu principal tipo de workitem.
- Scrum - process template adotado pela Sefaz em 2016 como padrão. Todos os projetos ativos devem usar somente esses tipos de itens de trabalho.
Utilizado no TPC-2016, PRODUTOS e ADMIN.

## Migração de projetos para process templates diferentes

A migração de um projeto para um process templates diferente apresenta algumas dificuldades e pontos de atenção:
- Tipos de workitens que não existam no destino impedem a migração.
- Tipos que existam no destino, mas com campos que não existam perdem a informação.
- Tipos que existam no destino, mas que possuam um ciclo de vida com states diferentes ficarão afetados.

Para contornar essas questões, foram criadas na [collection PRODUTOS três process templates](https://ads.intra.fazenda.sp.gov.br/tfs/PRODUTOS/_settings/process):
- [Scrum SEFAZ](https://ads.intra.fazenda.sp.gov.br/tfs/PRODUTOS/_settings/process?process-name=Scrum%20SEFAZ&_a=workitemtypes) - padrão a ser usado em todos os projetos
- [Scrum SEFAZ - 2016](https://ads.intra.fazenda.sp.gov.br/tfs/PRODUTOS/_settings/process?process-name=Scrum%20SEFAZ%20-%202016&_a=workitemtypes) - cópia do Scrum Sefaz que inclui alguns tipos que foram usados em alguns projetos do TPC-2016 e que não deverão mais ser utilizados.
- [Scrum SEFAZ - Ocorrencias](https://ads.intra.fazenda.sp.gov.br/tfs/PRODUTOS/_settings/process?process-name=Scrum%20SEFAZ%20-%20Ocorrencias&_a=workitemtypes) - cópia do Scrum Sefaz - 2016 que inclui os tipos de workitens do template de ocorrências usado no TPC-2010

Com isso, é possível migrar os projetos do TPC-2010 para a collection PRODUTOS mantendo os itens de trabalho e possibilitando continuar usando as boards com os tipos previstos no template Scrum.
Assim, o problema de projetos cadastrados em collections não se comunicarem fica resolvido, ainda que permita que seus usuários continuem usando ocorrências.

## Mudança do tipo de workitem
A nova collection, entre outras vantagens, permite a alteração do tipo dos workitens.
Portanto, uma vez que um projeto seja migrado do TPC-2010, as suas ocorrências podem ser convertidas em Product Backlog Item (PBI) pelos seus administradores usando o próprio navegador.

Evidentemente, os campos que não existirem no novo tipo perderão sua informação.
Para mitigar o problema, os workitens migrados para a nova collection possuem em seu campo "Discussion" um link para o workitem original na collection antiga, que poderá ser utilizado para consulta das informações.

## Mudança de process template
Outro novo recurso na collection nova é a alteração de process template de um projeto.

Isso pode ser feito desde que não haja no projeto nenhum workitem de algum tipo não existente no novo process template.
Se houver, esses itens de trabalho devem ser excluídos ou os seus tipos devem ser alterados para algum existente no template destino, antes de fazer a mudança de process.

## Estratégias de migração de projetos do TPC-2010
Em virtude dos pontos explicados, há diversas possibilidades de ações sobre os projetos em uso no TPC-2010:
- Continuar no TPC-2010 - por determinação do DTI em 2017, os projetos não devem mais continuar utilizando o TPC-2010, apesar de alguns ainda permanecerem, em desacordo com o estabelecido.
Esses projetos possuem "enablers" indicando a dívida técnica a ser atendida.

- Criar um novo projeto no template Scrum Sefaz, migrar apenas os repositórios de código fonte, replicar manualmente os itens de trabalho ativo, queries e outros objetos e parar de usar o projeto na antiga collection, exceto para consulta.

- Usar as ferramentas de migração e replicar o projeto em PRODUTOS no process template "Scrum Sefaz Ocorrências". 
Nesse caso, abrem-se novas alternativas:
  - Prosseguir utilizando o projeto com uso de ocorrências - não há previsão normativa para essa opção.
  - Manter o projeto nesse process template, mas parar de usar ocorrências.
  - Alterar as ocorrências e demais tipos para aqueles previstos no Scrum Sefaz e alterar o seu process template para esse.

  > Essas três opções são evolutivas, assim a indefinição sobre a decisão de alterar o process template não impede a migração.

---

#Ferramentas de replicação (migração)
Foram desenvolvidos diversos scripts PowerShell, com o uso da ferramenta [Azure Devops Migration Tools](https://nkdagility.github.io/azure-devops-migration-tools/), para a criação e replicação dos projetos, disponíveis no repositório [Migração](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_git/Migracao?path=%2F&version=GBmaster).
Para utilizar essas ferramentas, faça um download desse repositório para uma pasta local.
**Leia as instruções** do arquivo [Instruções.txt](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_git/Migracao?path=%2Fver2.0%2FInstrucoes.txt).
> Para [habilitar a execução de scripts PowerShell](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-7.1) em seu computador, se necessário, inicie o Windows PowerShell com a opção "Executar como administrador" e execute a instrução:
`Set-ExecutionPolicy -ExecutionPolicy Bypass -Scope LocalMachine`

A migração de workitens só funciona com destino nas collections inherited.

## Relação dos projetos
Os projetos a serem migrados devem ser relacionados no arquivo texto _[Projetos.csv](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_git/Migracao?path=%2Fver2.0%2FProjetos.csv&version=GBmaster)_.
Esse arquivo pode ser editado no bloco de notas ou no Excel.
Esse arquivo é uma tabela com três colunas:
- Origem - nome do projeto no destino
- Destino - nome do projeto no destino
- Template - template usado na criação do projeto no destino, quando necessário.
> Cuidado com os nomes dos projetos, que são "case sensitive".
Nome contendo espaços, caracteres especiais, acentos e cedilhas podem prejudicar a migração.

## Criação dos projetos
Os projetos a serem migrados precisam existir na collection destino.
> Somente os administradores do ads conseguem criar projetos. Portanto, se a migração for feita por alguém que não é administrador do ads, ele deve solicitar a criação do projeto através de chamado no [Suporte_Devops](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Suporte_DevOps/_wiki/wikis/Suporte_DevOps.wiki/125/Cria%C3%A7%C3%A3o-de-um-novo-projeto-no-ADS), com a inclusão do usuário que vai fazer a migração no grupo "Project Administrators".

Eles podem ser criados manualmente ou através do script _[CriarProjetos](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_git/Migracao?path=%2Fver2.0%2F0-CriarProjetos.ps1&version=GBmaster)_.
O script usa o arquivo Projetos.csv para essa criação.
- As linhas sem a indicação do template serão ignoradas nesse processo.
- Projetos que já existem no destino não serão afetados.
- Utilize nomes de projetos sem caracteres especiais, acentos ou cedilhas.

## Replicação (migração)
A migração se faz com a execução do script [_MigrarProjetos_](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_git/Migracao?path=%2Fver2.0%2F1-MigrarProjetos.ps1&version=GBmaster), que faz a chamada de outros scripts e do [Migrantion Tools](https://nkdagility.github.io/azure-devops-migration-tools/).
Apesar de se poder executar todas as migrações de uma só vez, recomenda-se sejam feitas em partes, certificando-se que não houve falhas na execução anterior.

Inicialmente, executam-se os scripts mais rápidos:
1. Usuários
1. Swimlanes das Boards
1. Colunas das Boards

Então, migram-se os repositórios:
1. Repositórios GIT
1. Wikis (links **não** são alterados, requerem verificação e correção posterior à migração)

Após a execução bem sucedida dos primeiro itens, os artefatos relacionados a workitens são replicados usando a aplicação Migration Tools:
1. WorkItens
1. TeamSettings
1. Shared Queries

Em seguida, mais alguns scripts PowerShell são executados:
1. Plans
1. Iterations (member capacity)
1. Ajuste de Workitens nas colunas das Boards (columns)
1. Dashboards
1. Pipelines

Ao final, se necessário, migram-se os Test Plans:
1. Test Plans

### Observações

####Criação de Personal Access Token - PAT
Para a migração de workitens, é necessário criar um PAT para cada instância que se deseja usar (ads e adshml).
- No ads (ou adshml) clique no ícone de seu perfil, no canto superior direito, e selecione "Security"
- Clique em "New Token"
  - **Nome** - Digite qualquer coisa em 
  - **Organization** - selecione "All accessible organizations"
  - **Expiration** - escolha "Custom defined" e selecione a data mais tardia possível (um ano)
  - **Escope** - "Full access"
- Clique em "Create"
![image.png](/.attachments/image-5d48d9ff-7b10-4b1a-9be1-b1cd0a1bb0b1.png =300x)

- Copie o PAT criado e guarde em algum arquivo texto, pois ele não pode ser consultado, apenas recriado.
![image.png](/.attachments/image-c3ade60f-f75d-4a05-af22-fca9f570b58c.png =300x)

####Links com código fonte
- Os links entre workitens e changesets do código fonte não são migrados, mas apenas ficam os IDs do Workitem de origem nos changesets migrados.
  - Para localizar o workitem a partir do changeset, pegue o ID e procure o WI na collection de origem.
  - Para localizar o changeset a partir do workitem, clique no link da migração no campo "Discussion" e veja o changeset relacionado na collection de origem. 

####Pipelines
- Na migração de pipelines, exibem-se mensagens para aqueles que não puderem ser replicados, que precisam ser recriados manualmente.
Não se consegue migrar pipelines que contenham tasks não nativas, como as tasks Fortify e Shell++, por exemplo.

- Pipelines de release podem precisar de edição, com a exclusão e adição dos artefatos, após a migração.

#### Wikis
- Os links dentro das Wikis devem ser corrigidos manualmente, após a migração.
- Quando houver links de Desenho de Solução na wiki, é necessário atualizar: incluir o parâmetro de collection no link para o visualizador e remover a tag DS_Raiz na collection antiga; atualizar link para a query de DS; atualizar link para a query de tarefas.

#### Queries
- Apenas Shared Queries são migradas.
My queries precisam ser movidas para Shared Queries antes de migrar.

- Pode haver quebra de links para projetos que não existam no destino, no momento da migração, que se observa em mensagens de erro na tela de execução do script. Quando o projeto relacionado for migrado, links em workitens serão corrigidos.
Porém, as queries, widgets (dashboards) e plans continuarão quebrados.
Quando isso ocorre, há duas opções:
  - Corrigem-se os objetos manualmente.
  - Após o projeto destino ser migrado, excluem-se as queries, widgets e plans a ele ligado e refaz-se a criação e migração desses objetos. 

#### Planos de testes
- A replicação de Test Plan é bem sensível a falta de informações, como referências a projetos que não foram migrados ou datas não definidas. 
Quando acorre um erro na migração de um Test Plan, o Migration Tools interrompe o processamento e não migra os demais planos. Nesse caso, o Test Plan que causou o problema precisa ser corrigido ou excluído na origem, para que os demais possam ser processados.

- Não há previsão, no Azure DevOps, da existência de workitens do tipo "Test Suite" desvinculados de "Test Plan".
Por isso, na migração de workitens, no arquivo [configuration.json](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_git/Migracao?path=%2Fver2.0%2FMigrationTools%2Fconfiguration.json), há um filtro para não migrar os tipos 'Test Suite', 'Test Plan', 'Shared Parameter', 'Shared Steps', 'Code Review Request' e 'Code Review Response'.

- Os resultados (outcome) dos Test Points não são migrados. 

- Recomenda-se que os Test Plans sejam verificados após a migração. 
Aqueles que não foram migrados ou que apresentarem algum problema devem ser corrigidos manualmente.

#### Fora da migração
- Não foram desenvolvidos scripts de replicação de Analytics Views. 
Esse recurso deve ser replicado manualmente. 

- Também **não** são replicados **repositórios TFVC**, que devem ser feitos através da [migração de controlador de versão com o GIT-TF](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Desenvolvimento/Git/Migração-de-controlador-de-versão-%2D-TFVC-para-o-Git).

#### Work Item com erro ao salvar devido a algum campo que "must be empty"
- Após a migração, aparece o erro **The value for field “Closed By” must be empty** ao tentar salvar o WI. Também ocorre **The value for field “Activated Date” must be empty**.
- Esse caso ocorre quando o WI tiver sido fechado e reaberto na collection antiga, antes da migração. Tentar alterações no status pode resolver o problema, ex: mudar o campo State para Approved e depois voltar para Commited. Se houver algum caso em que a alteração de status não resolva, abrir chamado no suporte_desenv (o tipo do WI será alterado para exibir e permitir edição do campo Closed By).

#### Imagens dentro da descrição do WI não são migradas
- Caso na descrição do WI tenha sido coladas imagens, tais imagens não são migradas. Como o link para a collection antiga continua, a imagem é mostrada de forma transparente se o usuário tiver acesso à collection antiga. Ou seja, um novo usuário na collection nova não visualizará a imagem enquanto não receber acesso à collection antiga.

# Junção de projetos
Esses mesmos scripts podem ser utilizados para juntar diversos projetos em um só.
Para isso, basta que o nome do projeto no destino seja o mesmo para aqueles que serão juntados.
Cada projeto da origem juntado ao projeto destino será uma área desse.

A junção de projetos poderá ocasionar alguns problemas.
Como o projeto juntado a outro não existirá mais, se não como uma área desse, as queries e plans ficarão prejudicadas.
Essas quebras são mostradas na tela de execução dos scripts, que podem ser copiadas e coladas em arquivos texto para verificação.

Esses links deverão ser corrigidos manualmente, onde houver.

Os links de workitens a esses projetos poderão ser corrigidos se a junção for feita depois da migração.

> Atenção: O Powershell mantém somente 3000 linhas de tela na execução, enquanto que o Powershell ISE mantém todas as linhas.
Prefira sempre a execução dos scripts pelo Powershell ISE.






