#Repositórios remotos
Para uma equipe poder trabalhar sobre uma mesma aplicação, seus membros precisam compartilhar as modificações de seus repositórios.
Isso é feito com a criação de um (ou mais) repositório remoto, hospedado em algum serviço web, em geral, como o [Git Hub](https://github.com/), [Azure Devops Services](https://azure.microsoft.com/pt-br/services/devops/), [Google Cloud](https://cloud.google.com/) ou [Amazon Web Services](https://aws.amazon.com/).

A Sefaz utiliza a versão on premises da Microsoft, chamada de [Azure Devops Server ads como servidor de repositórios](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/ADS/Recursos-do-Azure-Devops/Criação-de-repositório-no-ads) de suas aplicações.

Os recursos oferecidos pelo Visual Studio para utilização de repositórios remotos podem ser acessados em três locais:
- Menu "Git" 
- Painel "Git Changes" - exibido pelo menu "View - Git Changes".
- Guia (tab) "Git repository" - exigido pelo menu "Git - Manage Branches".

![image.png](/.attachments/image-1e10da30-653a-48da-9475-410a3653aa4d.png)

Todos esses oferecem os comandos para gravação de alterações nos repositórios local e remoto. 
Basicamente, há duas ações: 
-	**Pull** - Confirmações de entrada 
Ao clicar em «Fetch», exibem-se as alterações no repositório remoto que não estão gravadas no local.
Clicando em «Pull», as modificações no servidor remoto são aplicadas no repositório local. 

-	**Push** - Confirmações de saída 
Não havendo alterações remotas, ou não havendo conflitos após um pull, ou após resolução dos conflitos, havendo alterações confirmadas (commit), se pode enviá-las a um repositório remoto pelo comando «Push»

A opção «Sync» faz as duas operações, primeiro Pull e, se não houver problemas, o Push.

No painel "Git Changes", em seu menu de contexto, observe-se a opção "Push All Tags".
O Git não envia tags, por padrão, ao repositório. Assim, se desejando que o repositório receba as tags, elas devem ser enviadas à parte.

###Resolução de Conflitos
Quando dois colaboradores efetuam alterações em um mesmo arquivo, podem haver conflitos que não se resolvam sozinhos, como modificações em uma mesma linha.

![image.png](/.attachments/image-38181d4e-328e-4a00-806d-08ee89643ab6.png =600x)

Nesse caso, o Visual Studio, após tentar a sincronização, exibe uma mensagem solicitando a definição e confirmação das alterações que serão definitivamente gravadas no repositório remoto.

Para cada conflito exibido, ao dar um duplo-clique sobra cada um, são oferecidas opções de resolução para cada diferença no arquivo:
- Take incoming – sobrescreve o arquivo local pelo remoto
- Take Current – sobrescreve o arquivo remoto pelo local no próximo push

Ao clicar no botão «Accept Merge», o arquivo estará disponível para novo envio. 

![image.png](/.attachments/image-159a3ebd-2b52-423e-bbd3-10ad82ac306e.png =600x)

Com isso, o commit que gerou o conflito mais o commit do merge poderão ser enviados (push) ao repositório remoto.

![image.png](/.attachments/image-ab19c1a9-5201-4c8d-acd8-c22f776dc1d6.png =600x)

Se o resultado da resolução desse conflito resultar em novo conflito para outros colaboradores, esses serão submetidos ao mesmo processo na próxima sincronização que fizerem. 
Isso pode se estender até que haja um acordo entre os colaboradores sobre a versão definitiva.
