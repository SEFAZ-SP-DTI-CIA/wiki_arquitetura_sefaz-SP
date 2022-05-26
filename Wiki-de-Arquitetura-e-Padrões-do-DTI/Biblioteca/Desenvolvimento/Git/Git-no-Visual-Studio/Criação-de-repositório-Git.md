Há dois tipos de repositórios Git:
- Local - uma pasta no mesmo computador em que o desenvolvedor faz seu trabalho.
- Remoto - um local de armazenamento em um servidor acessível por algum serviço.

O primeiro passo para começar a usar o Git é a criação de um repositório local.
O usuário trabalha sobre um repositório local, envia (push) e recebe (pull) alterações de um repositório remoto. Não dá para trabalhar diretamente sobre o repositório remoto, na maioria dos casos.

#Criação local de repositório Git
Um repositório local pode ser construído de duas formas: 
- Init - criação de um repositório novo local.
- Clone - replicação de um repositório existente remoto.

Nos dois casos, um repositório será associado a uma pasta local na máquina do usuário.

##Init
Para transformar uma pasta local em um novo repositório, abra a pasta  pelo Visual Studio.
O painel "Git Changes" oferece um botão para a criação.

![image.png](/.attachments/image-bf7f41c2-4960-4886-9b9a-81f11431747c.png =500x)

##Clone
A clonagem faz uma cópia de um repositório remoto na máquina local.
Para clonar um repositório Git com o Visual Studio, há diversos caminhos:
- ads - usando a [interface web do ads](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_git), no repositório desejado, clica-se no botão "Clone", depois em "IDE" e seleciona-se "Visual Studio".
![VS_Git_clone.png](/.attachments/VS_Git_clone-6a95b1a8-ad67-4fb0-a878-714028863cc3.png =400x)

- Visual Studio - há um botão "Clone Repository" no menu "Git", no Painel "Git Changes" e tela inicial do Visual Studio. 
Todos esses abrem uma janela solicitando o endereço do repositório e a indicação do local onde o código será gravado em sua máquina.
![image.png](/.attachments/image-7d6e4053-4d6e-44f9-8ef8-6710cb67b263.png =450x)

- Team Explorer - veja no [Git usando Team Explorer](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Desenvolvimento/Git/Git-no-Visual-Studio/Git-com-Team-Explorer).

###Terminal prompt disabled
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


