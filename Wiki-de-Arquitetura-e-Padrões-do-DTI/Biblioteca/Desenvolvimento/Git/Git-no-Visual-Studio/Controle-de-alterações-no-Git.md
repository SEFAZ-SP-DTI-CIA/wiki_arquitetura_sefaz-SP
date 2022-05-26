Toda alteração feita em um repositório local deve ser preparada e confirmada, processo chamado de [_Commit_](https://git-scm.com/docs/git-commit), para que possa ser controlada. 
Alterações não "comitadas" não deixam registro no histórico e podem ser perdidas. 

[[_TOC_]]

#Alterações
As alterações podem ser visualizadas no painel «Git Changes»:

![image.png](/.attachments/image-726ae931-2e0b-41ac-80e1-f7214d2ab7ce.png =250x)

O controle (versionamento) de alterações no Git, é feito em duas ações: preparar (+ Stage) e confirmar (Commit).

##Stage
Cada modificação pode ser preparada individualmente, ao se clicar com o botão direito do mouse sobre ela, ou se pode adicionar todas as alterações ao stage clicando no botão com um sinal de adição (+):

![Changes_stage.png](/.attachments/Changes_stage-f1fe116f-e502-4cca-ae98-4b615c6b52f4.png =300x)

As alterações preparadas não estão salvas, mas apenas sinalizadas de que deverão ser comitadas.

##Commit
Commit pode se referir a duas coisas, um objeto ou uma ação:
- Histórico - registro de um conjunto de alterações no histórico.
- Git Changes - gravação das alterações preparadas no repositório.

###Gravar um commit
Havendo alguma alteração em quaisquer arquivos controlados pelo Git no repositório, a modificação é exibida no painel "Git Changes".

![image.png](/.attachments/image-de8e10c3-90aa-4f45-864e-1a9611596f85.png =300x)

Se houver alguma alteração preparada, o painel exibirá o botão "Commit Staged", se não, exibirá "Commit All".
Ao clicar sobre esse botão, será criado um commit.

####Relacionar um workitem a uma alteração
Um workitem (WI) pode ser relacionado a uma alteração digitando-se seu número (id) precedido pelo símbolo de cerquilha, inserido na observação do commit.

Esse id pode ser obtido no ads, [Boards](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/ADS/Recursos-do-Azure-Devops/Boards). Procura-se o WI e se anota seu número.

No Visual Studio 2019, os workitens podem ser visualizados no painel "Team Explorer". Se ele não estiver visível, acesse o menu "View - Team Explorer".
Diferente das versões anteriores, esse painel é usado para relacionar o repositório ao projeto ads.

A primeira coisa a ser feita é se conectar a um projeto ads. 

![image.png](/.attachments/image-2d810c51-d8e3-44b2-80c4-8050cfd3bf8e.png =700x)

Com isso, no menu do Visual Studio aparece a opção Team, com operações para manutenção do projeto ads.

No painel "Team Explorer" aparece um botão "Work Itens", que exibe os WIs disponíveis.

![image.png](/.attachments/image-42de8a8a-50b3-4679-88f8-fd83281d8c14.png =300x)

> Ao clicar com o botão direito do mouse sobre ele, há uma opção "Relate To Changes", que acho que não funciona.

No painel "Git Changes", ao digitar uma cerquilha (#), os WIs aparecem para serem selecionados.

![image.png](/.attachments/image-d4944978-de6a-4891-9cbe-12250d1013d4.png =300x)

>Observe-se que no Git não há um vínculo direto entre o repositório local e os demais recursos do projeto remoto.
Esse relacionamento é feito pelo Visual Studio e pelo ads, não pelo Git. Por exemplo, o id de um WI em um projeto ads não fará sentido em outro repositório, como no Git Hub.

###Histórico de alterações
Para ver os commits gerados no repositório, exibe-se a guia "History" através do menu "Git - View Branch History".

![image.png](/.attachments/image-fabb7b38-a183-45e0-bda0-3ceb0a195ea2.png =750x)

##Desfazer alterações
Há alguns caminhos para reverter alguma alteração comitada, usando a guia "History".

![image.png](/.attachments/image-ea74688d-944e-44c5-9492-4b8d3788b7d0.png =600x)

Ao clicar com o botão direito do mouse sobre um commit, entre outras coisas, o Visual Studio oferece as opções:
- Revert - desfaz somente as alterações daquele commit, quando possível.
- Reset - apaga os commits posteriores, com duas possibilidades:
  - Keep Changes - mantém todas as alterações. Serve para "limpar" o histórico.
  - Delete Changes - reverte todas as alterações possíveis dos commits excluídos.

>Obs: alguns commits podem não ser possíveis de reversão. 
Por exemplo: um commit cria um arquivo e o seguinte o apaga. 
Se o primeiro for revertido, o segundo poderá não ter mais o arquivo para excluir.

##Tag
Um commit pode receber um rótulo (tag) que o identifique entre os diversos que vão sendo criados durante o processo de desenvolvimento.
A tag pode ser criada na guia "History", clicando-se com o botão direito do mouse sobre o commit desejado e clicando em "Create Tag":

![image.png](/.attachments/image-d69b873e-6f2d-4e94-80c0-d7a7e433fa8b.png =650x)

Essa tag pode identificar, por exemplo, a versão da aplicação, o início ou o final de alguma modificação, ou ter qualquer outro significado.

### Envio de tags a repositórios remotos
Conforme a [documentação do Git](https://git-scm.com/book/en/v2/Git-Basics-Tagging), por padrão, o comando git push não envia tags para os servidores remotos.
Se houver muitas tags que se deseje enviar a um repositório remoto de uma só vez, por algum console, utiliza-se o comando:
`$ git push origin --tags`

![image.png](/.attachments/image-c56ef2bb-f1a2-4e3d-a7dd-efa5d2604ab3.png =500x)

