O Git Bash é uma ferramenta baseada em linha de comando que, apesar de ser muito mais complicado, o seu entendimento é útil para detalhar as capacidades do git, além de ser igual em quaisquer sistemas operacionais.

Após a sua instalação, observa-se a criação de um atalho para a aplicação Git Bash na área de trabalho ou no menu iniciar. As instruções do git passam a funcionar também no prompt de comando (cmd) ou PowerShell. 
No Windows Explorer, ao se clicar com o botão direito do mouse em alguma pasta, aparecem duas novas opções: Git Bash e Git GUI. Não vamos explicar o Git GUI nesse texto, mas seu uso se torna simples após a aprendizagem dos comandos.

Ao abrir o Git Bash, a execução do comando git, sem argumentos, exibe seus comandos mais comuns, para que não precise memorizá-los.

```
$  git
usage: git [--version] [--help] [-C <path>] [-c name=value] [--exec-path[=<path>]] [--html-path] [--man-path] [--info-path] [-p | --paginate | --no-pager] [--no-replace-objects] [--bare] [--git-dir=<path>] [--work-tree=<path>] [--namespace=<name>] <command> [<args>]
These are common Git commands used in various situations:
start a working area (see also: git help tutorial)
   clone      Clone a repository into a new directory
   init       Create an empty Git repository or reinitialize an existing one
work on the current change (see also: git help everyday)
   add        Add file contents to the index
   mv         Move or rename a file, a directory, or a symlink
   reset      Reset current HEAD to the specified state
   rm         Remove files from the working tree and from the index
…
'git help -a' and 'git help -g' list available subcommands and some concept guides. See 'git help <command>' or 'git help <concept>' to read about a specific subcommand or concept.
```
[[_TOC_]]

#Repositório local
Os arquivos que serão controlados pelo Git são copiados em uma pasta do computador marcada para ser um repositório. Para indicar o repositório, utilize o prompt do cmd ou do git bash, acesse a pasta desejada e digite `Git init` para um repositório vazio ou `Git Clone [url]` para copiar um projeto de um endereço web (hub).

Ao iniciar um repositório, o git cria uma estrutura de pastas ocultas sob ela:
A pasta .git fica oculta, por padrão, pois não deve ser manipulada diretamente, sob risco de prejudicar o controle do git. A pasta do repositório exibe a situação do projeto em determinado ponto, que é redesenhada quando há determinados tipos de alterações.

##Init
O comando «git init» inicia um repositório local vazio.

Ao abrir o Git Bash, navegue até uma pasta que se deseja que seja um repositório local, por exemplo:

```
cd /d
mkdir git
cd git
git init
  Initialized empty Git repository in D:/git/.git/
```

##Clone
O comando `git clone` inicia um repositório local a partir da cópia de um repositório compartilhado na rede local ou na internet, em algum site de hospedagem.

No Git Bash, navegue até uma pasta que se deseja que seja o repositório local, por exemplo:
`cd /d/git`
o comando precisa do endereço do repositório a ser copiado:
`git clone «nome_repositorio»`

##Config
O GIT mantém um arquivo de configurações globais de nome “.gitconfig”, no computador do usuário, que contém as variáveis que são usadas em todos os projetos, por padrão, usualmente na pasta do profile (%userprofile%). 

Configurações específicas de um projeto são gravadas na pasta “.git” do próprio repositório e são visíveis por todos que tiverem acesso a essa pasta.

As alterações feitas em seu código serão registradas com o nome e email de seu autor. Essas informações podem ser cadastradas no repositório:

```
git config user.name "Nome"
git config user.email "usuario@dominio"
```

Ou então cadastradas no git do usuário, que as usará para todos os repositórios em que não estiverem definidas:

```
git config --global user.name "Nome"
git config --global user.email "usuario@dominio"
```

Para ver os valores registrados no GIT para seu computador:

```
git config -–list
git config --global --list
```

Para poder verificar e alterar os valores, use a cláusula «–edit»:

```
git config --edit
git config --global –-edit
```
 
##Controlando alterações (changes)
O GIT mantém o controle de tudo o que houver no repositório através de listas. Ele mantém uma lista dos arquivos que ele não controla alterações, chamado de “Untracked files”. Nessa lista constam os nomes arquivos novos do repositório.
Ele mantém também uma lista de arquivos modificados (changes) dividida em mudanças prontas (to be commit) e incompletas (not staged for commit).

##Add
As alterações podem ser finalizadas ou desfeitas. 
Para indicar que as alterações estão prontas para serem usadas, utiliza-se o comando `git add`, que adiciona o nome do arquivo de “untracked” ou de “not staged” na lista “changes to be commited”.
`git add «nome do arquivo»`

Um arquivo novo assume o estado de “new file” e um arquivo controlado e alterado assume o estado de “modified”, nos dois casos irão para “to be commited”. 
Se o arquivo com estado “new file” for alterado, ele muda seu estado para “modified” e vai para a lista de “not staged”, e terá que ser adicionado novamente a “to be commited”.

Para voltar de staged para modified, uiliza-se o comando «git reset HEAD»:
`git reset HEAD «nome do arquivo»`

Para desfazer as alterações, utiliza-se o comando «git checkout»:
`git checkout --«nome do arquivo»`

#Commit
Os arquivos em changes podem ser registrados em uma lista de modificações denominada changeset, identificada por um código gerado automaticamente, pelo comando `git commit`.
- Uma alteração só pode ser controlada e compartilhada após commit. 
- Dentre as diversas alterações feitas no mesmo elemento de um projeto, somente o estado final do texto será registrado no changeset.

Para fazer o commit somente de “changes to be commited” (modified e new file), utiliza-se a instrução:
`git commit –m “«texto explicativo qualquer»”`

Para fazer commit de todas as changes (all modified):
`git commit –am “«texto explicativo qualquer»”`

Os arquivos em untracked não serão “comitados”.
![image.png](/.attachments/image-80c964d4-02ae-4c4d-b657-231e6c64261e.png)
 
##Status
Denomina-se «HEAD» a representação do estado dos arquivos após o commit, que ficará igual até o próximo. 
Chamamos de «working directory» a representação do estado dos arquivos no repositório. 
Define-se «index» ou «staging area» a lista das diferenças entre o working directory e o HEAD. 

Para saber a situação do index, utiliza-se o comando «git status». Se não houver alterações:

```
git status
On branch master
nothing to commit, working tree clean
```

Havendo mudanças, o git status exibe untracked files, changes staged e unstaged.

```
git status
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)
        modified:   teste1.txt
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)
        modified:   teste2.txt
Untracked files:
  (use "git add <file>..." to include in what will be committed)
        teste3.txt
```

Nesse exemplo, teste1.txt está diferente da versão indicada por HEAD e pode ser comitado. O arquivo teste3.txt foi criado no working directory, mas não está registrado no projeto, ficando sinalizado como untracked file. Após adicioná-lo com `git add`, seu estado muda para “new file”:

```
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)
        modified:   teste1.txt
        new file:   teste3.txt
```

Se o teste3.txt for alterado antes do commit, ele retornará para unstage, porém com estado “modified”:

```
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)
        modified:   teste3.txt
```

##Log
Para verificar o histórico de alterações de um repositório, use o log:
`git log` 

Para uma visão mais simplificada: 
`git log –oneline`

Os dois últimos commits: 
`git log -2 git log -2 --oneline`

##Controlando versões
Uma tag é um marcador que pode ser criado para identificar um estado do repositório, uma “etiqueta”, uma palavra que identifica como os arquivos estavam após um commit. É uma gravação de HEAD em um registro.
`git tag “v1.0”`

Quando um conjunto de changesets pode ser promovido (deploy), diz-se que há uma release (versão), que pode ser sinalizada com a criação de uma tag. Não há diferença entre uma tag de release e qualquer outra, assim se recomenda que o nome da tag seja explicativa, se possível.

Para listar todas as tags existentes em um projeto:
`git tag`

Com o comando checkout, o repositório (HEAD) assume o estado em que estavam os arquivos no momento em que foi criada a tag:
`git checkout “v1.0”`

Para comparar duas tags:
`git diff v1.0 v2.0`

Para identificar quem fez as alterações:
`git blame index.html`

Para sair do blame, tecle «q».

---

#Dividindo o desenvolvimento
Quando se cria um projeto, o repositório em uso recebe o nome de “master”. 

Há situações em que se deseja desenvolver algum recurso separadamente, para não prejudicar a master. Para isso, há um recurso utilizado pelos versionadores de código chamado de “branch” (ramificação).

Uma branch se comporta como uma cópia do projeto, que começa igual e pode ser alterada sem prejudicar a master ou outras branches. Em algum ponto, pretende-se unir (merge) branches para chegar a uma nova versão do projeto.

##Branch
O comando git branch cria uma cópia do projeto a partir da HEAD e coloca um marcador para identificá-la.
`git branch «nome_da_branch»`

Para verificar as branches locais disponíveis, digite o comando sem o nome:

```
git branch 
branch1
* master
```

Para ver branches remotas, use o parâmetro –a ou –r. O asterisco indica a branch em uso, que é para onde aponta a HEAD.

##Checkout
Para mudar para a outra branch:
`git checkout «nome_da_branch»`

![Git_checkout.png](/.attachments/Git_checkout-7dd3350a-3f1c-4650-9683-2edc4163ea70.png) 	 
   
Para criar e mudar para a nova branch com um só comando:
`git checkout --b «nome_da_branch»`

Para apagar uma branch, após a sincronização:
`git checkout --d «nome_da_branch»`

##Merge
Para sincronizar duas branches, utiliza-se o comando merge. Por exemplo, para fazer o merge a partir da master:

```
git checkout master
git merge «nome_da_branch»
```
O git junta as alterações automaticamente se não houver mudanças na mesma linha de um arquivo. Nesse caso, o git faz anotações no arquivo nos pontos de conflito, o merge é interrompido e uma instrução de resolução do conflito é indicada na tela, orientando a abrir o arquivo, excluir as anotações, fazer add, commit e merge novamente.

##Stash
Armazena as alterações temporariamente, retornando o working directory para a versão anterior.
`git stash`

Neste momento, você pode facilmente mudar de branch e trabalhar em outra coisa; suas alterações estão armazenadas na sua pilha.  
Para ver as stashes que você guardou, você pode usar `git stash list`:

```
$ git stash list
stash@{0}: WIP on master: 049d078 added the index file
stash@{1}: WIP on master: c264051... Revert "added file_size"
stash@{2}: WIP on master: 21d80a5... added number to log
```

Nesse caso, duas stashes tinham sido feitas anteriormente, então você tem acesso a três trabalhos stashed diferentes. Você pode aplicar aquele que acabou de fazer o stash com o comando mostrado na saída de ajuda do comando stash original: `git stash apply`. 
Se você quer aplicar um dos stashes mais antigos, você pode especificá-lo, assim: `git stash apply stash@{2}`. 
Se você não especificar um stash, Git assume que é o stash mais recente e tenta aplicá-lo:

```
$ git stash apply
# On branch master
# Changes not staged for commit:
#   (use "git add <file>..." to update what will be committed)
#
#      modified:   index.html
#      modified:   lib/simplegit.rb
```

Havendo conflito, o git solicita a sua correção, como em merge.

##Rebase
O ponto em que foi criada uma branch é chamado de base da branch. Se houve alterações na base que se desejam buscar para a branch, redefinindo sua base, utiliza-se o comando rebase, em vez de merge.
`git rebase master «nome_da_branch»`

Uma boa explicação sobre rebase pode ser vista em https://www.treinaweb.com.br/blog/git-merge-e-git-rebase-quando-usa-los

##Tag
Tag é uma marca feita em uma branch, usualmente a master, com a finalidade de poder identificar uma versão (release) da aplicação ou para retomar esse ponto do desenvolvimento se for necessário, como, por exemplo, um hotfix após um deploy em produção, ou simplesmente para melhorar o entendimento do histórico do código.

Par criar uma tag, utiliza-se o comando:
`git tag -a <nome da tag> -m "<comentário>"`

Se já houve alterações na branch e se esqueceu de criar a tag após o deploy, há a possibilidade de se criar a tag indicando o commit que antecedeu esse deploy:
`git tag -a <nome da tag> -f <id do commit> -m "<comentário>"`

Não se pode fazer checkout em uma tag no Git, mas se pode colocar uma versão do repositório no working directory que se pareça com a tag. Cria-se uma branch sobre a tag com o commando:
`git checkout -b [branchname] [tagname]`

por exemplo:
`git checkout -b hotfix_v1 v2.0.0`
Supõe-se que essa branch, após seu devido uso, seja mantida separada da master ou excluída. 

## Envio de tags a repositórios remotos
Conforme a [documentação do Git](https://git-scm.com/book/en/v2/Git-Basics-Tagging), por padrão, o comando git push não envia tags para os servidores remotos.
Se houver muitas tags que se deseje enviar a um repositório remoto de uma só vez, utiliza-se o comando:
`$ git push origin --tags`

#Compartilhando o código
Para uma equipe de desenvolvimento trabalhar sobre o mesmo projeto, é necessário que ela estabeleçam um repositório que receberá seus códigos, para que possam ser compartilhados. 
Geralmente, escolhe-se um serviço de hospedagem, como o Azure ou GitHub, entre outros, ou algum servidor on premises. 

A Sefaz utiliza o [Azure Devops Server](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/ADS), ads, para seus [repositórios](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/ADS/Recursos-do-Azure-Devops/Criação-de-repositório-no-ads).

Através de envios para o servidor (push) ou busca de alterações (pull) nesse repositório remoto, todos os membros das equipes conseguem sincronizar seus trabalhos de forma bem controlada.
![image.png](/.attachments/image-7acdf5ea-72df-4791-8cc6-cba11f53f721.png)


##Remote
Adiciona um dado endereço (URL) de um repositório ao projeto, associando a uma variável, por exemplo “origin”. (o GIT é sensível à maiúsculas/minúsculas). 
`git remote add origin <url>`

Para verificar as suas conexões remotas digite: 
`git remote`

##Push
Atualiza um repositório remoto com as alterações de um repositório local.
Para enviar para a branch principal:
`git push origin master`

Para criar uma branch remota:
`git push -u origin [nome_branch]`
Cria uma branch remota [nome_branch] no repositório referente ao repositório associado à variável “origin” e uma ligação entre a branch local e a remota.

##Fetch
Compara o repositório em uso com um repositório remoto, indicando se há alterações disponíveis para atualizar, e cria no repositório local uma “tracking-branch”, que é um branch local com relação direta a uma branch remota.
`git fetch origin`

O comando fetch realiza apenas o download das diferenças, mas não exibe essas modificações no working directory, permitindo que se verifique o que foi mudado e avaliar as mudanças.
Para utilizar as mudanças, utiliza-se o comando `merge (remotes/origin/master)`.

##Pull
Incorpora alterações de um repositório remoto à branch em uso. Funciona como um fetch seguido de merge.
`git pull origin`

##Checkout
Com o comando `git checkout -t origin/design`, todo esse trabalho é feito automaticamente para a branch chamada design:
- Criação de uma branch local com o mesmo nome da branch remota
- Mudança para essa nova branch criada
- Criação de link entre a branch local e remota

##Pull request
Alguns hospedeiros de GIT, como o Azure DevOps e GitHub, oferecem um recurso de proteção de alteração da branch master, mas permitindo a criação de branches que podem ser analisadas, aprovadas ou rejeitadas antes de incorporá-la definitivamente ao produto. A configuração de pull requests se faz na aplicação do hospedeiro. Nada muda no GIT.

![image.png](/.attachments/image-fa7d87b4-e51e-494b-8beb-0956a6bc0c04.png)

Havendo a configuração de pull request no repositório remoto, após um envio, um usuário designado fará a verificação dos arquivos enviados, poderá discutir sobre as alterações, aprovar ou reprovar a requisição, que poderá impedir o merge dos artefatos de acordo com a política configurada de segurança.

Não havendo um pull request criado no projeto, qualquer envio será adicionado ao repositório.

Esta estratégia é especialmente útil em projetos de código aberto, onde não se conhecem os colaboradores, ou em terceirização de desenvolvimento, para oferecer uma forma alternativa de aceitação de código ou de controle de qualidade.


