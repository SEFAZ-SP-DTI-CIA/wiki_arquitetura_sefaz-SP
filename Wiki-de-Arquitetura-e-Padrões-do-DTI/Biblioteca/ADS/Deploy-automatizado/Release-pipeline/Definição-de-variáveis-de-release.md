No menu «Variables», cadastram-se variáveis que podem ser utilizadas nas tasks. 
![Variaveis.png](/.attachments/Variaveis-a27d6582-ac40-464b-aeb4-903efaf9051f.png =600x)

Variáveis com o cadeado escondem o seu conteúdo e não permitem <ctrl C>, útil para armazenar senhas. 

A coluna “Scope” define dois tipos de variáveis:
- Release - comum para todos os stages da release. 
- Stage - usada somente no stage indicado (dev, hml, prd etc.).
>Ao se clonar um stage, as variáveis desse tipo são duplicadas para o novo stage.

O coluna "Settable at a release time" sinaliza que o conteúdo do campo deve ser oferecido para alteração no painel de criação de releases, apropriado para informações que podem mudar no momento do deploy, como datas.

As variáveis de todos os stages podem ser visualizadas lado a lado ao clicar o botão «Grid», no lado direito da tela.
Esta visão em tabela favorece a identificação de erros de digitação e a administração das referências.

![image.png](/.attachments/image-1780d694-d64b-4812-8809-b464f5fb6197.png)

#Variáveis pré-definidas
O ads oferece diversas variáveis pré-definidas, que contém informações de ambiente, que podem ser consultadas pelo link [«pre-defined variables»](https://docs.microsoft.com/en-us/azure/devops/pipelines/release/variables?view=azure-devops&tabs=batch). 

#Variáveis de substituição
São variáveis que são usadas com seus nomes entre “$(“ e “)”, por exemplo $(ads_admin). 
![image.png](/.attachments/image-82602f66-38cb-452c-a3e1-31e9c018c36c.png)

#Variáveis de transformação
São variáveis que podem ser usadas para substituição de valores nos arquivos de configuração.
Os nomes dessas variáveis devem corresponder a campos ou chaves dentro desses arquivos.

![Variaveis_transformacao.png](/.attachments/Variaveis_transformacao-1594fa9a-e6d2-404b-ac3c-b14cd382d1e6.png =1000x)

##JSON
Em arquivos em formato JSON, as variáveis são nomeadas usando expressões com sintaxe [JSON Path](https://docs.microsoft.com/en-us/azure/devops/pipelines/tasks/transforms-variable-substitution?view=azure-devops&tabs=Classic#json-variable-substitution-example).

Para susbstituir ‘ConnectionString’ no exemplo abaixo, define-se a variável como
 `Data.DefaultConnection.ConnectionString`

```
{
  "Data": {
    "DefaultConnection": {
      "ConnectionString": "Server=(localdb)\SQLEXPRESS;Database=MyDB;Trusted_Connection=True"
    }
  }
}
```
>A sintaxe JSON Path em pipelines Azure tem uma pequena alteração. As referências ao índices de itens de um vetor não usam colchetes, mas somente o número, por exemplo:
`listaItens.0.nome` em vez de `listaItens[0].nome`

##XML
Para arquivos nesse formato, como web.config, os nomes das variáveis são comparadas com valores do tipo 'key' ou 'name' para fazer a substituição.

Neste exemplo:
```
<appSettings>
    <add key="Ambiente" value="Desenvolvimento" />
```
Para substituir 'Ambiente', cadastra-se a variável com o mesmo nome da chave. Assim, o conteúdo de 'value' será substituído pelo valor da variável.
Ambiente : Desenvolvimento










