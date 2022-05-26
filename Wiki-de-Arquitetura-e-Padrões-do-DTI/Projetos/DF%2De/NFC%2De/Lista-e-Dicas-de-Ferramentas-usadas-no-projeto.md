## (Usar o índice lateral para navegar nos tópicos)

Dicas gerais de ferramentas
[ClicarAqui](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/292/Dicas-de-Ambiente)
 
Ferramentas usadas para o desenvolvimento do projeto NFC-e
 * Microsoft .Net Framework 4.5
 * Visual Studio 2012 (Ultimate ou Premium) com extensões abaixo:
   * Microsoft Team Foundation Server 2012 Power Tools
   * SlowCheetah - XML Transforms
 * SQL Server 2008 ou 2012 (local)
 * SQL Server Data Tools 2012
 * SQL Server 2012 Express Management Studio w/SP1
 * soapUI 4.5.x ou 4.6.0
 * mmc (C:\Windows\System32\mmc.exe)
 * KeyStore Explorer (verificar e manipular certificados)

Observações:

## VISUAL STUDIO

 * O Visual Studio deve ser executado com permissões de Administrador da máquina
 * As Extensões do Visual Studio devem estar disponíveis Online (Menu Tools/Extension and Updates)
 * Caso não apareçam as opções de Extensão Online, seguir os passos abaixo:
   * Editar o arquivo {Visual Studio folder}\Common7\IDE\devenv.exe.config incluindo a linha em **negrito** abaixo

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;   `<system.net>`
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;        `<settings>`
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;            `<ipv6 enabled="true"/>`
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;   **<servicePointManager expect100Continue="false" />**
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;        `</settings>`
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;     `</system.net>`


##        Atalhos e Janelas no VisualStudio 2013:

1) Abrir Janela Conflicts: TeamExplorer>PendingChanges>Actions>ResolveConflicts

2) Encontrar changeset: TeamExplorer>SourceControlExplorer>CTRL+G>colar número do changeset>OK (clicar em "Find" abre diálogo para busca avançada)


## SOAP UI

 * **Para incluir o certificado no SoapUI**
     1. Duplo clique no nome do projeto
     2. Aba "WS-Security Configurations"
     3. Botão "+" , procurar local onde se encontra o arquivo .p12 ou equivalente
     4. Digitar a senha do certificado
     5. Verificar se status == OK
     * Mais detalhes em: http://www.soapui.org/SOAP-and-WSDL/applying-ws-security.html

 * **Para incluir um certificado no XML request**
     * Selecionar o Test-Step
     * Na janela TestRequest Properties, selecionar o certificado em "SSL Keystore"
     * Fechar e abrir novamente o SoapUI
 * **Para alterar o endereço de um teste existente**, clique no drop-down que contém o endereço, "[edit current]" (não adianta alterar direto na caixa)
     * **Para atualizar as definições dos WebServices** (por exemplo, um novo WebMethod foi criado)
     * right-click sobre a Interface que deseja atualizar, item "Update Definition" ou F5 (ver imagem)

![UpdateSoapUIws.png](/.attachments/UpdateSoapUIws-150fae79-a6d4-4e0d-b592-790d48721b4b.png) 

**Obs:** a maioria dos Test Steps precisa que seja setada a propriedade _**StripWhiteSpaces**_ = true