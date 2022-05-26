A partir do Visual Studio 2019, com o desenvolvimento do menu Git e do painel "Git Changes", a utilização do painel "Team Explorer" para manutenção do código Git não é recomendada e está com recursos obsoletos.

[[_TOC_]]

#Clone
Utilizando-se o painel «Team explorer» para administrar conexões e alterações de código, clica-se em «Manage Connections» e «Connect to a project». 
Na janela que se abre, digita-se o nome do repositório na linha de pesquisa. Se for encontrado, ele será exibido no quadro central.

Ao selecionar o repositório, a janela exibe um campo de preenchimento para definir um local para gravação dos arquivos e um botão na parte inferior a opção «Clone» para iniciar um repositório local a partir de um remoto. Se o repositório local já existe no endereço, clica-se em «Conectar».

![Connect_ads.png](/.attachments/Connect_ads-3a94d2ad-b3c1-415b-81a6-0529040d67e6.png =400x)

#Alterações
Toda alteração feita em um repositório local deve ser preparada e confirmada, processo chamado de [_Commit_](https://git-scm.com/docs/git-commit), equivalente ao _checkin_ do TFVC, antes de ser enviada ao repositório remoto. 

As alterações podem ser visualizadas no painel Team Explorer,  opção «Changes»:
![Changes.png](/.attachments/Changes-ce1cf886-d72c-4c4f-a7cf-889c0c857ed9.png =300x)

O compartilhamento de alterações no Git, é feito em quatro ações: preparar (stage), confirmar (commit), buscar (pull) e enviar (push). Essas duas últimas podem ser feitas em uma só ação chamada sincronizar (sync). 

 ##Stage
Cada modificação pode ser preparada ([add ou stage](https://git-scm.com/docs/git-add)) individualmente, ou se pode adicionar todas as alterações ao stage clicando no botão com um sinal de mais:
![Changes_stage.png](/.attachments/Changes_stage-f1fe116f-e502-4cca-ae98-4b615c6b52f4.png =300x)

##Commit
A gravação das alterações ([commit](https://git-scm.com/docs/git-commit)) solicita uma mensagem ou observação para entendimento do que está sendo modificado.
>Um workitem pode ser relacionado à alteração digitando-se seu número (id) precedido pelo símbolo de cerquilha, inserido nessas observações, ou adicionando no campo «Itens de trabalho relacionado» (Related workitens).

![Commit_staged.png](/.attachments/Commit_staged-49af9f4a-5202-46b7-9320-7a65b3af9f7a.png =600x)

#Sincronização
No painel «Team Explorer», administram-se as alterações gravadas nos repositórios local e remoto. 

![Sincronizando.png](/.attachments/Sincronizando-8ab12714-a7d5-41ef-8568-27742221828c.png)

Basicamente, há duas ações de controle: 
-	**Pull** - Confirmações de entrada 
Ao clicar em «Buscar», exibem-se as alterações no repositório remoto que não estão gravadas no local.
Clicando em «Efetuar Pull», são exibidas as alterações disponíveis. 
-	**Push** - Confirmações de saída 
Havendo alterações confirmadas (commit), se pode enviá-las por «Push»
A opção «Sincronizar» faz as duas operações, primeiro os pulls e em seguida os pushes.

#Resolução de Conflitos
Quando dois colaboradores efetuam alterações em um mesmo arquivo, podem haver conflitos que não se resolvam sozinhos, como modificações em uma mesma linha.
Nesse caso, o Visual Studio, após tentar a sincronização, exibe uma mensagem solicitando a definição e confirmação das alterações que serão definitivamente gravadas no repositório remoto.

![Sincronizar.png](/.attachments/Sincronizar-c57fb106-b3fa-4ca2-a4e9-0fc4cd9be8b7.png)

Ao clicar em «Conflitos», o Visual Studio exibe os arquivos que precisam ser analisados. 

![Conflitos.png](/.attachments/Conflitos-c9e366e4-bc5f-4df4-a8ed-0047447ee8d8.png)

São oferecidas diversas opções de resolução:
-	Usar remoto – sobrescreve o arquivo local pelo remoto
-	Manter local – sobrescreve o arquivo remoto pelo local na próxima sincronização
-	Mesclar – exibe os dois arquivos e oferece um terceiro com uma sugestão de mesclagem, que pode ser editado. Ao clicar no botão «Aceitar Mesclagem», o arquivo estará disponível para novo envio. Ao clicar em «Confirmar mesclagem», os arquivos alterados são preparados para commit.

Se o resultado da resolução desse conflito resultar em novo conflito para outros colaboradores, esses serão submetidos ao mesmo processo na próxima sincronização que fizerem. Isso pode se estender até que haja um acordo entre os colaboradores sobre a versão definitiva.

#Branches
O trabalho com branches é o mesmo que se faz com linha de comando do Git, porém com interface gráfica no Visual Studio, no painel «Team explorer» - «Branches».

![Branch.png](/.attachments/Branch-82ee63cf-39dc-4e1e-9e7d-fd01132739fe.png)

Ao clicar sobre «Nova ramificação», digita-se o nome da branch, sem espaços ou símbolos, e seleciona-se a branch existente que será usada para a ramificação.
Observe-se que nova branch não está automaticamente no repositório remoto, pois deve ser enviada por push após a alteração de qualquer artefato.
Terminado o serviço na branch, ela pode ser mesclada com outra branch. Após a mesclagem, a branch pode ser excluída.

##Cherry-pick
Recurso para selecionar changesets que irão ser promovidos para uma branch. 
Resumidamente, os passos são esses:
-	Fazer checkout no branch destino
-	Entrar no histórico do branch origem
-	Clicar com o botão direito em cada um dos itens desejados e selecionar «Cherry-pick»
-	Sincronizar o branch destino

#Itens de trabalho – workitens
>Recurso descontinuado na versão 2019 do Visual Studio.

No painel «Team explorer» - «Itens de trabalho», há ligações com os workitens cadastrados no Azure DevOps, onde devem ser gerenciados.

![Workitens.png](/.attachments/Workitens-7e398102-ba11-4941-a620-18bc1df5e471.png)

Ao clicar em qualquer tipo de workitem em «Novo Item de Trabalho», o Visual Studio remete o controle para o navegador padrão, na página de criação de itens de trabalho.
Havendo queries cadastradas, os workitens podem ser consultados diretamente no Visual Studio, útil para fazer a ligação entre o item e as alterações.

![LinkWorkitem.png](/.attachments/LinkWorkitem-6428da63-9d20-41ab-96ad-8d50195273bb.png)

#Tag
Tag (marca ou label) é uma marca feita em uma branch, usualmente a master, com a finalidade de poder identificar uma versão (release) da aplicação ou para retomar esse ponto do desenvolvimento se for necessário, como, por exemplo, um hotfix após um deploy em produção, ou simplesmente para melhorar o entendimento do histórico do código.

Gerenciados pelo painel «Team explorer» - «Marcas» (labels).
 
![label.png](/.attachments/label-90f96d68-77ca-40dd-a297-018b7c5e1ed8.png)

## Envio de tags a repositórios remotos
Conforme a [documentação do Git](https://git-scm.com/book/en/v2/Git-Basics-Tagging), por padrão, o comando git push não envia tags para os servidores remotos.
Se houver muitas tags que se deseje enviar a um repositório remoto de uma só vez, utiliza-se o comando:
`$ git push origin --tags`

#Falhas conhecidas

##Terminal prompt disabled
Se o Visual Studio acusar erro e não solicitar a senha e mostrar a mensagem “...terminal prompt disabled”, a clonagem do repositório terá que ser feita pelo Git Bash:
-	No Windows Explorer, crie uma pasta onde será colocado o repositório
-	Com o botão direito dentro da pasta criada no Windows Explorer, abra o Git Bash
-	Digite o comando para criar uma repositório Git:
`Git init`
-	Digite um comando para o Git solicitar a senha somente uma vez:
`git config credential.helper store`
-	Faça a clonagem por linha de comando:
`git clone «nome_repositorio»`
O git deve exibir a tela solicitando a senha (token). Depois disso, o Visual Studio não deverá apresentar mais esse problema.

