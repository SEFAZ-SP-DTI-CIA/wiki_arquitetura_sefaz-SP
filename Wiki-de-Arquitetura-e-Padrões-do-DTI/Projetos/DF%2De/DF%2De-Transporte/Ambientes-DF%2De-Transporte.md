
## Servidores

### Homologação
Web: SRV10642 e SRV10643
Serviço (somente CTe): SRV11489 e SRV11490

### Produção
Web: SRV10814, SRV10815, SRV10816, SRV10817, SRV10818, SRV10819, SRV11079 e SRV11080
Serviço (somente CTe): SRV11544, SRV11545, SRV11546, SRV11547, SRV11548, SRV11549, SRV11550 e SRV11551

| **CT-e**                                | **BP-e**                         |
|-----------------------------------------|----------------------------------|
|||
| **DESENVOLVIMENTO**                     |**DESENVOLVIMENTO**               |
| https://localhost/CTeWeb/services       | https://localhost/BPeWeb/services|
| https://localhost/CTeConsulta           | https://localhost/BPe/consulta   |
| https://localhost/CTeAdmin              | https://localhost/BPeAdmin       |
| https://localhost/CTeIntegraWeb/Services|                                  |
|||
| **HOMOLOGAÇÃO**                         | **HOMOLOGAÇÃO**                  |
| https://homologacao.nfe.fazenda.sp.gov.br/CTeWeb/services       | https://homologacao.bpe.fazenda.sp.gov.br/BPeWeb/services|
| https://homologacao.nfe.fazenda.sp.gov.br/CTeConsulta       | https://homologacao.bpe.fazenda.sp.gov.br/BPe/consulta|
| https://homologacao.nfe.fazenda.sp.gov.br/CTeAdmin       | https://homologacao.bpe.fazenda.sp.gov.br/BPeAdmin |
| https://homologacao.nfe.fazenda.sp.gov.br/CTeIntegraWeb/Services       | |
|        | Testes (alteração no arquivo hosts não funciona)|
|        | SRV10642 - https://10.216.38.37:8443/|
|        | SRV10643 - https://10.216.38.38:8443/|
|||
| **PRODUÇÃO**                            | **PRODUÇÃO**                     |
| https://nfe.fazenda.sp.gov.br/CTeWeb/services       | https://bpe.fazenda.sp.gov.br/BPeWeb/services|
| https://nfe.fazenda.sp.gov.br/CTeConsulta       | https://bpe.fazenda.sp.gov.br/BPe/consulta|
| https://nfe.fazenda.sp.gov.br/CTeAdmin       | https://bpe.fazenda.sp.gov.br/BPeAdmin|
| https://nfe.fazenda.sp.gov.br/CTeIntegraWeb/Services       | |
| | |
| **WebSvc. CTeWeb**                      | Testes (alteração no arquivo hosts não funciona)|
| CteConsulta.asmx       | SRV10814 - https://10.216.38.48:8443/|
| CTeConsultaCred.asmx   | SRV10815 - https://10.216.38.49:8443/|
| CteConsultaDFe.asmx    | SRV10816 - https://10.216.38.50:8443/|
| CteInutilizacao.asmx   | SRV10817 - https://10.216.38.51:8443/|
| CteLiberaEPEC.asmx     | SRV10818 - https://10.216.38.52:8443/|
| CteRecepcao.asmx       | SRV10819 - https://10.216.38.53:8443/|
| CteRecepcaoEvento.asmx | SRV11079 - https://10.216.38.115:8443/|
| CteRecepcaoGTVe.asmx   | SRV11080 - https://10.216.38.116:8443/|
| CteRecepcaoOS.asmx       | |
| CteRetRecepcao.asmx      | |
| CteStatusServico.asmx    | **WebServices**|
| | BPeRecepcao.asmx|
| **WebService - CTeIntegraWeb**  | BPeRecepcaoTM.asmx        |
| CTeDistNSUAut.asmx  | BPeConsulta.asmx        |
|   | BPeRecepcaoEvento.asmx        |
| **WindowsServices**  | BPeStatusServico.asmx  |
| Sefaz.CTeService  |   |





