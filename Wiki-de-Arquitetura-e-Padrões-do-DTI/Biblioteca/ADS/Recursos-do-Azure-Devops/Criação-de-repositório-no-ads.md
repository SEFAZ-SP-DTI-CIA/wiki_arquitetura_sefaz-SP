Repositórios, em desenvolvimento de sistemas, são locais para armazenamento de cópias de arquivos, tipicamente em bancos de dados.

[[_TOC_]]

#Gerenciar repositórios
Os repositórios de cada projeto são acessados pelo menu vertical direito do ads.
Ao clicar sobre o nome do repositório, exibem-se os demais repositórios, se houver:
![Repositorios.png](/.attachments/Repositorios-6cb99e15-bdd5-4e80-9819-1173d35cf2ae.png =500x)

##Criar um repositório
Para criar um novo repositório, clica-se em "+New Repository".
![Repositorio_Novo.png](/.attachments/Repositorio_Novo-0dafac4c-12a2-4751-b319-8c1e0c852290.png =300x)
> Desmarque "Add a README"

###Tipos de repositórios no ads
O ads oferece um sistema de repositórios com dois tipos de [controle de versões](https://pt.wikipedia.org/wiki/Sistema_de_controle_de_vers%C3%B5es):
- [TFVC](https://pt.wikiversity.org/wiki/TFVC_-_Team_Foundation_Version_Control_2016-2) - Team Foundation Version Control, era o versionador da Microsoft, que será descontinuado.
- [Git](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Desenvolvimento/Git) - sistema de controle de versão oficial da Sefaz, o mais utilizado no mundo e adotado pela própria Microsoft como seu versionador.

##Renomear ou excluir um repositório
Para renomear ou excluir um repositório, clica-se em "Manage Repositories"
![Repositorios_Manage.png](/.attachments/Repositorios_Manage-d4c4de44-69dc-44f3-b347-19cc789c2f5c.png =400x)

Se houver apenas um repositório, o botão "Delete repository" não será exibido. Nesse caso, um novo deve ser criado antes de excluir aquele.

---

#Enviar arquivos a um repositório Git
Após sua criação, o código já pode ser enviado por [linha de comando Git](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Desenvolvimento/Git/Git-por-linha-de-comando) ou pelo [Visual Studio](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Desenvolvimento/Git/Git-no-Visual-Studio) para o endereço indicado em "Clone".
![image.png](/.attachments/image-2733691d-3d83-4781-8a88-0571cea1d5df.png)

Um repositório Git pode receber arquivos por diversas formas:
- [Linha de comando](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Desenvolvimento/Git/Git-por-linha-de-comando) - através de consoles como o Git Bash, CMD ou PowerShell.
- [Visual Studio](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Desenvolvimento/Git/Git-no-Visual-Studio) - para versão 2017 ou superior.
- Upload de arquivos no próprio ads - limitado a arquivos com menos de 2 Mb individualmente.
![image.png](/.attachments/image-94d7cc7e-bd5a-4d05-9b12-284aa47a91e5.png =400x)

Nos dois primeiros casos, é necessário que se [prepare o computador para uso do Git](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Desenvolvimento/Git/Preparação-do-ambiente).

