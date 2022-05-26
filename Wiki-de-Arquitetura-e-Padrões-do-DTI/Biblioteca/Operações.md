A manutenção do ambiente que suporta os recursos tecnológicos da Sefaz-SP é responsabilidade do COI - Centro de Operações e Infraestrutura do DTI. Quando se diz "equipe de operações", entende-se quaisquer colaboradores do COI competentes a atuar sobre alguma situação. 

Toda vez que se observa a necessidade de algum tipo de intervenção na infraestrutura, ela deve ser documentada em uma RDM - Requisição de Mudança através da aplicação [Remedy](https://sefaznetvirtual.intra.fazenda.sp.gov.br/nidp/idff/sso).

Quando a intervenção é relacionada ao ads, a solicitação deverá ser feita através de chamado ao [[Suporte_DevOps]](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Suporte_DevOps), área "Infraestrutura".
A partir desse chamado (PBI) o CIA, em conjunto com a equipe de operações, fará os estudos necessários, planejamento de atendimento e as devidas requisições de mudanças, assim como a homologação da mudança, com o objetivo de evitar que a intervenção prejudique o bom funcionamento dos demais recursos tecnológicos existentes.

# Documentação
Textos de orientação sobre algumas das atividades que envolvem as equipes de operações.

##Suporte ao desenvolvimento
Orientações de apoio às equipes de desenvolvimento.
- [Deploy automatizado](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/ADS/Deploy-automatizado) - essa atividade se divide entre a criação de um pipeline e a execução de um deploy automatizado. Nos dois casos, há a criação de requisições de mudanças.
  - [RDM automatizada de execução](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Operações/Solicitações-de-infraestrutura/Requisição-de-Mudanças/RDM-Automatizada) - explicação sobre como é feita a configuração da task de criação de RDM no pipeline e seu atendimento, usando o template "Deploy automatizado".
  - [RDM para configuração de Deploy Automatizado](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Operações/Solicitações-de-infraestrutura/Requisição-de-Mudanças/RDM-para-configuração-de-Deploy-Automatizado) - auxílio ao preenchimento de RDM do template de mudança "Criação do Deploy Automatizado".
- [Agent Job](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Operações/Agent-Job) - recurso criado no ads que faz o papel de operador de um pipeline. 
- [Usuários em aplicações](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Segurança-da-Informação/Usuários-em-aplicações) - padrões de segurança estabelecem a utilização de contas de serviço.
- [Deploy em servidor Linux](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Operações/Deploy-em-Linux) - (em construção)
- [Ambientes de hospedagem de aplicações](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Operações/Ambientes-de-hospedagem-de-aplicações) - O DTI oferece três ambientes para hospedagem de projetos de desenvolvimento interno.
 
