Erro identificado pelo projeto DIPAM.

Em 2019 o Mário iniciou a criação de um projeto de testes de unidade, com vistas a melhorar a qualidade do projeto. Porém na época foi selecionada a tecnologia do Microsoft Fakes para tal.

Contudo depois se verificou que o Fakes está apenas disponível na edição Ultimate do Visual Studio, que não está disponível para os colaboradores da CapGemini.

Com isso, pelo menos até que seja implementada uma alternativa de testes de unidade, vamos remover o projeto de UnitTest do Buid.

Estes aqui são os erros que são exibidos neste caso:

![Imagem_33.png](/.attachments/Imagem_33-717f46ce-bef0-46a6-8451-7bbde2c4edb4.png)

A solução temporária é deselecionar o projeto no Configuration Manager conforme mostramos aqui:

![Imagem_34.png](/.attachments/Imagem_34-543495f9-6cb3-49f3-b2e1-8f8579d1b018.png)

e desmarcar o projeto:

![Imagem_35.png](/.attachments/Imagem_35-b11d36b3-a9a6-414d-a18d-5b142f5ebada.png)