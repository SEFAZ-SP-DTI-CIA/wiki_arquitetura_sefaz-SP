[[_TOC_]]

# _​Problemas encontrados no IIS_

### _O conjunto de chaves não existe_

Esse erro ocorre quando o certidicado digital não está acessível ao WCF.

## Detalhes do erro:

[CryptographicException: O conjunto de chaves não existe].

[ArgumentException: Provavelmente, talvez aquele certificado 'CN=XXXXXXXXXXXXXXXXX, OU=Autenticado por XXXXXXXXXXXXXXXXXXXXXXX, OU=RFB e-CNPJ A1, OU=Secretaria da Receita Federal do Brasil – RFB, O=ICP-Brasil, L=Sao Paulo, S=SP, C=BR' não tenha uma chave privada capaz de ser trocada, ou talvez o processo não tenha direitos de acesso para a chave privada. Consulte a exceção interna para obter detalhes.].

[ServiceActivationException: Não é possível ativar o serviço '/services3/CreditoRural.svc' devido a uma exceção durante a compilação. A mensagem da exceção é: Provavelmente, talvez aquele certificado 'CN=XXXXXXXXXXXXXXXXX, OU=Autenticado por XXXXXXXXXXXXXXXXXX, OU=RFB e-CNPJ A1, OU=Secretaria da Receita Federal do Brasil – RFB, O=ICP-Brasil, L=Sao Paulo, S=SP, C=BR' não tenha uma chave privada capaz de ser trocada, ou talvez o processo não tenha direitos de acesso para a chave privada. Consulte a exceção interna para obter detalhes..].


____________________________________________________________________________________________________________________________________________________________________________________________________________________________________________________

Solução:

​Executar os comandos encontrados nas página abaixo por através da aplicação FindProvateKey.exe:

https://docs.microsoft.com/en-us/dotnet/framework/wcf/feature-details/how-to-make-x-509-certificates-accessible-to-wcf

Segue o link onde pode-se encontrar o download FindProvateKey.exe​:

https://docs.microsoft.com/en-us/dotnet/framework/wcf/samples/findprivatekey

# Trabalhando com Visual Studio

## Abrindo uma Solution

1) Abrir a janela "Team Explorer" e clicar no botão de gerenciar conexões (![image.png](/.attachments/image-baeb78e3-7162-48b7-9ca3-9bf09f1aef2c.png))

![image.png](/.attachments/image-8698f824-ce64-4cf3-a2be-550bc33e4bcf.png)

2) Clicar no link 'Manage Connections':
![image.png](/.attachments/image-f7dc6473-9b00-48ed-bc42-ae43e5bec3d7.png)

3) Clicar em 'Connect to a Project':
![image.png](/.attachments/image-b3ceed94-d260-4096-b756-38ff3c549f0a.png)

4) Adicionar o usuário e clicar em 'Add TFS Server':
![image.png](/.attachments/image-bbb54c8a-125a-453e-8584-a04b05af8c36.png)

5) Digitar 'ads.intra.fazenda.sp.gov.br' e clicar no botão 'Add':
![image.png](/.attachments/image-1306a3dc-f91d-4997-a7e5-8b607463f15e.png)

6) Selecionar o projeto desejado, e clicar no botão 'Connect' (esse passo pode demorar):
![image.png](/.attachments/image-c58ecbb1-f0d2-49be-90f9-5d1edc173bff.png)

7) De volta ao Team Explorer, selecionar um WorkSpace:
![image.png](/.attachments/image-b3679ea4-7bf3-4c16-8f15-e1a39b9a359f.png)

8) Caso não exista um Workspace mapeado, clicar na opção 'Manage Workspace':
![image.png](/.attachments/image-f2aa69e4-6854-4547-96f2-3e3a4ceb5fbb.png)

9) Verifique novamente se não existe um Workspace mapeado. Caso não exista, clicar no botão 'Add':
![image.png](/.attachments/image-eb0d22c3-c616-492c-9084-d5d77ec46cb4.png)

10) Preencher com os dados do Workspace (o exemplo abaixo é apenas ilustrativo, cada projeto terá uma configuração própria) e clicar no botão 'OK':
![image.png](/.attachments/image-67f07698-bbd5-43d4-acc6-f86119391667.png)

11) De volta à janela de Workspace, clicar no botão 'Close':
![image.png](/.attachments/image-c30cbd1d-5d25-4775-9103-dad5c5258f3e.png)

12) De volta ao Team Explorer, a parte inferior mostrará a lista de Solutions:
![image.png](/.attachments/image-c4655f8c-11d6-4451-bcfc-34e7c5456c9e.png)

13) Ao colocar o cursor do mouse sobre a Solution, aparecerá o mapeamento:
![image.png](/.attachments/image-d54566f8-abd4-4717-bfea-b0d6ab6885bc.png)

14) Ao clicar duas vezes no nome da Solution, a mesma será aberta no Visual Studio, podendo ser visualizada na janela 'Solution Explorer':
![image.png](/.attachments/image-47224fe0-9587-4d73-a91c-ce9352bc01d9.png)

15) Clicando com o botão direito do mouse sobre a Solution no 'Solution Explorer', é possível verificar um menu flutuante. 
![image.png](/.attachments/image-6dea53b6-abe2-420e-95de-8b1a13987bde.png)

16) Ao clicar na opção 'Open Folder in File Explorer' do menu flutuante, é possível verificar que os arquivos foram copiados para a área local previamente mapeada:
![image.png](/.attachments/image-50ac26dc-c9b3-4f18-913c-53cc66696db5.png)
