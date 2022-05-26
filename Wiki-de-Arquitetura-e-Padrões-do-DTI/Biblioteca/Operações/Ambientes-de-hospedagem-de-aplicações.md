O DTI mantém três ambientes para hospedagem de projetos de desenvolvimento interno. 
- DEV/QA - Acessível para gerenciamento por desenvolvedores, exceto a criação de BD, que deve ser feita por RDM.
- HML - Gerenciamento solicitado por RDM 
- PRD - Gerenciamento solicitado por RDM. 

Recomenda-se que não haja referências cruzadas entre ambientes, exceto quando não houver alternativas.

#Banco de dados
##SQL Server
- DEV: SRVDES01\DES01
- HML: SRVHML01\HML01
- PRD: SRVCORP01\CORP01 ou SRVCORP02\CORP02

##Oracle
- DEV - consulte o DTIDBA
- HML - consulte o DTIDBA
- PRD - consulte o DTIDBA
  
#Servidores Windows
- DEV 
  - Servidor web/serviços: webdesenv
- HML
  - Servidor web: webhomolog
  - Servidor serviços: webserviceshml
- PRD
  - Servidor web/serviços: consulte o DTIWindows

#Docker-Kubernetes
- DEV
  - k8s-dev
- HML
  - k8s-hml
- PRD
  - k8s

Veja mais no [projeto Kubernetes_Sefaz](https://ads.intra.fazenda.sp.gov.br/tfs/TPC-2016/Kubernetes_Sefaz/_wiki/wikis/Kubernetes_Sefaz.wiki/1069/Kubernetes-Sefaz).


#Linux
Consulte o DTILinux para informações sobre a infraestrutura ou abra um chamado no [[Suporte_DevOps]](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Suporte_DevOps) (área Infraestrutura) solicitando auxílio.

#Outras plataformas
Consulte o COI para informações sobre outras plataformas de desenvolvimento ou abra um chamado no [[Suporte_DevOps]](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Suporte_DevOps) (área Infraestrutura) solicitando auxílio.