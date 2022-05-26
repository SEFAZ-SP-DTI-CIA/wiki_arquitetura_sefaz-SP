# DICAS PARA INSTALAÇÃO e DEBUG do IIS
1) SETUP WebService:
No diretório onde vai ser hospedado o web-server, adicionar através do Windows Service -> Properties -> Security , dois usuários
 * <nome do servidor>\Users
 * <nome do servidor>\IIS_IUSRS
Users precisa somente das permissões "Read & execute", "List Folder Contents" e "Read".

IIS_IUSRS precisa de todas as permissões.

Configurar o SSL Settings para

 * Require SSL
 * Accept client certificate

2) Para definir o tamanho máximo do XML aceito pelo IIS:
[Tamanho máximo do XML aceito pelo IIS](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/309/Tamanho-m%C3%A1ximo-do-XML-aceito-pelo-IIS)

3) Para abrir um prompt do DOS com o usuário do IIS:
`psexec -i -u "nt authority\network service" cmd`

(o psexec deverá estar visível no PATH.. nos servidores web NFC-e e EPEC atualmente esse executável está instalado em D:\util)