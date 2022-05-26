Para utilizar o GIT por linha de comando, basta instalar. Obtém-se seu instalador no site [Git for Windows](https://gitforwindows.org/)

Para utilização do GIT no Visual Studio, é necessário que a versão seja, pelo menos a 2017, na qual será preciso que se selecione o GIT na instalação como opcional. 
Na versão 2019, o GIT já é padrão e não precisa ser selecionado.

Os repositórios da Sefaz ficam no [ADS - Azure Devops Server](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/ADS). 
[[_TOC_]]

#Erros conhecidos
Principalmente na primeira utilização do Git, alguns erros já são bem conhecidos e suas soluções.

## Filename too long
Repositorios GIT com nomes muito longos podem apresentar problemas.
Pode ser necessário executar uma vez o comando:
```
git config --system core.longpaths true
```

##SSL
Por ser https, para seu acesso é necessário que se execute, uma vez apenas por máquina, a linha de comando:
`git config --global http.sslBackend schannel`
>Alternativamente, desative o verificador de ssl:
`git config --global http.sslVerify false`

##Proxy
Estando na intranet Sefaz, para acessar repositórios externos, por exemplo o https://dev.azure.com/, é necessário configurar o proxy para cada repositório por linha de comando no git bash, powershell ou cmd:
```
git config --global http.'[endereco_repositorio]'.proxy http://cs_git_servico:'git_cia'@10.216.28.101:8080
git config --global https.'[endereco_repositorio]'.proxy http://cs_git_servico:'git_cia'@10.216.28.101:443
```
Onde o [endereco_repositorio] é o link de clone do repositório externo.
Exemplo:
```
git config --global  http.'https://dev.azure.com/dti-cia-nsd/Aplicacao_Modelo/_git/wiki'.proxy http://cs_git_servico:'git_cia'@10.216.28.109:8080
git config --global https.'https://dev.azure.com/dti-cia-nsd/Aplicacao_Modelo/_git/wiki'.proxy http://cs_git_servico:'git_cia'@10.216.28.109:443
```
##Credenciais do Windows
O gerenciamento de credenciais pelo Windows costuma ser automática, porém, podem ocorrer falhas.

###Credential Helper
Algumas vezes, o acesso a repositórios remotos funciona usando o Git por linha de comando, mas no Visual Studio pode ocorrer o erro:
`Ensure the 'credential.useHttpPath' configuration value is set`

Isso pode ser resolvido no Visual Studio pelo menu Git -> Settings -> Git Global Settings, alterando a configuração do "Credential Helper" para "GCM Core":
![CredentialHelper.png](/.attachments/CredentialHelper-4775fbd0-a48b-4b14-88fa-c8ae69c8da65.png =600x)

###VPN
Para acesso aos repositórios do ADS através da VPN da Sefaz, crie uma credencial do windows. Para isso, acesse “Painel de Controle -> Contas de Usuário -> Gerenciador de Credenciais” e clique em “Adicionar uma Credencial do Windows”.
![CredencialWindowsGit.png](/.attachments/CredencialWindowsGit-8db29a02-e95a-48e7-8d36-aea78437b138.png)

