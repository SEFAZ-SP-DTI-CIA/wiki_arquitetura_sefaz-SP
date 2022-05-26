## Problema com driver de leitora de cartão
Após a instalação do Windows 10 em máquinas DELL Optiplex 9020, foi detectado problema com a leitora de cartão que vem embutida no teclado. Este problema acontece somente com algumas pessoas e não constava no procedimento do DTI-Windows. Para resolver, foi necessário reverter o driver da leitora de cartão, para a versão da Microsoft (caso esteja instalado o driver da DELL).

## Problema em certificados locais
Após instalar versões mais novas do Windows (como Windows Server 2012 ou Windows 10, ou o KB 2795828), o HTTPS para de funcionar se houver um ou mais certificados "não autoassinados" na pasta de certificados "Autoridades de Certificação Raiz Confiáveis" da máquina local.
Para resolver o problema, é necessário mover esses certificados não-autoassinados da pasta "raiz" para a pasta "CA". Isso pode ser feito manualmente no Console MMC ou, mais facilmente, pelo PowerShell (seguindo os passos abaixo):

## Resolvendo o problema via Powershell
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
    Move-Item -Destination Cert:\LocalMachine\CA​
```

4) Rodar novamente o comando do item "2 )", se houver ainda certificados, será necessário mover manualmente

Referência: https://stackoverflow.com/questions/26247462/http-error-403-16-client-certificate-trust-issue​