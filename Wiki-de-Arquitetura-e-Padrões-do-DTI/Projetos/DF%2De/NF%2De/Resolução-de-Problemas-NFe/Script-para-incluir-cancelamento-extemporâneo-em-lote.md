Para incluir uma lista de NF-e para cancelamento extemporâneo, é necessário realizar as duas etapas abaixo:

1. Incluir os registros na tabela **[dbo].[ContribuinteNfeExtemporanea]** do servidor **srvcorp01**(banco **Credenciamento**)



A tabela possui os seguintes campos:

![tabela.png](/.attachments/tabela-e09aa8ce-4666-44d8-ad76-35dbeae7d115.png)


E um exemplo de script:


```
use credenciamento

go

INSERT INTO [dbo].[ContribuinteNfeExtemporanea] ([NfeExtemporanea],[Cnpj],[RazaoSocial],[IE],[DataInclusao],[LoginInclusao],[Gdoc]) VALUES ('35180147379565007017550100000000551713333558','47379565007017','PROXXI TECNOLOGIA LTDA.','149600049117','2020-12-02','lcbsoares','OFICIO CIRCULAR 03/2020')

GO
```


 

2. Incluir os registros na base da NF-e através da procedure **[NFeEventos].[usp_CancExtemporaneoInsert]** do servidor **srvnfe01**(banco **NFe_Out**)

 

Exemplo de script:


```
USE [NFe_Out]

GO

DECLARE @idNFe varchar(44)

EXECUTE [NFeEventos].[usp_CancExtemporaneoInsert] @idNFe='35180147379565007017550100000000551713333558'

GO
```


