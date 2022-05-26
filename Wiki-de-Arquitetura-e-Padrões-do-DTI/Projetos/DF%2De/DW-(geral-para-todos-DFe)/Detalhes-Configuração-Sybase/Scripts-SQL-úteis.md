### Linha de comando para rodar scripts Sybase com múltiplos selects gerando saída em modo texto:

`>[SQL-Workbench]\sqlwbconsole.cmd -profile='[profile]' -script='[script SQL]' -displayResult=true > '[arquivo saida]'`

Onde: 

```
[SQL-Workbench] : é o diretório onde fica o SQL-Workbench

[profile] : é o nome do perfil no SQL-Workbench, por exemplo: NFe_Dev (isso é criado por cada usuário na interface gráfica)

[script SQL] : é o path completo do script a ser executado (normalmente com vários selects), ex: D:\Scripts\script.sql

[arquivo saída] : é o path completo do arquivo onde ficará a saída do script, ex: D:\TMP\saida.txt
```


Exemplo de linha de comando:
 `D:\SQL-Workbench> .\sqlwbconsole.cmd -profile='NFe_Dev' -script='D:\SQL_Scripts\Sybase_Workbench_script_CTe_Reextracao.sql' -displayResult=true > D:\TMP\teste_CTe_rextrator.txt`

### Backup scripts fsnagamine:

[SQL_Scripts.zip](/.attachments/SQL_Scripts-d1c8ec8f-efd4-4ccb-a364-c675cf629db5.zip)