# Integração contínua (CI)

## Regra
O código salvo no repositório do ADS não pode conter erros de compilação. 
Assim, recomenda-se a [criação de um pipeline de build](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/ADS/Deploy-automatizado/Build-pipeline) que compile o código do repositório sempre que alguma alteração é enviada por um _check-in_ ou _push_.

Os artefatos gerados pelo pipeline de build no ADS devem ser [publicados em "Azures Pipelines"](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/12/Configura%C3%A7%C3%A3o-das-tasks?anchor=publish-artifact), não mais na BMD,  para que não possa ser alterado manualmente, evitando falhas no processo de instalação da aplicação.

## Critério de aceitação
Deve conter um descritivo dos builds criados no projeto ADS referente à aplicação. O trigger do build deve ser automático ao fazer push na branch do repositório.

As instruções para configuração podem ser encontradas em [Deploy automatizado](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/ADS/Deploy-automatizado).
