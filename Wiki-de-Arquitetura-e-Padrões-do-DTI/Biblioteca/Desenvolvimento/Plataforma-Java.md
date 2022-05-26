[[_TOC_]]
#Sobre o Guia
Versão 2020.02.21

___

#Objetivos
Este documento define as diretrizes gerais de arquitetura para desenvolvimento na Plataforma Java. A iniciativa visa estabelecer uma referência para o desenvolvimento padronizado de sistemas que permita o planejamento da adoção de práticas para reduzir riscos de segurança e aumentar a disponibilidade de serviços da instituição. Dentro desta proposta foram priorizados os aspectos referentes às seguintes frentes de trabalho:

- **PLATAFORMA JAVA:** Diretrizes para organização e construção de componentes que devem servir todas as arquiteturas específicas;

- **BANCO DE DADOS:** Revisão de práticas de desenho físico e ferramentas de apoio. Este guia diz respeito à frente de trabalho Plataforma Java e especifica um conjunto de recomendações para o desenvolvimento de aplicações corporativas da SEFAZ. Ele está dividido nas seguintes seções:

   - **Distribuição Lógica dos Componentes** – Descreve uma recomendação de distribuição lógica dos componentes de uma aplicação, destacando características, responsabilidades e benefícios de cada bloco. Fornece os conceitos necessários para os outros capítulos do documento.

   - **Desenvolvimento dos Componentes de uma Aplicação** – Baseado num cenário de exemplo, explica a sequência e as práticas recomendadas de programação para cada tipo de componente, aplicando os conceitos descritos no capítulo anterior.


___

#Requisitos de ambiente

Para a utilização das práticas recomendadas neste guia, é considerado o 
seguinte ambiente:

- **Versão do JAVA:** 8+

- **Versão do Banco de Dados:** SQL Server 2008 R2 SP1 e/ou SQL Server 2012

___

#Recomendações para a linguagem Java

1. [Utilizar as convenções de codificação Java](https://www.oracle.com/technetwork/java/codeconventions-150003.pdf)
1. Spring Boot
1. Spring MVC (dividido nas camadas ```@Controller```, ```@Entity```, ```@Service``` e ```@Repository```)
1. Obrigatório o uso de injeção de dependência (```@Autowired```)
1. **Versão mínima:** Java 8
1. Camada de dados:
    * JPA gerenciado pelo contêiner Spring
    * Transações gerenciadas pelo contêiner
    * Somente utilizar relacionamentos ```@OneToMany``` e ```@ManyToOne```

___


#Visão Geral de Arquitetura

Durante a criação de aplicações distribuídas, recomenda-se a abordagem de serviços sempre que possível. Embora a orientação a objetos forneça uma visão pura do que um sistema deve parecer e é eficaz para a produção de modelos lógicos, uma abordagem baseada em objeto pode deixar de considerar fatores reais, como distribuição física, limites de confiança e comunicação de rede, bem como requisitos não-funcionais, tais como desempenho e segurança.

Além disso, é necessário sempre ter em mente o Separation of Concerns (SoC), ou Separação de Conceitos, ou ainda Separação de Responsabilidades, que é um princípio de design para a separação de uma aplicação em blocos distintos, onde cada bloco tem um objetivo e uma responsabilidade bem definidos. Uma aplicação que implementa bem o SoC é chamado um programa modular. Designs de aplicações em camadas são outra implementação do Separation of Concerns, por exemplo: camada de apresentação, camada de lógica de negócios, camada de acesso a dados, etc. A figura abaixo mostra um exemplo de camadas lógicas de uma aplicação.

![Imagem](/.attachments/Figura01-ebf0d8cf-6ed5-4913-9f30-80e02fd7a2f6.png =400x)

O valor da Separação de Responsabilidades é simplificar o desenvolvimento e manutenção das aplicações e sistemas. Quando as responsabilidades são bem separadas, componentes individuais podem ser desenvolvidos e atualizados de forma independente. Permite, também, melhorar ou modificar um componente num momento posterior, sem ter que saber os detalhes de outros e sem ter que fazer as alterações correspondentes a esses.

Além da distribuição lógica dos componentes abordada por este documento, foi levada em consideração que os serviços web podem ser acessados por dois tipos de clientes: internos e externos à SEFAZ. Os serviços privados, por estarem disponibilizados na intranet, não oferecem grandes desafios no que concerne à segurança pois eles estão disponíveis do lado de dentro do firewall e tem acesso ao banco de dados e demais recursos.

O mesmo não acontece se o serviço precisa ser disponibilizado para clientes externos (cliente público). Dois paradoxos devem coexistir: ao mesmo tempo que o serviço deve residir em um local acessível externamente, geralmente uma DMZ (zona desmilitarizada), eles também devem acessar recursos que habitam do lado de dentro da empresa, na intranet. É o caso do banco de dados, para citar um exemplo.

A SEFAZ endereçou o problema conforme ilustra a figura abaixo: os serviços expostos externamente (públicos) ficam na DMZ, porém não têm acesso direto aos recursos que precisa. Ao invés, os serviços acessam os recursos necessários através dos serviços privados disponibilizados na intranet. Estes por sua vez, a partir da intranet, conseguem acessar os recursos de que necessitam, como banco de dados por exemplo.

![Imagem02.png](/.attachments/Imagem02-b3301fcb-bef8-4205-9b1a-a9422e0ab8b6.png)

O objetivo desde documento é definir uma referência geral para os blocos da aplicação e não apenas para Serviços WEB. Para maiores informações especificas sobre recomendações desta camada consulte o Guia de Desenvolvimento para Serviços WEB.


___



#Recomendações

##Distribuição lógica dos componentes

Uma aplicação distribuída é usualmente formada por um número considerável de componentes. A divisão de uma aplicação em componentes ajuda na sua criação, manutenção e testes, além de permitir a reutilização de blocos de código previamente testados e otimizados.

No entanto, para se obter o máximo de benefício desta divisão, é necessário entender como os componentes de uma aplicação devem ser distribuídos e também como se comunicam entre si e com outras aplicações.

A distribuição lógica separa a aplicação em camadas e leva em consideração as responsabilidades de cada uma, assim como encapsulamento e desacoplamento de tecnologias usadas.

 ![Imagem03.png](/.attachments/Imagem03-c0f94f21-f105-4dbd-9236-444ad0cd2aa7.png)

No modelo proposto, aplicação baseada em serviços tem as seguintes camadas por trafegam as operações: **controller**, **service**, **repository**. Para ajudar a integrar todas as camadas existe uma camada formada por entidades e configurações (**config**, **dto**, **model**, **exception**).

Blocos complementares, não expecíficos da aplicação, podem ser agrupados estão em pacotes externos(por exemplo: logs, segurança, auditoria).

No âmbito do domínio da aplicação, pode-se ter um site remoto (UI hospedada numa DMZ apartada, por exemplo) e um site local (UI hospedada na mesma máquina que a camada de negócio). Ambas UI’s executam os métodos da camada **controller**. A comunicação entre a camada de apresentação e a camada **controller** deve ser realizada prioritariamente através DTOs (data transfer objects).

A camada **service** implementa as lógicas de negócio (sejam elas específicas de uma entidade de negócio ou orquestrando uma ou mais entidades). Para facilitar a fatoração das regras de negócio em serviços, recomenda-se manter domínios de negócio em pacotes separados.

### dto (Data Transfer Object)

Objetos que transitam informação para fora do domínio da aplicação. Estes objetos não são persistidos. Um DTO pode ser formado por partes de diversas entidades de negócio.

Características:

* Não são persistidos.
* Devem conter apenas propriedades.
* São agnósticos de tecnologia de persistência.
* Devem conter apenas os dados necessários para a função a que se destinam.

### config

Camada que agrupa as configurações da aplicação.

### model

Entidades de negócio que modelam a aplicação. Estas classes servem para transitar informação entre as camadas lógicas da aplicação e tem mecanismo para rastrear as suas alterações. Estes objetos podem ser persistidos e, sua persistência depende de um mecanismo de mapeamento objeto-relacional a ser fornecido pelo provider escolhido para a aplicação.

Características:

* Costumam ser POJOs.
* Guardam anotações de persistência.

### exception

Classes com as exceções específicas da aplicação. Não devem ser repassadas diretamente aplicações externas.

### repository

Classes responsáveis por todo acesso aos **dados da aplicação**. Estas classes devem acessar apenas os dados que são mantidos pela aplicação. Dados mantidos por outras aplicações devem ser acessados por serviços.

Provêm automaticamente operações de inclusão, alteração, exclusão e leitura de sua respectiva entidade.

### service

Camada que contém as regras de negócio. Devem ser realizadas as validações dos parâmetros passados para esta camada, tratamento de exceção e controle de transação.

Seus métodos devem ser atômicos, deixando sempre a aplicação num estado íntegro, mesmo na ocorrência de exceções.

Podem alimentar “contadores de negócio” quando necessário, assim como auditar as operações de negócio.

_Validação dos parâmetros:_

As classes de negócio devem conter toda a validação dos parâmetros passados para esta camada.

Um método de um serviço que é chamado a partir da camada de apresentação, normalmente tem a maioria de seus parâmetros já validada pela interface com o usuário, neste caso, um parâmetro inválido poderia ser tratado como uma exceção. Já para um método que é chamado a partir de sistemas externos (API), não se pode assumir nenhuma validação prévia de seus parâmetros.

_Retorno das validações:_

Algumas técnicas podem ser usadas para retornar os resultados das validações realizadas por um método da camada Business Process:

* Disparar exceções próprias de validação de regras de negócio - Ideal para métodos chamados pela camada de apresentação, a qual já fez a maior parte das validações.
* Retornar um objeto contendo uma lista dos resultados das validações de seus parâmetros além de seu valor de retorno propriamente dito – Ideal para métodos chamados por sistemas externos.
* Criar um método adicional específico para a validação dos parâmetros - Este método seria chamado apenas pelas operações da camada **controller**, impondo uma validação mais severa apenas às chamadas por sistemas externos.

_Tratamento de exceções:_

Independentemente do tratamento de exceção nas diversas camadas da aplicação, a camada de serviço deve sempre que possível tratar e/ou logar as exceções.

### controller

Camada com as definições dos controllers visando expor os serviços para páginas web ou como APIs. Idealmente, todas as entidades expostas por esta camada são DTOs.

Características:

* Encapsulam as especificidades das tecnologias de comunicação.
* Convertem DTOs em entidades de negócio e vice-versa.

