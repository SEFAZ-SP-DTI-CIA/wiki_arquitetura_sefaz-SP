

### Query para saber se o MDF-e está recebendo dados do RS:


```
SELECT *

FROM [MDFeIntegra].[MDFe].[Configuracao] c

INNER JOIN [MDFeIntegra].[MDFe].[ConfiguracaoTipo] ct

on c.pkeyConfiguracaoTipo = ct.pkey

WHERE nome = 'ULTNSU'
```
