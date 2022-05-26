# Configurações SQL Workbench/J

## Instalação
- https://www.sql-workbench.eu/downloads.html

- versões antigas: https://www.sql-workbench.eu/download-archive.html

- a versão 119 é a que usamos atualmente (Outubro/2021)
- necessário baixar o Java 8

## Conexão
1- Após instalar a ferramenta, abrir a tela de perfis de conexão: ALT + C -> Abrir 'Select Connection Profile'

2- Configurar conforme abaixo:

Driver: Sybase jConnect 6.0 (com.sybase.jdbc4.jdbc.SybDriver)

URL: jdbc:sybase:Tds:SRV15434:2640

| Username     |Password               |
|--------------|-----------------------|
|bpe_extrator  |bpe_extrator           |
|ccc_extrator  |ccc_extrator_5102      |
|Cte_extrator  |cte_extrator01         |
|mdfe_extrator |cargamdfedw            |
|nfce_extrator |nfce_extrator_5102     |
|nfe_extrator  |Nfe_Extrator_1409      |
