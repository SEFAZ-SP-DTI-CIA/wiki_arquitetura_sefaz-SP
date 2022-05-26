## Problemas de contribuintes
[Página sobre problemas de contribuintes​](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/318/Problemas-De-Contribuintes)

## Problemas de banco de dados
-- Banco de dados em modo "SUSPECT"; rodar os comando abaixo:

```
ALTER DATABASE <Nome do banco> SET EMERGENCY
ALTER DATABASE <Nome do banco> SET ONLINE
ALTER DATABASE <Nome do banco> SET SINGLE_USER
DBCC CHECKDB(<Nome do banco>,REPAIR_ALLOW_DATA_LOSS)
ALTER DATABASE <Nome do banco> SET MULTI_USER
```

## Problemas no Visual Studio

-- **Erro 403 atualizando WebReference:** 
  O modo mais rápido de corrigir é mudando a referência para localhost e tirando a exigência de certificado no IIS Manager (importante: lembrar de voltar a configuração após realizar o Update do WebReference):

![IISmanager_SSL.png](/.attachments/IISmanager_SSL-3066955f-969d-4d7f-8abf-a74858c048a4.png)

## Problemas em certificados locais
Após instalar versões mais novas do Windows (como Windows Server 2012 ou Windows 10, ou o KB 2795828), o HTTPS para de funcionar se houver um ou mais certificados "não autoassinados" na pasta de certificados "Autoridades de Certificação Raiz Confiáveis" da máquina local.
Para resolver o problema, é necessário mover esses certificados não-autoassinados da pasta "raiz" para a pasta "CA". Isso pode ser feito manualmente no Console MMC ou, mais facilmente, pelo PowerShell (seguindo os passos abaixo):

Resolvendo o problema via Powershell
1) Abrir o prompt de comando como Admin, digitar "Powershell"
2) Digitar o script abaixo para listar os certificados ofensores:

```
Get-Childitem cert:\LocalMachine\root -Recurse | 
    Where-Object {$_.Issuer -ne $_.Subject}
```

3) Se houver ALGUM certificado na lista, digitar o script abaixo para mover os ofensores:

```
Get-Childitem cert:\LocalMachine\root -Recurse | 
    Where-Object {$_.Issuer -ne $_.Subject} | 
    Move-Item -Destination Cert:\LocalMachine\CA
```

4) Rodar novamente o comando do item "2 )", se houver ainda certificados, será necessário mover manualmente

Referência: https://stackoverflow.com/questions/26247462/http-error-403-16-client-certificate-trust-issue​


 
## Problemas na Instalação de Contadores de Performance
Em caso de problemas na instalação de contadores de performance, rodar num prompt de comando em modo administrador o seguinte comando:
**LODCTR /R**
Esse comando faz o rebuild das informações de contadores. 
 

## Problema quando não consegue fechar conexão SSL, mas somente TLS

Algumas vezes clientes tipo SOAP-UI que requisitam conexão SSL para fechar o HTTPS encontram problemas, o servidor simplesmente fecha a conexão TCP (vem um pacote TCP do host com bit RST ligado).

O caso é identificado pelo COI como "inconsistência na configuração de certificados do servidor", sendo que o servidor aparece como indisponível no BIG IP. A ação corretiva é "reconfiguração do binding via ferramenta `httpconfig` (interface gráfica da ferramenta `netsh http`)", pela qual o balanceamento do servidor no BIG IP volta a funcionar.

## Problema quando o SOAP-UI não funciona no 'localhost'

Se acessos ao 'localhost' não estiverem funcionando (por exemplo o assinador de notas), com o servidor não retornando nada, o problema pode ser a configuração de proxy do SOAP-UI. 

A primeira tentativa deve ser a de desabilitar o proxy do SOAP-UI, clicanco no ícone dele que fica na Tool Bar, ele tem um marcador que passa de 'verde' para 'vermelho'.

Se isso resolver, o ideal é ir em Preferences -> Proxy Settings:

 * Manual
 * Host: proxy-wscas.intra.fazenda.sp.gov.br​
 * Port: 8080
 * Excludes: <copiar dos settings do seu IE, substituindo ponto-e-vírgula por vírgula>


## Fazendo o SOAP-UI forçar protocolo TLS1.2 (ou algum outro)

Acrescentar no arquivo "SoapUI-5.2.1.vmoptions" (ou equivalente) a linha abaixo:

  -Dsoapui.https.protocols=TLSv1.2​ 

Esse arquivo fica na pasta "bin" do SOAP-UI.



## Assinador não funciona devido a SQL Server local estar desligado e não liga (erro 5)

1) Para verificar que é esse erro mesmo, precisa ver o log em `C:\Program Files\Microsoft SQL Server\MSSQL11.MSSQLSERVER\MSSQL\Log` tem o erro abaixo:

`UpdateUptimeRegKey: Operating system error 5(Access is denied.)​`

2) Caso positivo, a solução é mudar a conta do SQL Server, conforme link:

https://docs.microsoft.com/en-us/sql/database-engine/configure-windows/scm-services-change-the-service-startup-account?view=sql-server-2017​



## Problema do Serviço LCR dar erro quando pára de rodar em WindowsServer 2008 R2

O erro indica que CryptLibHelper.dll ou um de seus componentes não foi encontrado. No caso, é a lib _"msvcr120d.dll"_ que falta nesse ambiente.

Há duas soluções possíveis:

1) fazer o download da lib [msvcr120d.zip](/.attachments/msvcr120d-cc4896af-174b-4950-aba5-16301e2111d3.zip) (descompactar e renomear para .dll) e copiar para a pasta do serviço no servidor. Não funciona copiar a versão que temos na máquina de desenvolvimento local, pois precisa ser a versão 32bits.

2) compilar sem debug (a lib msvcr120.dll, que é a versão sem debug dessa lib, existe nos servidores)

### Porque acontece esse erro:

Segundo o site da Microsoft: "

**Debug** versions of an application are not **redistributable**, and **debug** versions of the Visual C++ library DLLs are not **redistributable**.
"

​(https://msdn.microsoft.com/en-us/library/aa985618.aspx)
desse modo, somente nas máquinas com SDK instalado temos a versão de debug dessa e outras libs. Como nos servidores apenas instalamos o redistributable, apenas as versões sem debug são instaladas.

A versão Redistributable pode ser encontrada em https://support.microsoft.com/pt-br/help/2977003/the-latest-supported-visual-c-downloads