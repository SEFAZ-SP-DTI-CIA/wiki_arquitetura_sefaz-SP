# [Sefaz Identity - Versão 003 (SSO)](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/413/Sefaz-Identity-vers%C3%A3o-003-(SSO))

<br/>

V003 é a versão recomendada para novas aplicações. Para aplicações já existentes, a diretriz é que migrem da v002 para a v003 imediatamente.

Permite que os usuários realizem apenas um login no Portal da Secretaria da Fazenda e possam navegar pelos vários serviços/sites disponíveis sem necessidade de autenticação a cada troca de serviço/site. A autenticação continua válida dentro da mesma sessão de interação entre usuário-navegador.

O Sefaz Identity é o principal sistema para controlar o acesso às aplicações de linha de negócio da SEFAZ. E por ser um componente fundamental para as diversas aplicações, frequentemente, melhorias e novas funcionalidades são solicitadas, demandando evoluções periódicas deste sistema.

Essa foi a motivação para a implementação de processo que permita o Single Sign-On (SSO – autenticação única e integrada entre múltiplas aplicações) para suportar os diversos serviços/sites disponibilizados aos contribuintes/usuários da Secretaria da Fazenda e Planejamento.

Um benefício direto do SSO é o enriquecimento da experiência de uso (UX) para o usuário final, que será autenticado uma única vez e poderá utilizar as várias aplicações da SEFAZ.

<br/>

---

# Leitura

<br/>

**[Autenticação X Autorização - Entendendo as principais diferenças](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/421/Autentica%C3%A7%C3%A3o-X-Autoriza%C3%A7%C3%A3o-Entendendo-as-principais-diferen%C3%A7as)** - 3 minutos para ler

**[Tipos de Certificados](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/422/Tipos-de-Certificados)** - 3 minutos para ler

<br/>

---

# Suporte_DevOps\Identity

<br/>

**[Solicitações Gerais](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Suporte_DevOps/_workitems/create/Product%20Backlog%20Item?templateId=3987f228-cfa0-4737-a63b-0842ab420ca1&ownerId=9657433a-6110-4733-8620-92a4c2a5d51b)**

**[Cadastro de Aplicação no Controle de Acesso do Sefaz Identity](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Suporte_DevOps/_workitems/create/Product%20Backlog%20Item?templateId=c491ab6f-14d2-4d3c-86fb-49d15084a4c7&ownerId=9657433a-6110-4733-8620-92a4c2a5d51b)**
Obs: Solicitações de acesso devem ser feitas aos administradores atuais e não serão atendidas pela equipe dtiidentity.

**[Cadastro de Usuário no Controle de Acesso do Sefaz Identity](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Suporte_DevOps/_workitems/create/Product%20Backlog%20Item?templateId=9d7fbaf5-ba11-47ba-bd48-f8cf6e88a88d&ownerId=9657433a-6110-4733-8620-92a4c2a5d51b)**

**[Cadastro de Grupo de Aplicação no Controle de Acesso do Sefaz Identity](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Suporte_DevOps/_workitems/create/Product%20Backlog%20Item?templateId=ac817815-e13e-4970-882a-1c1ed2ae56ea&ownerId=9657433a-6110-4733-8620-92a4c2a5d51b)**
Obs: Necessário somente para utilização do Portal de Assinatura e/ou do LogAudit.

<br/>

[Wiki de Arquitetura e Padrões do DTI](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura)
