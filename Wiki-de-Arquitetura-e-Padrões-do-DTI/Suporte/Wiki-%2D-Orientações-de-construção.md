[[_TOC_]]

# **Orientações de formatação**

##Página principal relativa a um tema 

### Table of Contents

- Na página principal relativa a um tema, orientamos o uso do recurso Table of Contents no caso de haver diversos tópicos, de forma que estes possam ser visualizados e encontrados com facilidade. Exemplo 1 (Página principal da Wiki): 


   ![Imagem06_v2.png](/.attachments/Imagem06_v2-07aafd67-0d79-4bd5-8e5b-eb2fd909c2da.png =600x)



 - Exemplo 2 (Página sobre Desenvolvimento Seguro): 

   ![Imagem05_v3.png](/.attachments/Imagem05_v3-01ebc4d7-84e3-4352-b61a-9303586f227c.png =600x)

- Link com orientações da Microsoft relativas ao uso de Table of Contents: 

   https://docs.microsoft.com/en-us/azure/devops/project/wiki/wiki-markdown-guidance?view=azure-devops#table-of-contents-toc-for-wiki-pages


___


### Resumo dos tópicos na página principal relativa a um assunto

- Caso haja diversos tópicos na página principal relativa a um assunto, orientamos descrevê-lo em um ou dois parágrafos, e logo em seguida disponibilizar o link para a página mais detalhada, se houver informações adicionais (link "Veja mais.."). Exemplos:

   - Exemplo 1 (Página principal da Wiki, item Informações):

   ![Imagem07_v2.png](/.attachments/Imagem07_v2-e4d41e8b-7c16-4cfd-bd60-b467eb48dcc9.png =600x)

   - Exemplo 2 (Página sobre desenvolvimento seguro): 
  
   ![Imagem08_v2.png](/.attachments/Imagem08_v2-6aeaad1d-3a1d-458e-b8f6-4ba7c346a60e.png =600x)

___
## Links para páginas de outros projetos

- Quando for necessário incluir um link para outro projeto que não o Wiki_Arquitetura, orientamos que esse link seja configurado para a abertura de outra janela do navegador. O modo edição da Wiki no Azure DevOps apresenta um ícone para inclusão de um link, porém este não está configurado para abertura de outra janela. 

  Exemplo (texto do modo edição da Wiki):


      <a href="https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Suporte_DevOps/_wiki/wikis/Suporte-Desenvolvimento-DTI.wiki?wikiVersion=GBwikiMaster&pageId=56&pagePath=%2FSuporte%20DevOps" target="_blank">Wiki do Suporte DevOps</a>. 

  O texto “Wiki do Suporte DevOps” será apresentado no modo de apresentação da Wiki, conforme a seguir:  

   <a href="https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Suporte_DevOps/_wiki/wikis/Suporte-Desenvolvimento-DTI.wiki?wikiVersion=GBwikiMaster&pageId=56&pagePath=%2FSuporte%20DevOps" target="_blank">Wiki do Suporte_DevOps</a>

___
 
##Exemplo de uso de tabelas para disponibilização de informações

- A seguir é apresentado um exemplo de uso de tabela para disponibilização de uma grande quantidade de informações, de modo que possa ser encontrada com facilidade pelo usuário. A segunda coluna do exemplo abaixo disponibiliza **links** para os comunicados enviados:

   ![Imagem09.png](/.attachments/Imagem09-09e8da42-c997-4397-952d-17bdf4b37fac.png =500x)


  Sintaxe (até primeira linha da tabela acima):

      | Data| Título|
      |--|--|
      | 04/2020| [Acesso ao Azure DevOps através de VPN](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki%20de%20Arquitetura?wikiVersion=GBwikiMaster&pagePath=%2FWiki%20de%20Arquitetura%20e%20Padr%C3%B5es%20do%20DTI%2FComunicados%2FAcesso%20ao%20Azure%20DevOps%20atrav%C3%A9s%20de%20VPN&pageId=224) |


____


# **Links úteis com orientações sobre Wiki no Azure DevOps**

https://docs.microsoft.com/en-us/azure/devops/project/wiki/markdown-guidance?view=azure-devops

https://docs.microsoft.com/en-us/azure/devops/project/wiki/wiki-markdown-guidance?view=azure-devops

https://docs.microsoft.com/en-us/azure/devops/project/wiki/wiki-keyboard-shortcuts?view=azure-devops


___

# **Conversão do formato Word para Markdown**

## Conversão de Word em Markdown através do programa pandoc

1. Baixe o programa pandoc.

2. Execute o comando: \pandoc.exe --toc --extract-media=<caminho para imagens> --wrap=preserve .\<nome do arquivo origem>.docx -o .\<nome do arquivo destino>.md

3.	Utilize um programa editor de texto do tipo Notepad++ e substitua todas as ocorrências do padrão “{width=".+" height=".+"}”, conforme exemplificado abaixo. Utilizar a opção “expressão regular”:

    ![Imagem10.png](/.attachments/Imagem10-0ab6fe72-8588-469f-92e1-55ef37eb6a9d.png =600x)

4. Utilize o git para enviar o arquivo e as imagens gerados para o repositório onde fica a wiki. Observação: O ideal é ter uma pasta para cada arquivo que vai ser carregado, e dentro dessa pasta o arquivo .md e a pasta com as imagens.

5. Após carregar o arquivo, verifique se a estrutura do arquivo está OK. 

    Sugestão: remover o índice original e adicionar um índice do markdown. Comando :  
 

        "[[_TOC_]]"


6. Para controlar o tamanho das imagens carregadas, caso seja necessário, pode-se adicionar =???x após o caminho para a imagem, conforme exemplo a seguir:


       ![](media/media/image2.png =800x)

___

# **Obtendo estatisticas de visualização de Wikis**

O ADS ainda não provê uma solução para analisar o uso da Wiki com informações de quantidade de "page views" e etc..

A informação mais próxima possível é a quantidade de acessos em uma determinada página por dia, mas não diferencia usuários distintos, ou seja, se o mesmo usuário acessar a mesma página no mesmo dia, serão contabilizados diversos acessos.

Essa informação é acessada apenas por API REST:

GET https://dev.azure.com/{organization}/{project}/_apis/wiki/wikis/{wikiIdentifier}/pages/{pageId}/stats?pageViewsForDays=30&api-version=6.0-preview.1

Response
Status code:
200
JSON


```
{
  "path": "/Test Page",
  "id": 1,
  "viewStats": [
    {
      "day": "2019-10-15T00:00:00",
      "count": 5
    },
    {
      "day": "2019-10-16T00:00:00",
      "count": 2
    },
    {
      "day": "2019-10-17T00:00:00",
      "count": 1
    }
  ]
}
```

Exemplo para a 1a página dessa wiki:

https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_apis/wiki/wikis/1e6e84bc-df95-4625-9181-e41725315c6a/pages/135/stats?pageViewsForDays=30&api-version=6.0-preview.1

Mais informações em:
https://docs.microsoft.com/en-us/rest/api/azure/devops/wiki/page%20stats?view=azure-devops-rest-6.0

