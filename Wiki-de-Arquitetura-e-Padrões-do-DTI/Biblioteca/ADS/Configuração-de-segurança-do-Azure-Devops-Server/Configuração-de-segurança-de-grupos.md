A configuração de um grupo de uma collection é feita em "Collection Settings", a de projetos e de times em "Project Settings".
As duas páginas exibem a opção "Security" no menu lateral direito. Por exemplo
- Collection Settings - https://ads.intra.fazenda.sp.gov.br/tfs/PRODUTOS/_settings/security
- Project Settings - https://ads.intra.fazenda.sp.gov.br/tfs/PRODUTOS/Sefaz_Identity/_settings/security

A página de segurança contém três painéis de configuração:
- Permissions - configurações de itens de permissão que cada usuário desse grupo possuem.
Há quatro tipos de permissão para cada item:
![Permissions.png](/.attachments/Permissions-47ab85f9-8cf6-4e1a-82cf-703cbf529f15.png =300x)
    - Not set - indefinido, o acesso ao recurso é definido em outro grupo.
    - Allow - permite o uso do recurso.
    - Allow inherited - a permissão é herdada de algum grupo (pai) ao qual esse pertence, quando for o caso.
    - Deny - nega acesso ao recurso, se o usuário não estiver em outro grupo que o permita.
- Members - gerencia a lista de usuários.
- Members of - exibe os grupos (pai) a que esse grupo pertence.

#Grupos padrão de projetos
Ao ser criado um projeto, alguns grupos já estão disponíveis para a inclusão de usuários, entre eles:
- Project Team - um time com o nome do projeto.
- Contributors - grupo iniciado com o time do projeto, permite aos seus integrantes acesso aos repositórios e pipelines, por padrão.
Por prática, recomenda-se que todos os times criados seja incluídos em Contributors.
- Project administrators - usuários que têm permissão para qualquer ação dentro do projeto.
- Readers - seus membros só podem ver parte dos recursos do projeto.

![Grupos_projeto.png](/.attachments/Grupos_projeto-72dcc98d-8ea5-488e-985a-694a9986aa4b.png =400x)

#Grupos padrão de collections
As collections também têm diversos grupos padrão similares aos de projetos.
![Grupos_collection.png](/.attachments/Grupos_collection-d6c43c7f-5f24-4046-9acf-ea6c8ef0f83c.png =200x)

##Grupos de produtos SAFe
A Sefaz criou grupos ads de collection para administrar times de projetos chamados de "Produtos SAFe".
Esses grupos são usados para dar maior eficiência à administração de acessos aos projetos dos produtos.
Ao se configurar um grupo de um projeto, adiciona-se um grupo SAFe, usualmente em um time.

Por exemplo:
![SAFe_Grupos.png](/.attachments/SAFe_Grupos-78b9b2c8-b713-481a-b95c-6737980ca9b4.png =600x)

Para solicitar a criação de um grupo de produtos SAFe, abra um chamado no [Suporte_Devops](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Suporte_DevOps/_wiki/wikis/Suporte_DevOps.wiki/128/Solicita%C3%A7%C3%A3o-de-cria%C3%A7%C3%A3o-ou-altera%C3%A7%C3%A3o-de-Grupo-de-Usu%C3%A1rios-no-ads), área Desenvolvimento.


