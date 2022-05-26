Após a criação de uma release, ela poderá ser gerenciada e acompanhada na página "View Releases", que pode ser acessada pelo menu vertical esquerdo em "Pipelines" - "Releases".
Selecionado um pipeline, exibem-se todas as suas releases criadas.

![View_Releases.png](/.attachments/View_Releases-5842033a-db01-4396-8a26-9fe9e5555bc4.png =700x)

#Acompanhamento
Ao clicar sobre o nome de uma das releases, abre-se o página de acompanhamento da release.
![Release_Gerenciamento.png](/.attachments/Release_Gerenciamento-b0f3fdb7-46b8-4640-ad67-90e8d340d70d.png =800x)

Os stages que já tiveram seus deploys executados aparecem com borda em cor verde e sinalizado com "Succeeded".

Os stages configurados para execução manual ficam com borda em cor cinza e com o texto "Not deployed".
Ao passar o mouse sobre esse stage, aparece um botão para execução de seu deploy.

Ao clicar sobre um stage que já teve um deploy executado, a página mostra as informações de resultado da execução em cada agent job.
![Release_Agent.png](/.attachments/Release_Agent-d8f66529-3960-4eed-8718-985f520d3ce0.png =400x)

Clicando-se dentro do círculo, a página mostra os servidores e as tasks.
![Release_servidores.png](/.attachments/Release_servidores-17e9020f-ebf7-4464-abbf-4dbfe76e28f9.png =400x)

Ao selecionar uma das tasks, exibem-se os detalhes (log) da sua execução.
![Release_log.png](/.attachments/Release_log-b8bd3967-8aa2-4f56-990d-c3975a51e749.png =400x)

#Roll back
Em havendo falha de execução que tenha prejudicado a disponibilidade da aplicação ou se houver a necessidade de reverter a aplicação para uma release anterior, na tela de "View Releases" seleciona-se a versão desejada para um novo deploy.
![Redeploy.png](/.attachments/Redeploy-79d6eeb0-ac97-402e-b8d8-97ddff05922e.png =600x)

Passando o cursor do mouse sobre o stage que se deseja refazer o deploy, clica-se sobre o botão "Redeploy".
Essa operação irá utilizar a versão de artefatos indicada na tela.

#Hotfix
Há situações em que se descobre alguma falha na aplicação em produção que necessitou de uma correção apartada, urgente e temporária, até que uma versão definitiva fique pronta para implantação. Esse tipo de correção é denominado de "Hotfix".

Em virtude da rapidez que um deploy automatizado oferece, a Sefaz orienta que o hotfix passe por todo o pipeline em nova release, com deploy em desenvolvimento e homologação antes de chegar à produção, com as devidas RDMs automatizadas criadas.
Com isso, a solução ou não do incidente ficará devidamente documentada no próprio pipeline, podendo ser útil em eventuais investigações futuras.

Como o hotfix pode prejudicar o trabalho da equipe de testes e dos homologadores, que se utilizam dos ambientes de QA e HML, após essa correção em produção, pode ser feito o redeploy da versão que estava sendo testada ou homologada.
