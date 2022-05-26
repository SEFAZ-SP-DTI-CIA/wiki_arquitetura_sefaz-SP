[voltar](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/70/Desenvolvimento-Seguro?anchor=convers%C3%A3o-do-.net-framework)

#Objetivo
Desativação das cifras vulneráveis e obsoletas as versões SSL 1.0,SSL 1.1,SSL 1.2 e TLS 1.0 e TLS 1.1, e recompilação das aplicações hospedadas em área segura (HTTPS) para o Framework 4.6.2 ou superior.

 
#Procedimento de recompilação – Framework 4.8
Antes de iniciar a recompilação deve-se validar com a equipe DTI_Windows se o framework a ser utilizado já está instalado no servidor de aplicação (Dev-Hml-Prod).

**Passo 1** - Abrir o projeto e clicar com o mouse direito sobre o nome do projeto. Com o menu disponibilizado escolher o item de menu “**Properties**” (Propriedades).
![image.png](/.attachments/image-ed29c61c-93b6-40f6-b44e-8245c22185b1.png)
 
**Passo 2** - Com a janela de “**Properties**” disponibilizada, escolher o Framework.
![image.png](/.attachments/image-c2fe975e-fa82-4f63-8c3b-ff32edb73693.png)
 
**Passo 3** - Após a escolha do Framework, aplicar o “**Clean**” para deletar os arquivos de compilação, “**DLLs**” e “**EXEs**” gerados no “**Build**” do framework anterior.
![image.png](/.attachments/image-994e06dd-89fa-4b7a-88f0-1353e6c85cd8.png)
 
**Passo 4** - Resultado do “**Clean**”
![image.png](/.attachments/image-8feac4e2-8686-4c85-a92b-1346181ff6d6.png)
 
**Passo 5** - Após o “**Clean**” fazer o “**Rebuild**” para recompilar a aplicação.
![image.png](/.attachments/image-833074b2-c35e-4b62-ba5f-513ee8dab96e.png)

**Passo 6** - Resultado do “**Rebuild**”
![image.png](/.attachments/image-5dfd4498-1b03-4891-8175-f3c97c5f7650.png)
 
**Passo 7** - Após a recompilação abrir o arquivo “**Web.Config**”. O Visual Studio automaticamente alterou a tag “**targetFramework**” para a nova versão.
![image.png](/.attachments/image-d8018d3a-e8c7-4a1a-9ea6-d1f230a29c97.png)
 
**Passo 8** - Gerar o pacote para “**Deploy**” da aplicação utilizando o recurso do “**Publish**” ou pela simples transferência do projeto para o servidor de aplicação.
![image.png](/.attachments/image-dd607515-d70d-458b-a98c-6d12917122f8.png) 

Atualização do Web.Config.
Normalmente o arquivo Web.config da aplicação que está no servidor de teste, homologação ou produção possui particularidades como a string de conexão com banco de dados, por exemplo. 
Editar o arquivo Web.config que está no servidor e alterar a Tag “**targetFramework**” com a versão do framework utilizado na recompilação conforme o “**Passo 7**”.

Utilização de protocolo em aplicações HTTP
Em aplicações hospedadas na área comum (HTTP) que acessam um WebService instalado em área segura foi necessário forçar o uso do protocolo de segurança, “**System.Net.SecurityProtocolType.Tls12**”, antes da execução do método do WebService.

```
RecebeMSG.RecebeMSG msgRef = new RecebeMSG.RecebeMSG();
msgRef.Url = System.Configuration.ConfigurationManager.AppSettings["PATH_WS_XML_SIAFEM"].ToString();

//força SSL/TLS 1.2, pois TLS 1.0 e 1.1 foram desativados por falta de segurança , pelo dti-windows em 10/2020

//Também foi necessária a troca do framework para 4.8
System.Net.ServicePointManager.SecurityProtocol = System.Net.SecurityProtocolType.Tls12;
System.Net.ServicePointManager.SecurityProtocol = (System.Net.SecurityProtocolType)3072;

string retornoXmlMensagem = msgRef.Mensagem(Usuario, Senha, AnoBase, UnidadeGestora, xmlRetorno);
return retornoXmlMensagem;
```

**Passo 9** - Solicitar a equipe de infraestrutura (WINWEB 3 nível) a desativação das cifras e protocolos obsoletos no servidor de aplicação Web, conforme imagem abaixo:

![image.png](/.attachments/image-b7252952-13f3-4830-b621-727e6b21fa0a.png)

<br>

#Erro01:

````
Após a desativação das cifras fracas, a tela abaixo é o resultado após a autenticação do usuário que é feita chamando o serviço em HTTPS, https://siafemhom.intra.fazenda.sp.gov.br/siafisico/RecebeMSG.asmx",.

A solução adotada foi implementar no WebConfig as diretivas abaixo, dentro da seção System.web:

   <system.web>
     <customErrors mode="Off"/>
     <compilation debug="true" targetFramework="4.8"/>
     <httpRuntime targetFramework="4.8"  maxRequestLength="1048576" executionTimeout="3600"/>
    ....
     ....
     ....
</system.web>

A diretiva “targetFramework=4.8”, dentro do “httpRuntime” é a responsável para que o próprio sistema operacional se encarregue de buscar o melhor TLS ativo.
````

#Erro02:
#Dificuldades com o SEFAZ.Identity após a Eliminação de Cifras Vulneráveis
![image.png](/.attachments/image-dc0e6e6d-2574-450c-97ad-1afa2b3f1406.png)
![image.png](/.attachments/image-820b08f9-7e4f-4bd6-9ded-3e4deed264ce.png)
![image.png](/.attachments/image-6d93c91e-00fa-4b09-9dac-ad0299b4eb05.png)
![image.png](/.attachments/image-5a78a6c8-7a94-4555-a956-4a70c590f99f.png)

