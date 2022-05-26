Os arquivos controlados pelo Git podem ser replicados no que se chama de "branch", uma ramificação com uma cópia idêntica dos arquivos na sua criação, mas que será modificada.
Essa ramificação possibilita o paralelismo no trabalho de desenvolvimento de forma a evitar que alterações inacabadas em uma parte do código afetem outra parte de forma indesejada.

Todo repositório Git possui, pelo menos, uma branch, que inicia com o nome "master".

As branchs podem ser administradas por dois elementos da tela do Visual Studio:
- Guia "Git Repository" - exibível pelo menu "Git - Manage Branches".
- Painel "Git Changes" - pode ser mostrada pelo menu "View - Git Changes"

![image.png](/.attachments/image-863c0270-84ca-4771-b86e-f3a5acccbcc6.png)


[[_TOC_]]

#Branch
Cada branch se comporta como se fosse um repositório, com seus próprios arquivos, histórico e alterações.
Os commits feitos em uma branch afetam somente ela própria, mas esses commits podem ser mesclados entre merges, gerando um novo commit.

##Criar uma branch
Para criar uma nova branch a partir de outra, há dois caminhos:
- Painel "Git" - clica-se na caixa com o nome da branch, no topo do painel (master) e em "New Branch".
- Guia "Git Repository" - clica-se com o botão direito sobre a branch e seleciona-se "New Local Branch From...".

Nos dois casos, abre-se uma janela solicitando um nome para a nova branch, que não pode conter espaços, cedilhas ou acentos.

##Mudar de branch
Para alterar a branch ativa, há duas opções:
- Painel "Git" - clica-se na caixa de branch e seleciona-se a desejada.
- Guia "Git Repository" - duplo clique sobre a branch desejada.

##Merge
Conforme alterações vão sendo feitas nas branches, seus históricos vão diferindo, conforme se observa na guia "Git Repository":

![image.png](/.attachments/image-55afbf89-8fdb-4e61-b267-dd7d52e1c733.png =500x)

Espera-se, na maioria dos casos, que as alterações feitas em uma branch sejam replicadas, pelo menos em parte, em sua branch de criação, em um procedimento denominado de "Merge".

Para fazer um merge, também há duas alternativas:
- Painel "Git" - estando ativa a branch que vai receber as alterações (com o nome em negrito), clica-se na caixa de branch, depois, com o botão direito do mouse, na branch a ser mesclada e seleciona-se "Merge into Current Branch".
- Guia "Git Repository" - clica-se com o botão direito sobre a branch e seleciona-se "Merge [nome da branch] into master".

Se não houver diferenças, uma mensagem será exibida informando.
Havendo diferenças, o Git replica as alterações e também os commits, para eventual consulta ou reversão das mudanças.

![image.png](/.attachments/image-89fe42ac-0f04-47ba-b33b-be1ce9c364a9.png =500x)

##Estratégias de uso de branch
O Git não estabelece formas de se criar branchs, mas a Sefaz-SP sugere algumas [estratégias de criação e uso de branches](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Desenvolvimento/Git/Estratégias-de-branch).

##Cherry-pick
Recurso para selecionar commits que serão mesclados em uma branch na página "Git Repository". 
Para fazer essa seleção, a branch a ser mesclada deve estar ativa.
Em seu histórico clica-se com o botão direito do mouse em cada um dos commits desejados e seleciona-se «Cherry-pick».
Então, faz-se o merge.
