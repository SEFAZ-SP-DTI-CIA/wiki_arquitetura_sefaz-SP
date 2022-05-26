# Testes unitários automatizados

## Introdução

A realização de testes unitários é uma boa prática para garantir a qualidade da aplicação. No .NET, por exemplo, é possível construir testes unitários diretamente no Visual Studio e executá-los na própria ferramenta na janela do Test Manager. 

Para que os testes sejam eficazes, o ideal é a utilização de um framework próprio para testes e que o percentual de código fonte coberto por esses testes seja alto. Apesar do desejado seja a criação de testes para toda a aplicação, esta regra em particular considera como obrigatória uma boa cobertura de código apenas para os projetos da camada de negócios.

Para a realização destes testes recomenda-se a utilização das seguintes ferramentas:

### .NET

* xUnit
* Moq
Um exemplo de utilização destes frameworks podem ser visto [aqui](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_git/Exemplo%20Testes%20xUnit).

### Java

* Junit
* Mockito

### Python

* unittest
* mock

---

## Regra

Os projetos devem ter testes unitários e integrados que cubram pelo menos 80% do código da camada de negócio da aplicação. Estes testes devem ser desenvolvidos utilizando frameworks aprovados pela [SEFAZ](http://etc.intra.fazenda.sp.gov.br/sites/suporte_desenv/SitePages/kit-boas-vindas.aspx).

## Critério de aceitação

Build de integração contínua deve ter tarefa para execução dos testes automatizados. Análise de cobertura de código deve indicar que a camada de negócio está coberta em pelo menos 80%.
