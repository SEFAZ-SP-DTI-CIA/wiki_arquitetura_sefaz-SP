<u>15/10/2019</u>

Enviamos a seguir as orientações para ajuste das configurações de build/ deploy automático do Azure DevOps Server 2019/  Team Foundation Server :


- **Atualização de pools – Build:** Com a migração foi realizada também a unificação dos pools de build, visando a diminuir o tempo de espera para a realização dos builds. Desta forma, os pools que estavam separados por versão do Visual Studio (2010, 2012, 2013 e 2015) foram unificados em um único, chamado apenas de “Visual Studio”, que contém todas as versões do Visual Studio e frameworks necessários para a compilação das aplicações. Assim, é necessário que seja alterado, em todos os pipelines, o Agent Pool para “Visual Studio”, conforme abaixo:

  ![20200708_imagem_01.jpg](/.attachments/20200708_imagem_01-b3c30280-c9c4-47fa-88df-c34d051f4e19.jpg)

- **Atualização de pools – Release:** Alguns pipelines estavam utilizando o pool “Release”. Este pool será descontinuado, sendo necessário alterar os pipelines para a utilização dos pools conforme o ambiente em que está sendo realizado o deploy (Release DEV, Release HML, ou Release PRD), conforme figura a seguir:    

   ![20200708_imagem_02.jpg](/.attachments/20200708_imagem_02-93f39931-74f2-47cc-b080-f0f92016449d.jpg)

- **Task Windows machine file copy - Update da Versão :** Caso o pipeline esteja utilizando a versão 1.* da tarefa é necessário atualiza-la para a versão 2.* :

  - Copie o valor do parâmetro “Machines” (no exemplo $(servidores)), pois este valor não será populado automaticamente ao alterar a versão:

  ![20200708_imagem_03.png](/.attachments/20200708_imagem_03-f9d8ceb0-9ecb-421a-a0f0-56ebd1a02432.png)

  - Altere o combo “Task version” para 2.*

  - Cole o valor no campo “Machines”:

   ![20200708_imagem_04.png](/.attachments/20200708_imagem_04-14b5a183-f864-462d-bded-beb26a1f90ff.png)

  - Task WinRM – IIS App Deployment – Desabilitação de _deploy_ em paralelo: Existe um problema nesta task fazendo com que esta falhe ao tentar executar o deploy em paralelo. Para contornar este problema é necessário desabilitar a opção “Deploy in parallel”, em “Advanced”, conforme figura a seguir:

   ![20200708_imagem_05.png](/.attachments/20200708_imagem_05-759922b7-4dfb-4997-b47f-268696648607.png)



Caso sejam necessários esclarecimentos adicionais, entre em contato através do e-mail dti_cia_nsd@fazenda.sp.gov.br. 
