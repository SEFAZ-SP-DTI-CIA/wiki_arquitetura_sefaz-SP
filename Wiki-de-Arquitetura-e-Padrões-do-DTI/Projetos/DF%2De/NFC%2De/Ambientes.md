### Desenvolvimento

 * SRV72069 Report Server 2008 CAS DESEN
   * Web Sites:
     * Portal: http://dev-nfce-epec.intra.fazenda.sp.gov.br/NFCePortal
     * Consulta Pública: https://dev-nfce-epec.intra.fazenda.sp.gov.br/NFCeConsultaPublica
     * Consulta Inutilização: https://dev-nfce-epec.intra.fazenda.sp.gov.br/NFCeConsultaPublica?c=i
     * Site Contribuinte: https://dev-nfce-epec.intra.fazenda.sp.gov.br/NFCeSiteContribuinte
     * Administação EPEC: https://dev-nfce-epec.intra.fazenda.sp.gov.br/NFCeAdministracaoServicoEPEC
     * Portal Fazendario: https://dev-nfce-epec.intra.fazenda.sp.gov.br/NFCePortalFazendario
   * Windows Service:
     * Sefaz.NFCeWindowsService
     * Sefaz.NFCeLCRService
     * Sefaz.NFCeVerificacaoSAT
     * Sefaz.NFCeAtivacaoEPEC
     * Sefaz.NFCeBloqueioEPEC
     * Sefaz.NFCeConciliacaoEPEC
   * Web Services
     * https://dev-nfce-epec.intra.fazenda.sp.gov.br/ws/NFeRetAutorizacao4.asmx 
     * https://dev-nfce-epec.intra.fazenda.sp.gov.br/ws/NFeAutorizacao4.asmx 
     * https://dev-nfce-epec.intra.fazenda.sp.gov.br/ws/NFeRecepcaoEvento4.asmx
     * https://dev-nfce-epec.intra.fazenda.sp.gov.br/ws/NFeInutilizacao4.asmx
     * https://dev-nfce-epec.intra.fazenda.sp.gov.br/ws/NFeConsultaProtocolo4.asmx
     * https://dev-nfce-epec.intra.fazenda.sp.gov.br/ws/NFeStatusServico4.asmx​
     * https://dev-nfce-epec.intra.fazenda.sp.gov.br/ws/NFeDiagnosticoServico.asmx
     * https://dev-nfce-epec.intra.fazenda.sp.gov.br/EPECws/EPECStatusServico.asmx
     * https://dev-nfce-epec.intra.fazenda.sp.gov.br/EPECws/RecepcaoEPEC.asmx
     * https://dev-nfce-epec.intra.fazenda.sp.gov.br/EPECws/EPECDiagnosticoServico.asmx
   * WCF (3 camadas): net.tcp://dev-nfce-epec.intra.fazenda.sp.gov.br​/NFCe.WcfServiceApplication/NFCeService.svc
   * WCF Consulta NFC-e: net.tcp://dev-nfce-epec.intra.fazenda.sp.gov.br​/
   * DW: `C:\ProgramFiles...\Br.Gov.Sp.Fazenda.DFeDW.NFCeOutConsole.exe`
   * Banco de dados: srvnfedes01

### Testes da DI

 * WCF: SRV72066
   * WCF: `net.tcp://dev-webdescas1intra.fazenda.sp.gov.br/NFCe.WcfServiceApplication/NFCeService.svc`
 * Web Services: SRV72067
   * https://dev-webdescas2.intra.fazenda.sp.gov.br/ws/NFeRetAutorizacao4.asmx
   * https://dev-webdescas2.intra.fazenda.sp.gov.br/ws/NFeAutorizacao4.asmx
   * https://dev-webdescas2.intra.fazenda.sp.gov.br/ws/NFeRecepcaoEvento4.asmx
   * https://dev-webdescas2.intra.fazenda.sp.gov.br/ws/NFeInutilizacao4.asmx
   * https://dev-webdescas2.intra.fazenda.sp.gov.br/ws/NFeConsultaProtocolo4.asmx
   * https://dev-webdescas2.intra.fazenda.sp.gov.br/ws/NFeDiagnosticoServico.asmx
   * https://dev-webdescas2.intra.fazenda.sp.gov.br/ws/NFeStatusServico4.asmx
   * https://dev-webdescas2.intra.fazenda.sp.gov.br/EPECws/EPECStatusServico.asmx
   * https://dev-webdescas2.intra.fazenda.sp.gov.br/EPECws/RecepcaoEPEC.asmx
   * https://dev-webdescas2.intra.fazenda.sp.gov.br/EPECws/EPECDiagnosticoServico.asmx
 * WebSites: SRV72067
   * Portal: http://dev-webdescas2.intra.fazenda.sp.gov.br/NFCePortal
   * Consulta Pública: https://dev-webdescas2.intra.fazenda.sp.gov.br/NFCeConsultaPublica
   * Consulta Inutilização: https://dev-webdescas2.intra.fazenda.sp.gov.br/NFCeConsultaPublica?c=i
   * Site Contribuinte: https://dev-webdescas2.intra.fazenda.sp.gov.br/NFCeSiteContribuinte
   * Administação EPEC: https://dev-webdescas2.intra.fazenda.sp.gov.br/NFCeAdministracaoServicoEPEC
   * Portal Fazendario: https://dev-webdescas2.intra.fazenda.sp.gov.br/NFCePortalFazendario
 * Windows Service: SRV72068
   * Sefaz.NFCeWindowsService
   * Sefaz.NFCeLCRService
   * Sefaz.NFCeVerificacaoSAT
   * Sefaz.NFCeAtivacaoEPEC
   * Sefaz.NFCeBloqueioEPEC
   * Sefaz.NFCeConciliacaoEPE
   * DW: `C:\ProgramFiles...\Br.Gov.Sp.Fazenda.DFeDW.NFCeOutConsole.exe`
 * Banco de dados: srvdes01
### Homologação.
 * Web: SRV72106, SRV72107 (10.217.10.52)
   * WCF: `net.tcp://nfcewshml.intra.fazenda.sp.gov.br/NFCe.WcfServiceApplication/NFCeService.svc`
   * Portal Fazendario: https://nfcewshml.intra.fazenda.sp.gov.br/NFCePortalFazendario
 * Web: SRV72085 SRV72086 SRV72087 SRV72088 
   * WebSites
     * Consulta Pública: https://www.homologacao.nfce.fazenda.sp.gov.br/NFCeConsultaPublica
     * Consulta Inutilização: https://www.homologacao.nfce.fazenda.sp.gov.br/NFCeConsultaPublica?c=i
     * Site Contribuinte: https://www.homologacao.nfce.fazenda.sp.gov.br/NFCeSiteContribuinte
     * Portal (agora o portal é o de produção)
   * Web Services:
     * https://homologacao.nfce.fazenda.sp.gov.br/ws/NFeRetAutorizacao4.asmx
     * https://homologacao.nfce.fazenda.sp.gov.br/ws/NFeAutorizacao4.asmx
     * https://homologacao.nfce.fazenda.sp.gov.br/ws/NFeRecepcaoEvento4.asmx
     * https://homologacao.nfce.fazenda.sp.gov.br/ws/NFeInutilizacao4.asmx
     * https://homologacao.nfce.fazenda.sp.gov.br/ws/NFeConsultaProtocolo4.asmx
     * https://homologacao.nfce.fazenda.sp.gov.br/ws/NFeStatusServico4.asmx
     * https://homologacao.nfce.fazenda.sp.gov.br/ws/NFeDiagnosticoServico.asmx
 * WebSite Administração EPEC: webhomolog (SRV11051 / SRV11052)
     * https://webhomolog.intra.fazenda.sp.gov.br/NFCeAdministracaoServicoEPEC/
 * Web Services EPEC: SRV11030 SRV11031
     * ​https://homologacao.nfce.epec.fazenda.sp.gov.br/EPECws/RecepcaoEPEC.asmx
     * https://homologacao.nfce.epec.fazenda.sp.gov.br/EPECws/EPECStatusServico.asmx
     * https://homologacao.nfce.epec.fazenda.sp.gov.br/EPECws/EPECDiagnosticoServico.asmx
 * Windows Services Autorização
     * Sefaz.NFCeWindowsService: SRV72089 SRV72090 SRV72091 SRV72092
 * Windows Service LCR e SAT: SRV72100 / SRV72108 (cluster SRVCS81) 
     * Sefaz.NFCeLCRService
     * Sefaz.NFCeVerificacaoSAT
 * Windows Services EPEC / DW: SRV11026 SRV11027 (cluster SRVCS88EPEC)
     * Sefaz.NFCeAtivacaoEPEC
     * Sefaz.NFCeBloqueioEPEC
     * Sefaz.NFCeConciliacaoEPEC:
     * `DW: C:\ProgramFiles...\Br.Gov.Sp.Fazenda.DFeDW.NFCeOutConsole.exe`
 * Banco de dados:
     * Servidores
       * NFC-e:  SRV11469 e SRV11470​ (era SRV70037 e SRV70053)
       * EPEC: SRV11028 e SRV11029
       * CADESP:
         * SP: SRV11049 e SRV11050
         * CAS: SRV70027 e SRV70043
     * Cluster SRVCS82
     * NFC-e: LST_NFCE01HML (R/W), SRVEPEC01HMLCAS (R/O)
     * EPEC: LST_EPEC01HML = SRVEPEC01HML (R/W), SRVNFCE01HML (R/O)
### Produção
 * Web: SRV72139, SRV72140 (10.217.32.111)
   * WCF: `net.tcp://nfcews.intra.fazenda.sp.gov.br/NFCe.WcfServiceApplication/NFCeService.svc`
   * Portal Fazendario: https://nfcews.intra.fazenda.sp.gov.br/NFCePortalFazendario
 * Web: SRV72131 SRV72132 SRV72133 SRV72134 
   * WebSites
     * Consulta Pública: https://www.nfce.fazenda.sp.gov.br/NFCeConsultaPublica
     * Consulta Inutilização: https://www.nfce.fazenda.sp.gov.br/NFCeConsultaPublica?c=i
     * Site Contribuinte: https://www.nfce.fazenda.sp.gov.br/NFCeSiteContribuinte
     * Portal: http://www.nfce.fazenda.sp.gov.br/NFCePortal (também terá acesso à homologação)
   * Web Services:
     * https://nfce.fazenda.sp.gov.br/ws/NFeRetAutorizacao4.asmx
     * https://nfce.fazenda.sp.gov.br/ws/NFeAutorizacao4.asmx
     * https://nfce.fazenda.sp.gov.br/ws/NFeRecepcaoEvento4.asmx
     * https://nfce.fazenda.sp.gov.br/ws/NFeInutilizacao4.asmx
     * https://nfce.fazenda.sp.gov.br/ws/NFeConsultaProtocolo4.asmx
     * https://nfce.fazenda.sp.gov.br/ws/NFeStatusServico4.asmx
     * https://nfce.fazenda.sp.gov.br/ws/NFeDiagnosticoServico.asmx
 * WebSite Administração EPEC: sefaznet9 (SRV10751-52 e SRV11071​)
   * https://sefaznet9.sede.fazenda.sp.gov.br/NFCeAdministracaoServicoEPEC/
 * Web Services EPEC: SRV11083 SRV11084 SRV11085 SRV11086
   * https://nfce.epec.fazenda.sp.gov.br/EPECws/RecepcaoEPEC.asmx
   * https://nfce.epec.fazenda.sp.gov.br/EPECws/EPECStatusServico.asmx
   * https://nfce.epec.fazenda.sp.gov.br/EPECws/EPECDiagnosticoServico.asmx
 * Windows Services Autorização
   * Sefaz.NFCeWindowsService: SRV72135 SRV72136 SRV72137 SRV72138
 * Windows Services LCR e SAT: SRV72141 / SRV72142 (cluster SRVCS87)
   * Sefaz.NFCeLCRService
   * Sefaz.NFCeVerificacaoSAT
 * Windows Services EPEC / DW: SRV11081 SRV11082 (cluster SRVCS91)
   * Sefaz.NFCeAtivacaoEPEC
   * Sefaz.NFCeBloqueioEPEC
   * Sefaz.NFCeConciliacaoEPEC
   * DW: `D:\ProgramFiles (x86)\SEFAZ_SP\NFCeExtratorDW\Br.Gov.Sp.Fazenda.DFeDW.NFCeOutConsole.exe`
 * Banco de dados:
   * Servidores
     * NFC-e: SRV70055 e SRV70058
     * EPEC: SRV11087 SRV​11088
     * CADESP:
       * SP: SRV11049 e SRV11050
       * CAS: SRV70027 e SRV70043
   * NFC-e: LST_NFCE01 (R/W), SRVEPEC01CAS (R/O)
   * EPEC: LST_EPEC01 = SRVEPEC01 (R/W), SRVNFCE01 (R/O)tps://lalala.com.br