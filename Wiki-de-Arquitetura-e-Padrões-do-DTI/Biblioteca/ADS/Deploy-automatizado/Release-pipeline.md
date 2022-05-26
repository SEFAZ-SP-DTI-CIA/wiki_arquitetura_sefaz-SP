Pipeline de "criação" de releases é um conjunto sequenciado de tarefas tipicamente capaz de fazer um deploy automatizado, que é a configuração e implantação de artefatos em um ou mais servidores de aplicações com mínima intervenção humana.
Veja um exemplo de pipeline de deploy no [SiteCIA](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_release).

>Um pipeline de release pode também ser usado para fazer outras atividades operacionais, como configurações de servidores ou serviços, cópia ou exclusão de arquivos, instalação de patches de segurança, verificação ou atualização de recursos etc.

Um release pipeline é composto de:
- Artifacts - que é uma ligação com algum build pipeline (Artifact publish location - Azure Pipelines) para a busca dos arquivos que serão copiados para seus destinos (deploy).
- Stages - sequência de tarefas

A utilização de um release pipeline requer: 
- [Criação do pipeline](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/ADS/Deploy-automatizado/Release-pipeline/Criar-release-pipeline)
- [Configuração de stages](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/ADS/Deploy-automatizado/Release-pipeline/Configuração-de-stage)
- [Inclusão de tasks](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/ADS/Deploy-automatizado/Release-pipeline/Inclusão-de-tasks)
- [Definição de variáveis](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/ADS/Deploy-automatizado/Release-pipeline/Definição-de-variáveis-de-release)
- [Execução e gestão de releases](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/ADS/Deploy-automatizado/Release-pipeline/Execução-de-releases)

>Nessa wiki, serão oferecidas informações suficientes para deploy de aplicações web em ambiente de desenvolvimento, no servidor webdesenv, em plataforma Windows que utilizam do IIS. 
Para outros tipos de deploy, abra um chamado no [[Suporte_DevOps]](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Suporte_DevOps/_workitems/create/Product%20Backlog%20Item?templateId=f2327198-0479-4896-a596-9473509abab7&ownerId=edf1fe16-ac08-4f98-bbbc-e06e32d9686e), área "Desenvolvimento".

Para configurar stages em homologação ou produção, abra uma [RDM de Criação de Deploy](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Operações/Solicitações-de-infraestrutura/Requisição-de-Mudanças/RDM-para-configuração-de-Deploy-Automatizado).

