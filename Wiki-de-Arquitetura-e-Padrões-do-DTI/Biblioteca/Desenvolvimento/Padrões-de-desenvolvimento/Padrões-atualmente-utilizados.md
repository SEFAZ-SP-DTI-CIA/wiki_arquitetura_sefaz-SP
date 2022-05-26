# Padrões atualmente utilizados pela SEFAZ

## Navegadores e versões mínimas suportadas:

* Internet explorer: 11 _(Obs.: O IE está em fim de suporte e não deve ser pré-requisito para nenhum sistema da Sefaz)_
* Firefox: N/D
* Chrome: N/D
* Safari: N/D

## Autenticação

* SEFAZ Identity como STS
    * Atualmente o SEFAZ Identity aceita apenas o protocolo WS-Federation para autenticação de aplicações Web.
* Usuários internos: autenticação com certificado digital
* Usuários externos: login e senha com utilização do recaptcha

## Padrão visual

* Utilizar o layout padrão definido pela SEFAZ, disponível em: https://webdesenv.intra.fazenda.sp.gov.br/LayoutPadrao/


## Desenvolvimento Front-end

* Utilizar os engines padrões definidos pelas tecnologias de back-end;
* Angular;


## Desenvolvimento Back-end

### .NET

* .NET Core (5.0)

* .NET 4.8 (apenas projetos já em desenvolvimento)

* Acesso a banco de dados

    * Entity Framework

    * Dapper

    * Ado.NET

* Testes automatizados

    * xUnit

    * Moq

    * Selenium

### Java

* Java 11

* Spring Framework

* Acesso a banco de dados

    * JPA

    * Hibernate

* Testes automatizados

    * Junit

    * Mockito

### Python

* Django

* Flask

* Tests automatizados

    * unittest
    * mock

## Testes de carga

* JMeter

## Banco de dados

* Oracle

* SQL-Server

