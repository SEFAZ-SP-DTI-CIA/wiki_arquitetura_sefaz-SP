Obs: 
para criar o usuário "assinanfe" no no Sql Server Data Management, na database XmlSigned:
0) Primeiro crie o Banco de Dados "XmlSigned"
1) rodar query CREATE LOGIN assinanfe WITH PASSWORD = 'teste'
2) na pasta "Security/Users" (dentro do Banco de Dados XmlSigned), right-click, "New User..."
3) digitar "assinanfe" para "user name", "assinanfe" para "login name", e selecionar "db_owner" para "Default Schema"

### Tabela para assinar XML de teste
### 
 

```
USE [XmlSigned]
GO
/****** Object:  Table [dbo].[evento]    Script Date: 08/07/2013 17:30:40 ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[evento](
 [id] [bigint] IDENTITY(100000,1) NOT NULL,
 [body] [xml] NOT NULL,
 CONSTRAINT [PK_evento] PRIMARY KEY CLUSTERED
(
 [id] ASC
)WITH (PAD_INDEX  = OFF, STATISTICS_NORECOMPUTE  = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS  = ON, ALLOW_PAGE_LOCKS  = ON) ON [PRIMARY]
) ON [PRIMARY]
GO

USE [XmlSigned]
GO
/****** Object:  Table [dbo].[xml]    Script Date: 08/07/2013 17:31:18 ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[xml](
 [id] [bigint] IDENTITY(100000,1) NOT NULL,
 [body] [xml] NOT NULL,
 CONSTRAINT [PK_xml] PRIMARY KEY CLUSTERED
(
 [id] ASC
)WITH (PAD_INDEX  = OFF, STATISTICS_NORECOMPUTE  = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS  = ON, ALLOW_PAGE_LOCKS  = ON) ON [PRIMARY]
) ON [PRIMARY]
GO
```
 
## Observação:
A- Caso mesmo após criar o Login, continue dando falha '18456', o problema pode ser no "modo de login" da instância SQL Server, sendo necessário executar o procedimento abaixo:


```
No Microsoft SQL Server Management Studio, no object explorer:

Right click no servidor (localhost) e selecione Properties

Abra a aba Security 

Na seção Server authentication escolher a opção SQL Server and Windows Authentication mode 

Clicar OK

Reiniciar a instância SQL Services (right click no servidor e selecione Restart)
```

B- Caso tenha havido algum erro na criação do Usuário e seja necessário remover, pode dar falha de permissão na hora de rodar o "drop user". Nesse caso, rodar antes os seguintes comandos:

```
1. USE XmlSigned; GO;
SELECT s.name
FROM sys.schemas s
WHERE s.principal_id = USER_ID('assinanfe');
```

`2. ALTER AUTHORIZATION ON SCHEMA::<db_owner> TO dbo;`

 Onde <db_owner> no segundo comando deve ser o resultado retornado no primeiro comando (pode ser que seja diferente de 'db_owner', dependendo de qual o erro cometido no processo de criação do usuário)