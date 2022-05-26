<h1>Destaques</h1>

1. **ADS - Azure DevOps Server**
O [ADS - Azure DevOps Server](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/ADS) é um sistema de gerenciamento de código, artefatos, documentação, dashboards e backlog dos projetos de desenvolvimento adotado pela Sefaz. 
   >    
   >- [Configuração de segurança do ads](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/ADS/Configuração-de-segurança-do-Azure-Devops-Server) - veja como o ads está estruturado e como se configuram as permissões de acesso e perfis.
   >- [Deploy automatizado](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/ADS/Deploy-automatizado) - do código ao deploy, com pouca intervenção humana, com mais agilidade e segurança.
   >- [Servidores compartilhados para aplicações .Net](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Recursos/Servidores-compartilhados-para-aplicações-.Net) - a Sefaz oferece um conjunto de servidores web para serem utilizados por aplicações .Net (antigo dotnet core).
   >- [Acesso remoto à intranet](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Recursos/Acesso-remoto-à-intranet) - com o teletrabalho e com a substituição de estações de trabalho por notebooks, a Sefaz oferece duas formas de acesso aos recursos internos da rede através de VPN.

1. **Git**
O [Git](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Desenvolvimento/Git) é a ferramenta oficial de versionamento de código de desenvolvimento de sistemas na Sefaz-SP.
TODOS os projetos que ainda estão utilizando o [TFVC (Team Foundation Version Control)](https://docs.microsoft.com/en-us/azure/devops/repos/tfvc/what-is-tfvc?view=azure-devops-2020) deverão [converter o código para GIT](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Desenvolvimento/Git/Migração-de-controlador-de-versão-%2D-TFVC-para-o-Git).

1. **Monitoração** 
Está disponível para utilização nas aplicações o serviço de [monitoração do Azure Devops: Application Insights](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Segurança-da-Informação/Monitoração/Application-Insights)

1. **Kit de boas vindas a novos desenvolvedores** 
Consulte o [Kit de Boas Vindas a Novos desenvolvedores](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Desenvolvimento/Kit-de-Boas-Vindas-a-novos-desenvolvedores-do-DTI). 

[[_TOC_]]

#Projetos do DTI
Conheça alguns dos [projetos de desenvolvimento do DTI](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Projetos) e sua documentação de acesso público.

##Catálogo de Serviços de TI
O [Catálogo de Serviços da TI](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Recursos/Catálogo-de-serviços) relaciona todos os serviços em desenvolvimento, implantados ou finalizados da Sefaz-SP. 

##Wiki
Recomenda-se que os projetos de desenvolvimento façam a [documentação em formato Wiki](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Suporte/Wiki-%2D-Orientações-de-construção), dentro do ads, sempre que possível. 

Uma wiki de projeto é acessível apenas por seus membros. 
[Páginas públicas dos projetos](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Projetos) devem ser criadas e mantidas nessa Wiki de Arquitetura e Padrões do DTI.

---

# **Informações**

##Portais colaborativos
Encontre diversos textos sobre tecnologia em nossos antigos portais:

- Wiki de Desenvolvimento do CDS
http://etc.intra.fazenda.sp.gov.br/sites/cds_equipe/Wiki%20CDS/Home.aspx

- Espaço de Trabalho Corporativo (ETC) 
​​O [Espaço de Trabalho Corporativo (ETC)](http://etc.intra.fazenda.sp.gov.br/Paginas/ETC.aspx) é um serviço de Tecnologia da Informação (TI) para produtividade, facilitando a atuação coletiva de  funcionários de diversas áreas em trabalhos de rotina, em projetos ou em grupos de estudo.​​​
Veja alguns documentos em:
http://etc.intra.fazenda.sp.gov.br/sites/suporte_desenv/Documentos%20Compartilhados/Forms/AllItems.aspx)

- [Portal TFS](http://portaltfs.intra.fazenda.sp.gov.br/vox/default.aspx)
Repositório para documentação de projetos do antigo TFS, em processo de substituição pelas Wikis no ads.
Há diversos documentos disponíveis em  [Guias .NET FW](http://portaltfs.intra.fazenda.sp.gov.br/sites/TPC-2010/Arquitetura_Referencia/Shared%20Documents/Forms/AllItems.aspx?RootFolder=/sites/TPC-2010/Arquitetura_Referencia/Shared%20Documents/Guias&FolderCTID=&View=%7b26F93AC8-C6F0-486B-B97A-B45B212CDB1E%7d) contendo guias para desenvolvimento em dotnet FW, para sistemas legados.

> Os portais estão sendo substituídos gradativamente pelas wikis no ads.

##Desenvolvimento
Veja, nessa wiki, páginas com orientações sobre diversos assuntos de interesse para desenvolvimento de aplicações.

###Kit de boas vindas a novos desenvolvedores
Está disponível o [Kit de boas vindas a novos desenvolvedores](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Desenvolvimento/Kit-de-Boas-Vindas-a-novos-desenvolvedores-do-DTI). 

###Padrões de Desenvolvimento
Conheça os [padrões de desenvolvimento de aplicações na Sefaz-SP](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Desenvolvimento/Padrões-de-desenvolvimento).

###ADS
O [ADS - Azure DevOps Server](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/ADS) é um sistema de gerenciamento de código, artefatos, documentação, dashboards e backlog dos projetos de desenvolvimento adotado pela Sefaz. 
###Configuração de segurança do ads
Como [configurar as permissões de acesso e perfis](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/ADS/Configuração-de-segurança-do-Azure-Devops-Server).

###Nuget
Como [utilizar pacotes compartilhados de código](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/ADS/Recursos-do-Azure-Devops/Artifacts) em aplicações desenvolvidas na Sefaz.

   >- [Migração de projetos entre Collections](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/ADS/Migração-de-projetos-ADS-para-Collections-Inherited) - Os projetos no TPC-2016 serão todos migrados para a nova Collection PRODUTOS.
   >- [Deploy automatizado](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/ADS/Deploy-automatizado) - do código ao deploy, com pouca intervenção humana, com mais agilidade e segurança.

###Git
Veja [orientações para utilização do Git](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Desenvolvimento/Git) na Sefaz-SP.

>TODOS os projetos que ainda estão utilizando o [TFVC (Team Foundation Version Control)](https://docs.microsoft.com/en-us/azure/devops/repos/tfvc/what-is-tfvc?view=azure-devops-2020) deverão [converter o código para GIT](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Desenvolvimento/Git/Migração-de-controlador-de-versão-%2D-TFVC-para-o-Git).

###Sefaz Identity
O [Sefaz Identity](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Recursos/Sefaz-Identity) é o serviço oficial de autenticação de sistemas desenvolvimento na Secretaria da Fazenda de São Paulo.

###Log audit
O [LogAudit](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Recursos/LogAudit) é o sistema da Sefaz para registro de log de aplicações.

###Treinamentos
Consulte alguns treinamentos sugeridos. [Veja mais...](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Desenvolvimento/Treinamentos)


## Operações
- [Procedimentos na infraestrutura do DTI](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Operações).

##Governança
  - [SAFe](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Governança/SAFe-%2D-Scaled-Agile-Framework)
  - [Desenho de Solucão](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Arquitetura/Desenho-de-Solucão)
  - [Maturidade de Sistemas](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Arquitetura/Maturidade-de-Sistemas)
  - [Desenvolvimento Seguro](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Segurança-da-Informação/Desenvolvimento-Seguro)
  - [Processos do DTI](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Governança/Processos-do-DTI)

## Arquitetura
  - [Arquitetura corporativa](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Arquitetura/Arquitetura-corporativa)
  - [DevOps](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Arquitetura/DevOps)
  - [Gerenciamento de APIs](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Arquitetura/API/Gerenciamento-de-APIs)
  - [Gestão e modelagem de dados](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Gestão-e-modelagem-de-dados)  

---

# **Comunicações**

##Comunicados através de e-mail
Algumas informações sobre Arquitetura e Padrões do DTI são [comunicadas através de mensagens de e-mail](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Suporte/Comunicados-através-de-e%2Dmail). 
Os destinatários dos comunicados, em geral, são os usuários cadastrados no grupo de emails **Usuarios_ADS**.

Se quiser receber nossos comunicados, abra um chamado no [Suporte_DevOps](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Suporte_DevOps/_workitems/create/Product%20Backlog%20Item?templateId=f2327198-0479-4896-a596-9473509abab7&ownerId=edf1fe16-ac08-4f98-bbbc-e06e32d9686e) (área desenvolvimento) solicitando sua inclusão nesse grupo de emails.

>Se desejar ser excluído desse grupo, utilize o "[Console do Solicitante](https://sefaznetvirtual.intra.fazenda.sp.gov.br/arsys/forms/arsapp/SRS:ServiceRequestConsole)" do Remedy​. 
Procure por "Grupo de E-mail - Alterações". 
Informe a conta "Usuarios_ADS", organização "CA", departamento "DTI", clique em "Exclusão" e informe seu login.

## Dúvidas ou auxílio
- [Atendimento de infraestrutura](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Operações/Solicitações-de-infraestrutura)
- Demais assuntos no [Suporte_DevOps](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Suporte_DevOps).

##Reuniões
O CIA [registra as reuniões](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Suporte/Reuniões) entre equipe de arquitetura com o CDS. 

---
      Envie sugestões sobre essa Wiki para dti_cia@fazenda.sp.gov.br


