De acordo com os [padrões de desenvolvimento estabelecidos pelo DTI](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Desenvolvimento/Padrões-de-desenvolvimento), não poderá haver registro de senha em strings de conexão com bancos de dados SQL Server no código-fonte das aplicações ou serviço hospedados em servidores Windows. 

Foi desenvolvido um documento de [Procedimentos para utilização do Windows Authentication em acesso a bancos de dados](http://etc.intra.fazenda.sp.gov.br/sites/suporte_desenv/Documentos%20Compartilhados/Guias%20e%20Manuais/Windows_Authentication_para_acesso_BD.pdf?Web=1) com instruções detalhadas da solução.

> Não há ainda uma solução estabelecida para outros tipos de bancos de dados e outras plataformas. Contribua.

Serão adicionados _enablers_ no backlog dos projetos que estiverem em desacordo com essa exigência para a sua devida adequação.

Os procedimentos para essa modificação inclui a criação de uma Liberação e uma RDM Normal com:
1. Task para criação de uma [Conta de Serviço Gerenciada - gMSA](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Segurança-da-Informação/Usuários-em-aplicações), com a identificação do:
    - Nome da conta
    - UA da equipe responsável pela aplicação
    - Servidores onde será hospedada a aplicação
>Para a utilização desse tipo de conta gerenciada, o servidor poderá ser reiniciado.
Verificar, nessa tarefa, a necessidade de reinicialização do servidor.

2. Task para criação do banco de dados, se necessária, com inclusão do formulário requerido
3. Task para configuração do banco de dados para usar essa conta de serviço com:
    - Nome da conta gerenciada criada na tarefa 1
    - Nome da conta anterior do banco de dados a ser substituído
    - Instância
    - Base de dados
4. Task de configuração da aplicação:
    - se for um Windows Service, dos servidores que hospedam a aplicação para usar a conta gMSA
      - conceder o direito de “Log On as Service”
    - se for uma aplicação web, da Application Pool para uso da conta criada:
      - Nome da app pool
      - Identity: Custom Account
      - Username: intra\\[conta de serviço]
      - Password: [deixar em branco, para conta gMSA]

5. Task para a equipe de desenvolvimento alterar a string de conexão com o banco de dados para utilizar "Windows authentication".

Após algum tempo, a equipe deve planejar e solicitar a exclusão da conta usada anteriormente, se não estiver mais em uso por nenhuma aplicação.

>Veja um exemplo de [aplicação dotnet core MVC em três camadas](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_releaseDefinition?definitionId=3&_a=environments-editor-preview), em que a conexão com o banco de dados é feita por uma api nessa especificação.


