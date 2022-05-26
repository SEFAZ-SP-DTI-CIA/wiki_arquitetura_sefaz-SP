# Desenvolvimento e utilização de componentes

## Introdução

O desenvolvimento do código em forma de componentes permite a reutilização do mesmo em outros projetos, além de acelerar a maturidade do código (com vários times usando e evoluindo o código fonte).

Na SEFAZ, o Azure DevOps Server disponibiliza um feed de artefatos que permmite a publicação e reunitilização de componentes. Atualmente são suportados os seguintes protocolos para publicação de bibliotecas:

* .NET - nuget
* Java - maven
* Python - pip

---

## Regra

O projeto do código fonte deve estar configurado para utilizar o feed da SEFAZ. Além disso, havendo a existência de componentes prontos, o projeto deve priorizar a sua utilização em detrimento de desenvolvimento próprio. Alguns exemplos de componentes disponíveis atualmente no feed da SEFAZ: Autenticação via SEFAZ Identity, termo de ciência (LGPD), assinador de documentos etc.

## Critério de aceitação

O build de integração contínua deve estar configurado para buscar os componentes do feed do ADS e não de uma pasta interna da aplicação. 

Caso o projeto disponbilize componentes, deverá existir uma esteira de integração e entrega no Azure DevOps para publicação no feed de artefatos.