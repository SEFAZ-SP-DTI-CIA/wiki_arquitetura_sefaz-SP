## **IMPORTANTE:** O Log/Audit, nos dias de hoje (out/2021), depende da tecnologia do MSMQ para funcionar. Esta dependência é um problema para aplicação .NET Core, uma vez que MSMQ não é suportado no Core.


## SOLUÇÃO

### Longo Prazo
O time do Log/Audit vai encontrar uma alternativa para MSMQ e fazer as alterações necessárias.
Isto implicará em atualizar o Desenho de Solução e também em desenvolvimento. Portanto não será uma solução factível a curto ou médio prazo.


### Curto Prazo
A solução encontrada para curto prazo será o uso de uma Web Api intermediária, desenvolvida pelo time do Log Audit. 
A aplicação .NET Core fará uma chamada via API REST para esta nova camada, que por sua vez chamará o Log Audit.
Será fundamental que cada projeto tenha sua própria Web Api, a partir do código fornecido pelo time Log Audit, alterando apenas o certificado de aplicação usado e também a parte de autenticação da Web Api, usando suas próprias credenciais.

###Alteração necessária no Pipeline do ADS
Com isso passaremos a ter um projeto .NET Framework dentro da Solution que antes provavelmente tinha somente projetos .NET Core.
Isto poderá gerar erro na Pipeline do ADS, com o projeto .NET Framework não sendo compilado com sucesso com a Task de compilação .NET Core.
No DIPAM-A adotamos a seguinte solução:
- alteramos (via Config Manager do VS) a configuração Release, que é usada pela Pipeline, para não realizar o Build do projeto .NET Framework. Vide aqui:
![LogAudit-Core-0.png](/.attachments/LogAudit-Core-0-5baaf040-1c0e-48f3-b486-c952124b5634.png)
- criamos duas novas Tasks na Pipeline um para trazer os pacotes NuGet necessários e para fazer a compilação deste novo projeto
![LogAudit-Core-1.png](/.attachments/LogAudit-Core-1-936f0fa1-64dc-4ff6-9c6a-6f5bea659674.png)

