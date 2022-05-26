A infraestrutura da Sefaz oferece uma grande quantidade de servidores em ambiente Windows e Linux.
Relacionam-se nessa wiki algumas recomendações de servidores compartilhados para hospedagem de aplicações.

Para hospedagem de aplicações em servidores exclusivos, consulte a equipe Windows através de email para dtiwinweb@fazenda.sp.gov.br

[[_TOC_]]

#Web .Net (core)
Servidores WEB em plataforma Windows Server 2016 com .Net (antigo dotnet core) instalados.

##Desenvolvimento
O ambiente de desenvolvimento não prevê acesso externo à rede da Sefaz, porém, são oferecidos dois servidores para publicação de aplicações web, para simulação de acesso intranet e internet.

- Aplicação Web para simular acesso pela internet:
    - Endereço: webdesenv4.intra.fazenda.sp.gov.br
    - Server name: SRV72445
    - IP: 10.217.13.69
- Aplicação Web para simular acesso pela intranet:
    - Endereço: webintradesenv4.intra.fazenda.sp.gov.br
    - Server name: SRV72446
    - IP: 10.217.13.70
- Serviço Web (API)
    - Endereço: webservicesdes4.intra.fazenda.sp.gov.br
    - Server name: SRV72447
    - IP: 10.217.13.71

##Testes
O ambiente de testes (QA - Quality Assurance) da Sefaz oferece apenas um servidor de acesso interno para aplicações web.

- Aplicação Web:
    - Endereço: webtst4.intra.fazenda.sp.gov.br
    - Server name: SRV72454
    - IP: 10.217.11.46
- Serviço Web (API)
    - Endereço: webservicestst4.intra.fazenda.sp.gov.br
    - Server name: SRV72455 
    - IP: 10.217.11.47

##Homologação
A partir desse ambiente, são oferecidos servidores em balance para acesso interno e externo, buscando reproduzir o ambiente de produção, quando possível.

- Aplicação Web para acesso pela intranet:
    - Endereço: webhomolog4.intra.fazenda.sp.gov.br
    - IP Balance: 10.215.11.64
    - Balance names: nlb
    - Servidores: 
      - SRV11757 - IP: 10.215.11.65
      - SRV11758 - IP: 10.215.11.66
- Aplicação Web para acesso pela internet:
    - Endereço: webhomolog4.fazenda.sp.gov.br
    - IP Balance: 10.216.38.181
    - Balance names: vcmpsp-dmzctb e vcmp2sp-dmzctb
    - Servidores: 
      - SRV11766 – IP: 10.216.38.182
      - SRV11767 – IP: 10.216.38.183
- Serviço Web (API) para acesso pela intranet:
    - Endereço: webserviceshml4.intra.fazenda.sp.gov.br
    - IP Balance: 10.215.11.67
    - Balance names: nlb
    - Servidores: 
      - SRV11759 - IP: 10.215.11.68
      - SRV11760 - IP: 10.215.11.69
- Serviço Web (API) para acesso pela internet:
    - Endereço: webserviceshml4.fazenda.sp.gov.br
    - IP Balance: 10.216.67.80
    - Balance names: vcmpsp-dmzctb e vcmp2sp-dmzctb
    - Servidores: 
      - SRV11768 - IP: 10.216.67.81
      - SRV11769 - IP: 10.216.67.82

##Produção
O ambiente de produção também oferece acesso interno e externo às aplicações.

- Aplicação Web para acesso pela intranet:
    - Endereço: sefaznet4.intra.fazenda.sp.gov.br
    - IP Balance: 10.216.84.22
    - Balance names: [consultar]
    - Servidores: 
      - SRV11770 -IP: 10.216.84.23
      - SRV11771 -IP: 10.216.84.24
- Aplicação Web para acesso pela internet:
    - Endereço: www4.fazenda.sp.gov.br
    - IP virtual (Balance): 10.216.67.70
    - Balance names: vcmpsp-dmzctb e vcmp2sp-dmzctb
    - Servidores: 
      - SRV11780 -IP: 10.216.67.71
      - SRV11781 -IP: 10.216.67.72
      - SRV11782 -IP: 10.216.67.73
      - SRV11783 -IP: 10.216.67.74
- Serviço Web (API) para acesso pela intranet:
    - Endereço: webservices4.intra.fazenda.sp.gov.br
    - IP virtual (Balance): 10.216.68.210
    - Balance names: vcmpsp-appctb e vcmp2sp-appctb
    - Servidores: 
      - SRV11772 – IP: 10.216.68.211
      - SRV11773 – IP: 10.216.68.212
- Serviço Web (API) para acesso pela internet:
    - Endereço: webservices4.fazenda.sp.gov.br
    - IP Externo: 201.55.62.82
    - IP virtual (Balance): 10.216.38.184
    - Balance names: vcmpsp-dmzctb e vcmp2sp-dmzctb
    - Servidores: 
      - SRV11784 – IP: 10.216.38.185
      - SRV11785– IP: 10.216.38.186
