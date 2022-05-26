O Visual Studio 2019 oferece duas visões de ferramentas para uso do Git:
. [Team Explorer](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Desenvolvimento/Git/Git-no-Visual-Studio/Git-com-Team-Explorer) - o painel antigo de administração de repositórios, para versões mais antigas do Visual Studio ou para repositórios TFVC.
. [Git user experience](https://docs.microsoft.com/en-us/visualstudio/ide/git-with-visual-studio?view=vs-2019) - o novo painel exclusivo para repositórios Git, para a versão 2019 ou posterior do Visual Studio.

A gestão de alterações de arquivos, também chamada de versionamento, é um processo que envolve:
- manutenção de cópias de arquivos modificados, para quaisquer propósitos, como verificação de históricos ou reversão de alterações.
- ramificação, ou duplicação, dos arquivos para possibilitar o desenvolvimento de funcionalidades separadamente.
- trabalho em equipe, com o controle do trabalho de diversas pessoas sobre os mesmos arquivos.

Os recursos de controle de versão podem ser agrupados em:
- [Criação de repositório](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Desenvolvimento/Git/Git-no-Visual-Studio/Criação-de-repositório-Git) - conversão de uma pasta local em repositório ou a replicação (clone) de um repositório remoto.
- [Controle de alterações](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Desenvolvimento/Git/Git-no-Visual-Studio/Controle-de-alterações-no-Git) - feito por uma pessoa em sua estação de trabalho (computador local).
- [Divisão do trabalho](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Desenvolvimento/Git/Git-no-Visual-Studio/Branch) - replicação dos arquivos de um repositório em ramificações (branch).
- [Compartilhamento de código](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Desenvolvimento/Git/Git-no-Visual-Studio/Compartilhamento-de-repositórios-Git) - feito por diversos colaboradores em um computador de acesso comum (hub).

>Veja a documentação da Microsoft em:
https://docs.microsoft.com/en-us/visualstudio/version-control/?view=vs-2022
---

###Desabilitar o Git experience
Para habilitar ou desabilitar o modo Git experience, no Visual Studio 2019, acesse o menu «Tools» «Options» «Environment» «Preview Features» e clique na opção «New Git user experience».
Essa possibilidade não existe mais na versão 2022.

