No desenvolvimento de aplicações, é um problema comum a concessão de autorização de acesso a determinados tipos de recursos. A pergunta inicial é: quem está tentando utilizar o recurso?
Certamente se trata de algum ser humano, diretamente ou não, mas do ponto de vista da aplicação, o que se conhece é o chamado **usuário** (login ou conta), que é uma cadeia de caracteres, um "nome" que representa o solicitante do acesso. 

O primeiro ponto a se verificar é se o usuário é autêntico, se esse nome representa de fato que ele parece ser ou se é alguma tentativa de fraude. Para isso, foram elaboradas diversos tipos de configuração de usuários, que podem utilizar senhas, cartões, impressões digitais ou quaisquer outras formas que ajudem a garantir que aquele login representa de fato o usuário.
Na forma mais comum e simples, cria-se uma tabela em um banco de dados contendo os nomes de usuários (login) e senha. O controle de segurança da senha fica a cargo das pessoas que se utilizam desse login.

O sistema operacional Windows Server oferece um serviço denominado _Active Directory Domain Services_ (AD) que oferece alternativas de armazenamento de identidades, utilizado como padrão pela Sefaz-SP para autenticação de contas de serviços.

#Ambiente de utilização
Por [padrão estabelecido na Sefaz](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Desenvolvimento/Padrões-de-desenvolvimento), as aplicações devem usar usuários diferentes para execução em cada ambiente.

#Tipos de contas
O acesso a recursos como um banco de dados pode ser feito por usuários cadastrados em aplicações ou por contas de serviço do AD.

##Usuário de aplicação
Tipo não recomendado pela Sefaz, é um login próprio de uma aplicação para ser utilizado como identificador em acessos a outras aplicações ou serviços. 
Por costume, uma conta de aplicação começa com um nome simples da aplicação seguido de "_app".
Exemplo: sitecia_app

##Conta de serviço
Usuário criado para ser configurado em serviços para que esses possam acessar outros serviços. A utilização desse usuário exige a informação de sua senha.
Principais características:
- Utiliza o mesmo tipo de objeto de usuário (mesma classe usada para contas de usuários finais).
- Deve ser criada sempre no Active Directory.
- Permite configurar algumas restrições como "Logon On To" (limita os computadores que essa conta poderá ser utilizada). Essa configuração é manual e em alguns cenários não é suportado, como alguns appliances.
- Se a conta não possui privilégios de administrador (recomendado), ela requer configurações e permissões adicionais, dependendo da aplicação.
- Para autenticação, requer o login e a senha.
- Permite acessar computadores de forma interativa (Logon ou Remote Desktop).

Por padrão, uma conta de serviço começa com "CS_" e termina com a sigla de um ambiente, como "_DEV". 
Exemplo: `cs_sitecia_dev`

##Conta de Serviço Gerenciada - gMSA - [Group Managed Service Accounts](https://docs.microsoft.com/pt-br/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)
Conta de serviço que possui senha, mas é de conhecimento apenas do servidor com a aplicação e do Active Directory. 
Esse usuário deve ser configurado nos servidores em que serão executados a aplicação, concedendo-se o direito de “Log On as Service” e outras permissões necessárias para a conta gerenciar esse serviço (ex: acesso de escrita em uma pasta).

Principais características:
- Possui um tipo de objeto próprio.
- É necessário informar quais servidores poderão ter acesso a senha.
- A troca da senha é automaticamente feita periodicamente (por padrão a cada 30 dias) e é totalmente transparente para o administrador, aplicação e servidor.
- Até a versão Windows Server 2019, o gMSA poderia ser configurado somente em: Application Pool, Scheduled Task, Windows Services ou em algum serviço que suporte o gMSA.
- Se a conta não possui privilégios de administrador (recomendado) requer configurações e permissões adicionais, dependendo da aplicação.
- Para autenticação requer apenas o login (o servidor fará a autenticação em um canal seguro com o AD).
- Não permite acessar de forma interativa (logon ou Remote Desktop).

O nome de uma conta gMSA:
- Começa com csg_ (Conta de Serviço Gerenciada)
- Contém uma palavra identificadora curta
- Termina com a identificação do ambiente (d, h, p)
- Não pode ultrapassar 15 caracteres
- deve conter somente letras minúsculas e símbolo de sublinhado

Exemplos: `csg_sitecia_dev$, csg_apiprojet_h$`

#Utilização de usuário para acesso a banco de dados
O acesso a um banco de dados pode ser feito diretamente pela aplicação com seu usuário, mais simples e inseguro, ou pode ser feita com a intermediação de algum serviço com uma conta de serviço.
Para o usuário ou conta de serviço utilizar um banco de dados, solicita-se ao DTI-DBA a configuração do acesso, através do Remedy.

>A Sefaz está em processo de substituição de usuários de aplicação por um [acesso seguro a bancos de dados por contas de serviço](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Governança/Acesso-seguro-a-banco-de-dados), por questões de segurança.

Contas de serviços autorizadas podem acessar o banco de dados através de serviços do Windows, como pelo IIS (application pool) ou por Windows Services. Na configuração do serviço, informa-se o usuário de serviço e senha, ou sem a senha se a conta for do tipo gMSA. Assim, a aplicação chama o serviço, que faz as chamadas ao banco de dados.

##Acesso por IIS Application Pool
Para configurar o acesso ao banco de dados de uma aplicação web que utiliza o IIS, na Application Pool seleciona-se:
- Identity - Custom Account
- Username - o nome da conta de serviço
- Password - a senha. Se a conta for gMSA, esse campo fica em branco.

Em um deploy automatizado, essa configuração pode ser feita no pipeline de release em uma task do tipo "IIS web app manage". 
A senha pode ser substituída por uma variável, que por sua vez pode ser digitada com criptografia, protegendo seu conteúdo. Nesse caso, a senha não precisa ser guardada em nenhum outro lugar. Se for necessário, solicita-se a redefinição da senha, por incidente no Remedy, para uma eventual redigitação.

>Veja um exemplo de configuração da AppPool em um pipeline de [deploy automatizado em...](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Aplicacao_Modelo/_release)







