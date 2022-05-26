# Entrega contínua (CD)

## Regra
Em um processo de [entrega contínua](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/ADS/Deploy-automatizado/Release-pipeline/Execução-de-releases), após a execução de uma build bem sucedida, pelo menos uma release de um pipeline é criada [automaticamente para deploy](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/ADS/Deploy-automatizado) em ambiente de desenvolvimento ou testes.

>A Sefaz não adota a entrega contínua nos ambientes de homologação e de produção, que requerem a criação e aprovação prévia de [requisições de mudanças](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Operações/Solicitações-de-infraestrutura/Requisição-de-Mudanças).

Espera-se que os artefatos sejam promovidos na [estratégia de promoção de binários](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/ADS/Deploy-automatizado/Estratégias-de-promoção-de-artefatos), em [pipeline de release em deploy automatizado](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/ADS/Deploy-automatizado/Release-pipeline). 

## Critério de aceitação
Este item deve conter um descritivo dos releases criados no projeto ADS referente à aplicação.
