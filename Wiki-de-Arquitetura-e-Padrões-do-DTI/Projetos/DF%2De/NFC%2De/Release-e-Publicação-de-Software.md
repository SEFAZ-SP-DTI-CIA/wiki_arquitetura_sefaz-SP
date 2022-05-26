
[[_TOC_]]

# Templates das Tarefas Windows nas RDMs usando Script Powershell (Hml e Prod)

0) No caso de Produção, antes de iniciar, enviar e-mail para 'monitoracao_lideres@fazenda.sp.gov.br' avisando que haverá ligação manual do EPEC

1) Copiar a pasta dos scripts (\\bmd\cds\NFC-e\scripts_de_deploy) para a máquina do analist e executar no Powershell o seguinte script:
`> .\DownloadFromAzurePipeline -drop <número> -amb <ambiente>`

2) Colocar em manutenção as máquinas listadas abaixo:
<Lista de máquinas>

3) Entrar em contato com os desenvolvedores Fábio Nagamine e Francisco Blasi

4) Para cada máquina do deploy, fazer a sequência:
4.1) Tirar do balance ou mover cluster, conforme necessário
4.2) Executar script \\bmd\cds\NFC-e\scripts_de_deploy\DeployNFCe.ps1 <X>
4.2.1) Caso o script esteja rodando da BMD, <X> = número da RDM
4.2.2) Caso o script esteja na pasta descompactada, <X> = . (ponto)
4.3) Aguardar testes dos desenvolvedores
4.4) Colocar de volta no balance ou mover cluster, conforme necessário

Lista de máquinas:

Homologação:

WCF/Portal Faz: SRV72106, SRV72107
Web DMZ: SRV72085 SRV72086 SRV72087 SRV72088 
NFCeWindowsService: SRV72089 SRV72090 SRV72091 SRV72092
LCR e SAT: SRV72100 / SRV72108 (cluster SRVCS81) 

DMZ EPEC:  SRV11030 SRV11031
Serviços ABCD: SRV11026 SRV11027 (cluster SRVCS88EPEC)
EPEC Admin: webhomolog (SRV11051 / SRV11052)


Produção:

WCF/Portal Faz: SRV72139, SRV72140
Web DMZ: SRV72131 SRV72132 SRV72133 SRV72134 
NFCeWindowsService: SRV72135 SRV72136 SRV72137 SRV72138
LCR e SAT: SRV72141 / SRV72142 (cluster SRVCS87)

DMZ EPEC: SRV11083 SRV11084 SRV11085 SRV11086
Serviços ABCD: SRV11081 SRV11082 (cluster SRVCS91)
EPEC Admin: sefaznet9 (SRV10751-52 e SRV11071)

# Tarefas no caso de RDM manual

## Instruções para instalação de Windows Service
(Feita somente uma vez por máquina. Após a primeira instalação, apenas substituir os arquivos nas atualizações seguintes)

- Trocar os termos entre "<>" de acordo com cada serviço.
- Criar um arquivo "INSTRUCOES.txt" com o texto abaixo.
- Copiar o arquivo "INSTRUCOES.txt" junto com os arquivos para instalação
<br/><p>INSTRUCOES.txt
--------------
1) criar a pasta local "D:\Program Files (x86)\SEFAZ\<nome do serviço>", onde o serviço será instalado

2) copiar conteúdo desta pasta para a pasta "D:\Program Files (x86)\SEFAZ\<nome do serviço>"

3) no prompt de comando, com permissão de ADMINISTRADOR:

3.1) ir para a pasta do serviço
> cd "D:\Program Files (x86)\SEFAZ\<nome do serviço>"

3.2) Instalar o serviço usando installutil:
> C:\Windows\Microsoft.NET\Framework\v4.0.30319\installutil <arquivo executável do serviço>

4) Subir o serviço "<nome do serviço>" na lista de serviços do Windows, OU usando o comando abaixo:

> net start <nome do serviço>
--------------

## RDM com tarefa manual de Homologação

### Tarefas DTI Windows Homologação

Algum dos seguintes developers do CDS deve acompanhar (exceto o item (A), backup, que pode ser feito sem acompanhamento): Fábio Nagamine, Francisco Blasi Jr. ou Rafael Assunção.
 
Máquinas envolvidas no deploy:
- NFC-e: SRV72085, SRV72086, SRV72087, SRV72088, SRV72089, SRV72090, SRV72091, SRV72092, SRV72106, SRV72107, SRV72100, SRV72108
- EPEC: SRV11026, SRV11027, SRV11030, SRV11031, webhomolog (SRV11051, SRV11052 e possivelmente outras)
 
A) Realizar back-up da versão atual da aplicação em cada servidor:
 
A.1) SRV72085, SRV72086, SRV72087, SRV72088:
- https://homologacao.nfce.fazenda.sp.gov.br/ws
- https://www.homologacao.nfce.fazenda.sp.gov.br/NFCeSiteContribuinte
- https://www.homologacao.nfce.fazenda.sp.gov.br/NFCeConsultaPublica/
 
A.2) SRV72089, SRV72090, SRV72091, SRV72092
- serviço Sefaz.NFCeWindowsService
 
A.3) SRV72106 e SRV72107
- net.tcp://nfcewshml.intra.fazenda.sp.gov.br/NFCe.WcfServiceApplication
- https://nfcewshml.intra.fazenda.sp.gov.br/NFCePortalFazendario
 
A.4) SRV72100 e SRV72108
- serviço Sefaz.NFCeLCRService
- serviço Sefaz.NFCeVerificacaoSAT
 
A.5) SRV11026 e SRV11027
- serviço Sefaz.NFCeConciliacaoEPEC
- serviço Sefaz.NFCeBloqueioEPEC
- serviço Sefaz.NFCeAtivacaoEPEC
- executável do DW no diretório D:\Program Files (x86)\SEFAZ_SP\NFCeExtratorDW 
 
A.6) SRV11030 e SRV11031
- https://homologacao.nfce.epec.fazenda.sp.gov.br/EPECws
 
A.7) webhomolog (SRV11051, SRV11052 e possivelmente outras)
- https://webhomolog.intra.fazenda.sp.gov.br/NFCeAdministracaoServicoEPEC/
 
-- Inicio procedimento NFC-e
 
B) Após a autorização dos developers (que deverão aguardar o item 1 dos DBAs), fazer os procedimentos a seguir, na ordem:
B.1) parar os applications pool das aplicações da NFC-e das máquinas web SRV72085, SRV72086, SRV72087, SRV72088
B.2) parar os applications pool das aplicações da NFC-e das máquinas web SRV72106 e SRV72107
B.3) parar o serviço Sefaz.NFCeWindowsService nas máquinas SRV72089, SRV72090, SRV72091, SRV72092
B.4) parar o cluster do serviço Sefaz.NFCeLCRService e Sefaz.NFCeVerificacaoSAT nas máquinas SRV72100 e SRV72108
 
C) Realizar as atualizações:
Observação: em todas as atualizações, sempre apagar o diretório destino antes de copiar o novo código.
 
C.1) Windows-Services
Atualizar o serviço Sefaz.NFCeWindowsService nas máquinas de Windows Service da NFC-e (SRV72089, SRV72090, SRV72091, SRV72092) , de acordo com arquivos em
\\caspcsf250802\RDMs\NFC-e\CRQ0000000xxxxx_hml\WindowsService_SRV72089_90_91_92
 
C.2) LCR Services
Atualizar o serviço Sefaz.NFCeLCRService nas máquinas de Windows Service de LCR da NFC-e (SRV72100 e SRV72108), de acordo com arquivos disponibilizados em
\\caspcsf250802\RDMs\NFC-e\CRQ0000000xxxxx_hml\LCRWindowsService_SRV72100_08
 
C.3) Verificação SAT
Atualizar o serviço Sefaz.NFCeVerificacaoSAT nas máquinas de Windows Service de Verificação SAT da NFC-e (SRV72100 e SRV72108) de acordo com arquivos disponibilizados em
\\caspcsf250802\RDMs\NFC-e\CRQ0000000xxxxx_hml\NFCeVerificacaoSAT_WindowsService_SRV72100_08
 
C.4) Web-Services
Atualizar os Web Services da NFC-e (https://homologacao.nfce.fazenda.sp.gov.br/ws) nas máquinas de homologação (SRV72085, SRV72086, SRV72087, SRV72088), de acordo com arquivos em
\\caspcsf250802\RDMs\NFC-e\CRQ0000000xxxxx_hml\WebService_SRV72085_86_87_88
 
C.5) Site do Contribuinte
Atualizar o Site do Contribuinte da NFC-e (https://www.homologacao.nfce.fazenda.sp.gov.br/NFCeSiteContribuinte/) nas máquinas de homologação (SRV72085, SRV72086, SRV72087, SRV72088), de acordo com executáveis disponibilizados em
\\caspcsf250802\RDMs\NFC-e\CRQ0000000xxxxx_hml\NFCeSiteContribuinte_SRV72085_86_87_88
 
C.6) Consulta Pública
Atualizar o Site de Consulta Pública da NFC-e (https://www.homologacao.nfce.fazenda.sp.gov.br/NFCeConsultaPublica/) nas máquinas de homologação (SRV72085, SRV72086, SRV72087, SRV72088), de acordo com executáveis disponibilizados em
\\caspcsf250802\RDMs\NFC-e\CRQ0000000xxxxx_hml\NFCeConsultaPublica_SRV72085_86_87_88
 
C.7) Serviço de WCF
Atualizar o serviço de WCF da NFC-e (net.tcp://nfcewshml.intra.fazenda.sp.gov.br/NFCe.WcfServiceApplication/NFCeService.svc) nas máquinas de homologação (SRV72106 e SRV72107), de acordo com executáveis disponibilizados em
\\caspcsf250802\RDMs\NFC-e\CRQ0000000xxxxx_hml\NFCe.WcfServiceApplication_SRV72106_07
 
C.8) Portal Fazendário
Atualizar o Poral Fazedário da NFC-e (https://nfcewshml.intra.fazenda.sp.gov.br/NFCePortalFazendario) nas máquinas de homologação (SRV72106 e SRV72107) de acordo com executáveis disponibilizados em
\\caspcsf250802\RDMs\NFC-e\CRQ0000000xxxxx_hml\NFCePortalFazendario_srv72106_07
 
C.9) Contadores de performance
Instalar contadores de performance, rodando *com permissão de administrador* o instalador disponível em:
\\caspcsf250802\RDMs\NFC-e\CRQ0000000xxxxx_Hml\ContadoresPerfmon\WebService_SRV72085_86_87_88
\\caspcsf250802\RDMs\NFC-e\CRQ0000000xxxxx_Hml\ContadoresPerfmon\WindowsService_SRV72089_90_91_92
* As máquinas onde os contadores devem ser instalados estão designadas nos nomes de cada pasta.
* Após a instalação dos contadores, verificar a instalação: abrindo o aplicativo "perfmon.exe" e adicionando os contadores do grupo "NFCe" (conferir com o desenvolvedor que estiver acompanhando a instalação).
 
D) Após autorização dos developers (que devem aguardar o item 4 dos DBAs), reiniciar na ordem
D.1) os windows-services Sefaz.NFCeWindowsService (SRV72089, SRV72090, SRV72091, SRV72092)
D.2) os windows-services Sefaz.NFCeLCRService e Sefaz.NFCeVerificacaoSAT (SRV72100 e SRV72108)
D.3) os applicationn pool das aplicações da NFC-e das máquinas web (SRV72106 e SRV72107)
D.4) os applications pool das aplicações da NFC-e das máquinas web (SRV72085, SRV72086, SRV72087, SRV72088)
 
-- Fim procedimento NFC-e
 
-- Inicio procedimento EPEC / DW
 
E) Após a autorização dos developers (que devem aguardar o item 5 de DBA), fazer os procedimentos a seguir, na ordem:
E.1) parar o application pool NFCeAdministracaoServicoEPECPool das máquinas da webhomolog (SRV11051, SRV11052 e possivelmente outras)
E.2) parar o cluster do serviço Sefaz.NFCeConciliacaoEPEC nas máquinas SRV11026 e SRV11027
E.3) parar o cluster do serviço Sefaz.NFCeBloqueioEPEC nas máquinas SRV11026 e SRV11027
E.4) parar o cluster do serviço Sefaz.NFCeAtivacaoEPEC nas máquinas SRV11026 e SRV11027
E.5) parar o application pool das aplicações da NFC-e das máquinas web do EPEC SRV11030 / SRV11031
 
F) Realizar as atualizações:
Observação: em todas as atualizações, sempre apagar o diretório destino antes de copiar o novo código.
 
F.1) Windows-Service Ativação EPEC
Atualizar o serviço Sefaz.NFCeAtivacaoEPEC nas máquinas de Windows Service do EPEC (SRV11026, SRV11027), de acordo com executáveis disponibilizados em:
\\caspcsf250802\RDMs\NFC-e\CRQ0000000xxxxx_hml\WindowsService_AtivacaoEPEC_SRV11026_27
 
F.2) Windows-Service Bloqueio EPEC
Atualizar o serviço Sefaz.NFCeBloqueioEPEC nas máquinas de Windows Service do EPEC (SRV11026, SRV11027), de acordo com executáveis disponibilizados em:
\\caspcsf250802\RDMs\NFC-e\CRQ0000000xxxxx_hml\WindowsService_BloqueioEPEC_SRV11026_27
 
F.3) Windows-Service Conciliação EPEC
Atualizar o serviço Sefaz.NFCeConciliacaoEPEC nas máquinas de Windows Service do EPEC (SRV11026, SRV11027), de acordo com executáveis disponibilizados em:
\\caspcsf250802\RDMs\NFC-e\CRQ0000000xxxxx_hml\WindowsService_ConciliacaoEPEC_SRV11026_27

F.4) DW NFC-e
Atualizar o executável do DW (diretório D:\Program Files (x86)\SEFAZ_SP\NFCeExtratorDW) nas máquinas de homologação (SRV11026 e SRV11027) de acordo com executáveis disponibilizados em
\\caspcsf250802\RDMs\NFC-e\CRQ0000000xxxxx_hml\DW_srv11026_27

F.5) Web-Service EPEC
Atualizar o Web Service do EPEC (https://homologacao.nfce.epec.fazenda.sp.gov.br/EPECws) nas máquinas de homologação (SRV11030, SRV11031), de acordo com executáveis disponibilizados em:
\\caspcsf250802\RDMs\NFC-e\CRQ0000000xxxxx_hml\EPEC_WebService_SRV11030_31
  
F.6) Site Administração do EPEC
Atualizar o Site de Administração do EPEC (https://webhomolog.intra.fazenda.sp.gov.br/NFCeAdministracaoServicoEPEC/) nas máquinas de homologação webhomolog (SRV11051, SRV11052 e possivelmente outras) de acordo com executáveis disponibilizados em
\\caspcsf250802\RDMs\NFC-e\CRQ0000000xxxxx_hml\NFCeAdministracaoServicoEPEC_SRV11051_52
 
F.7) Contadores de performance
Instalar contadores de performance, rodando *com permissão de administrador* o instalador disponível em:
\\caspcsf250802\RDMs\NFC-e\CRQ0000000xxxxx_Hml\ContadoresPerfmon\EPEC_SRV11026_27_30_31
* As máquinas onde os contadores devem ser instalados estão designadas nos nomes de cada pasta.
* Após a instalação dos contadores, verificar a instalação: abrindo o aplicativo "perfmon.exe" e adicionando os contadores do grupo "NFCe" (conferir com o desenvolvedor que estiver acompanhando a instalação).
 
G) Após autorização dos developers (que devem aguardar 9 dos DBAs), reiniciar na ordem
G.1) o application pool NFCeAdministracaoServicoEPECPool das máquinas dar webhomolog (SRV11051, SRV11052 e possivelmente outras)
G.2) os windows-service, pelo cluster, Sefaz.NFCeConciliacaoEPEC, Sefaz.NFCeBloqueioEPEC e Sefaz.NFCeAtivacaoEPEC (SRV11026 e SRV11027)
G.3) o application pool das aplicações da NFC-e das máquinas web do EPEC (SRV11030 e SRV11031)
 
-- Fim procedimento EPEC / DW

### Tarefas DTI Windows Homologação Sem Parar Sistema

Algum dos seguintes developers do CDS deve acompanhar (exceto o item (A), backup, que pode ser feito sem acompanhamento): Fábio Nagamine ou Francisco Blasi Jr.
 
Máquinas envolvidas no deploy:
- NFC-e: SRV72085, SRV72086, SRV72087, SRV72088, SRV72089, SRV72090, SRV72091, SRV72092, SRV72106, SRV72107, SRV72100, SRV72108
- EPEC: SRV11026, SRV11027, SRV11030, SRV11031, webhomolog (SRV11051, SRV11052 e possivelmente outras)
 
A) Realizar back-up da versão atual da aplicação em cada servidor:
 
A.1) SRV72085, SRV72086, SRV72087, SRV72088:
- https://homologacao.nfce.fazenda.sp.gov.br/ws
- https://www.homologacao.nfce.fazenda.sp.gov.br/NFCeSiteContribuinte
- https://www.homologacao.nfce.fazenda.sp.gov.br/NFCeConsultaPublica/
 
A.2) SRV72089, SRV72090, SRV72091, SRV72092
- serviço Sefaz.NFCeWindowsService

 
A.3) SRV72106, SRV72107
- net.tcp://nfcewshml.intra.fazenda.sp.gov.br/NFCe.WcfServiceApplication
- https://nfcewshml.intra.fazenda.sp.gov.br/NFCePortalFazendario
 
A.4) SRV72100 e SRV72108
- serviço Sefaz.NFCeLCRService
- serviço Sefaz.NFCeVerificacaoSAT
 
A.5) SRV11026 e SRV11027
- serviço Sefaz.NFCeConciliacaoEPEC
- serviço Sefaz.NFCeBloqueioEPEC
- serviço Sefaz.NFCeAtivacaoEPEC
- executável do DW no diretório D:\Program Files (x86)\SEFAZ_SP\NFCeExtratorDW
 
A.6) SRV11030 SRV11031 
- https://homologacao.nfce.epec.fazenda.sp.gov.br/EPECws
 
A.7) webhomolog (SRV11051, SRV11052 e possivelmente outras)
- https://webhomolog.intra.fazenda.sp.gov.br/NFCeAdministracaoServicoEPEC 
 
=======  Inicio procedimento NFC-e ======= 
 
B) Realizar as atualizações:
 
Observação 1: em todas as atualizações, sempre apagar o diretório destino antes de copiar o novo código.
 
Observação 2: nas atualizações de web-services ou sites:
- tirar a máquina do balance
- parar o respectivo application pool
- fazer a atualização
- reiniciar o respectivo application pool
- recolocar a máquina no balance
 
Observação 3: nas atualizações de windows-services em cluster
- atualizar o serviço no lado do cluster desligado
- virar o cluster para o lado atualizado
- atualizar o lado recém desligado
 
Observação 4: nas atualizações de windows-services não em cluster
- parar o serviço
- atualizar
- reiniciar o serviço
 
Observação 5: nas atualizações de Contadores de performance, rodar *com permissão de administrador* o instalador disponível. Após a instalação dos contadores, verificar a instalação: abrindo o aplicativo "perfmon.exe" e adicionando os contadores do grupo "NFCe" (conferir com o desenvolvedor que estiver acompanhando a instalação).

-------------------- 

B.1) Máquinas SRV72106 e SRV72107
 
B.1.1) Serviço de WCF
- Atualizar o serviço de WCF da NFC-e (net.tcp://nfcewshml.intra.fazenda.sp.gov.br/NFCe.WcfServiceApplication/NFCeService.svc) nas máquinas de homologação (SRV72106 e SRV72107), de acordo com executáveis disponibilizados em
\\caspcsf250802\RDMs\NFC-e\CRQ0000000xxxxx_hml\NFCe.WcfServiceApplication_SRV72106_07
 
B.1.2) Portal Fazendário
- Atualizar o Poral Fazedário da NFC-e (https://nfcewshml.intra.fazenda.sp.gov.br/NFCePortalFazendario) nas máquinas de homologação (SRV72106 e SRV72107) de acordo com executáveis disponibilizados em
\\caspcsf250802\RDMs\NFC-e\CRQ0000000xxxxx_hml\NFCePortalFazendario_SRV72106_07

B.1.3) TesteMonitoracaoNFCe
- Atualizar o aplicativo TesteMonitoracaoNFCe nas máquinas de homologação (SRV72106 e SRV72107), localizado em D:\Util\Scripts_Monitoracao_scom, de acordo com arquivos em \\caspcsf250802\RDMs\NFC-e\CRQ0000000xxxxx_hml\TesteMonitoracaoNFCe

--------------------


B.2) Máquinas SRV72085, SRV72086, SRV72087, SRV72088
 
B.2.1) Site do Contribuinte
- Atualizar o Site do Contribuinte da NFC-e (https://www.homologacao.nfce.fazenda.sp.gov.br/NFCeSiteContribuinte/) nas máquinas de homologação (SRV72085, SRV72086, SRV72087, SRV72088), de acordo com executáveis disponibilizados em
\\caspcsf250802\RDMs\NFC-e\CRQ0000000xxxxx_hml\NFCeSiteContribuinte_SRV72085_86_87_88
 
B.2.2) Consulta Pública
- Atualizar o Site de Consulta Pública da NFC-e (https://www.homologacao.nfce.fazenda.sp.gov.br/NFCeConsultaPublica/) nas máquinas de homologação (SRV72085, SRV72086, SRV72087, SRV72088), de acordo com executáveis disponibilizados em
\\caspcsf250802\RDMs\NFC-e\CRQ0000000xxxxx_hml\NFCeConsultaPublica_SRV72085_86_87_88
 
B.2.3) Web-Services
- Atualizar os Web Services da NFC-e (https://homologacao.nfce.fazenda.sp.gov.br/ws) nas máquinas de homologação (SRV72085, SRV72086, SRV72087, SRV72088), de acordo com arquivos em
\\caspcsf250802\RDMs\NFC-e\CRQ0000000xxxxx_hml\WebService_SRV72085_86_87_88
- Atualizar contadores de performance de acordo com arquivos em \\caspcsf250802\RDMs\NFC-e\CRQ0000000xxxxx_hml\ContadoresPerfmon\WebService_SRV72085_86_87_88


B.2.4) TesteMonitoracaoNFCe
- Atualizar o aplicativo TesteMonitoracaoNFCe nas máquinas de homologação (SRV72085, SRV72086, SRV72087, SRV72088), localizado em D:\Util\Scripts_Monitoracao_scom, de acordo com arquivos em \\caspcsf250802\RDMs\NFC-e\CRQ0000000xxxxx_hml\TesteMonitoracaoNFCe

--------------------

 
B.3) Máquinas SRV72089, SRV72090, SRV72091, SRV72092
 
B.3.1) Windows-Services
- Atualizar o serviço Sefaz.NFCeWindowsService nas máquinas de Windows Service da NFC-e (SRV72089, SRV72090, SRV72091, SRV72092) , de acordo com arquivos em
\\caspcsf250802\RDMs\NFC-e\CRQ0000000xxxxx_hml\WindowsService_SRV72089_90_91_92
- Atualizar contadores de performance de acordo com arquivos em \\caspcsf250802\RDMs\NFC-e\CRQ0000000xxxxx_hml\ContadoresPerfmon\WindowsService_SRV72089_90_91_92

--------------------

 
B.4) Máquinas SRV72100 e SRV72108
 
B.4.1) LCR Services
- Atualizar o serviço Sefaz.NFCeLCRService nas máquinas de Windows Service de LCR da NFC-e (SRV72100 e SRV72108), de acordo com arquivos disponibilizados em
\\caspcsf250802\RDMs\NFC-e\CRQ0000000xxxxx_hml\LCRWindowsService_SRV72100_08

B.4.2) Verificação SAT

- Atualizar o serviço Sefaz.NFCeVerificacaoSAT nas máquinas de Windows Service de Verificação SAT da NFC-e (SRV72100 e SRV72108), de acordo com arquivos disponibilizados em
\\caspcsf250802\RDMs\NFC-e\CRQ0000000xxxxx_hml\NFCeVerificacaoSAT_WindowsService_SRV72100_08

B.4.3) TesteMonitoracaoNFCe
- Atualizar o aplicativo TesteMonitoracaoNFCe nas máquinas de homologação (SRV72100 e SRV72108), localizado em D:\Util\Scripts_Monitoracao_scom, de acordo com arquivos em \\caspcsf250802\RDMs\NFC-e\CRQ0000000xxxxx_hml\TesteMonitoracaoNFCe


=======  Fim procedimento NFC-e ======= 
 
=======  Inicio procedimento EPEC / DW ======= 
 
C) Realizar as atualizações:
 
Observação 1: em todas as atualizações, sempre apagar o diretório destino antes de copiar o novo código.
 
Observação 2: nas atualizações de web-services ou sites:
- tirar a máquina do balance
- parar o respectivo application pool
- fazer a atualização
- reiniciar o respectivo application pool
- recolocar a máquina no balance
 
Observação 3: nas atualizações de windows-services em cluster
- atualizar o serviço no lado do cluster desligado
- virar o cluster para o lado atualizado
- atualizar o lado recém desligado
 
Observação 4: nas atualizações de Contadores de performance, rodar *com permissão de administrador* o instalador disponível. Após a instalação dos contadores, verificar a instalação: abrindo o aplicativo "perfmon.exe" e adicionando os contadores do grupo "NFCe" (conferir com o desenvolvedor que estiver acompanhando a instalação).

--------------------

 
C.1) Máquinas SRV11026 e SRV11027
 
C.1.1) Windows-Service Ativação EPEC
- Atualizar o serviço Sefaz.NFCeAtivacaoEPEC nas máquinas de Windows Service do EPEC (SRV11026 e SRV11027), de acordo com executáveis disponibilizados em:
\\caspcsf250802\RDMs\NFC-e\CRQ0000000xxxxx_hml\WindowsService_AtivacaoEPEC_SRV11026_27
 
C.1.2) Windows-Service Bloqueio EPEC
- Atualizar o serviço Sefaz.NFCeBloqueioEPEC nas máquinas de Windows Service do EPEC (SRV11026 e SRV11027), de acordo com executáveis disponibilizados em:
\\caspcsf250802\RDMs\NFC-e\CRQ0000000xxxxx_hml\WindowsService_BloqueioEPEC_SRV11026_27
 
C.1.3) Windows-Service Conciliação EPEC
- Atualizar o serviço Sefaz.NFCeConciliacaoEPEC nas máquinas de Windows Service do EPEC (SRV11026 e SRV11027), de acordo com executáveis disponibilizados em:
\\caspcsf250802\RDMs\NFC-e\CRQ0000000xxxxx_hml\WindowsService_ConciliacaoEPEC_SRV11026_27
 
C.1.4) DW NFC-e
- Atualizar o executável do DW (diretório D:\Program Files (x86)\SEFAZ_SP\NFCeExtratorDW) nas máquinas de homologação (SRV11026 e SRV11027) de acordo com executáveis disponibilizados em
\\caspcsf250802\RDMs\NFC-e\CRQ0000000xxxxx_hml\DW_SRV11026_27

--------------------

 
C.2) Máquinas SRV11030 e SRV11031
 
C.2.1) Web-Service EPEC
- Atualizar o Web Service do EPEC (https://homologacao.nfce.epec.fazenda.sp.gov.br/EPECws) nas máquinas de homologação (SRV11030 e SRV11031), de acordo com executáveis disponibilizados em:
\\caspcsf250802\RDMs\NFC-e\CRQ0000000xxxxx_hml\EPEC_WebService_SRV11030_31
- Atualizar contadores de performance de acordo com arquivos disponibilizados em \\caspcsf250802\RDMs\NFC-e\CRQ0000000xxxxx_hml\ContadoresPerfmon\EPEC_SRV11026_27_83_84_85_86

C.2.2) TesteMonitoracaoNFCe
- Atualizar o aplicativo TesteMonitoracaoNFCe nas máquinas de homologação (SRV11030 e SRV11031), localizado em D:\Util\Scripts_Monitoracao_scom, de acordo com arquivos em \\caspcsf250802\RDMs\NFC-e\CRQ0000000xxxxx_hml\TesteMonitoracaoNFCe

--------------------

  
C.3) Máquinas webhomolog (SRV11051, SRV11052 e possivelmente outras)
  
C.3.1) Site Administração do EPEC
- Atualizar o Site de Administração do EPEC (https://webhomolog.sede.fazenda.sp.gov.br/NFCeAdministracaoServicoEPEC/) nas máquinas webhomolog (SRV11051, SRV11052 e possivelmente outras) de acordo com executáveis disponibilizados em
\\caspcsf250802\RDMs\NFC-e\CRQ0000000xxxxx_hml\NFCeAdministracaoServicoEPEC_srv11051_52
 
=======  Fim procedimento EPEC / DW =======

### Tarefas DBA Homologação

-- Início procedimento NFC-e

1) Desligar o sistema da NFC-e logicamente através dos comandos abaixo:
 
<Máquina SRVNFCE01HMLCAS>
USE NFCe_Out_Constraint
GO
UPDATE NFCeOut.ConfigSistema 
SET   valor = 'OFF'
WHERE parametro = 'ONLINE'
 

2) Após autorização dos developers (que deverão aguardar o item B do Windows), atualizar o banco de dados da NFC-e na instância SRVNFCEHML01CAS de acordo com scripts disponibilizados nos seguintes subdiretórios:
\\CASPCSF250802\RDMs\NFC-e\CRQ0000000xxxxx_hml\DB\NFCe_In
\\CASPCSF250802\RDMs\NFC-e\CRQ0000000xxxxx_hml\DB\NFCe_In_XML
\\CASPCSF250802\RDMs\NFC-e\CRQ0000000xxxxx_hml\DB\NFCe_Out
\\CASPCSF250802\RDMs\NFC-e\CRQ0000000xxxxx_hml\DB\NFCe_Out_XML
\\CASPCSF250802\RDMs\NFC-e\CRQ0000000xxxxx_hml\DB\NFCe_Out_Constraint
 
Importante: 
a- verificar que novos artefatos devem poder ser acessados/executados pelo usuário nfce_user.
b- verificar que novos artefatos sejam criados nas partições corretas, determinadas pelo COI
  

3) Extrair os dacpacs dos bancos da máquinas SRVNFCE01HMLCAS modificados pelos scripts acima e colocá-los em \\CASPCSF250802\RDMs\NFC-e\CRQ0000000xxxxx_hml\DB\dacpac , através do seguinte comando:
 
select 'SqlPackage.exe /a:Extract /ssn:SRVNFCE01HMLCAS\NFCE01CAS /sdn:"'+ name + '" /tf:"D:\SCRIPTS\BACPAC\' + name + '.dacpac" ' from sys.databases
  
sendo que o sqlpackage.exe está normalmente na seguinte pasta \Program Files (x86)\Microsoft SQL Server\110\DAC\bin
 

4) Após autorização dos developers (que devem aguardar o item C de Windows, e verificar os dapcapcs acima), ligar logicamente o sistema NFC-e:
 
<Máquina SRVNFCE01HMLCAS>
USE NFCe_Out_Constraint
GO
UPDATE NFCeOut.ConfigSistema 
SET   valor = 'ON'
WHERE parametro = 'ONLINE'

-- Fim procedimento NFC-e

-- Início procedimento EPEC / DW

5) Após autorização dos developers (que devem aguardar o item D dos Windows e fazer os testes), desligar logicamente o sistema EPEC:
<Máquina SRVEPEC01HML>
USE NFCe_EPEC_Constraint
GO
UPDATE EPEC.ConfigSistema
SET   valor = 'OFF'
WHERE parametro = 'ONLINE'
 

6) Após autorização dos developers (que devem aguardar o item E de Windows), atualizar o banco de dados do EPEC na instância SRVEPEC01HML de acordo com scripts disponibilizados nos seguintes subdiretórios:
\\CASPCSF250802\RDMs\NFC-e\CRQ0000000xxxxx_hml\DB\NFCe_EPEC_Constraint
\\CASPCSF250802\RDMs\NFC-e\CRQ0000000xxxxx_hml\DB\NFCe_EPEC_Dados
\\CASPCSF250802\RDMs\NFC-e\CRQ0000000xxxxx_hml\DB\NFCe_EPEC_XML 
 
Importante: 
a- verificar que novos artefatos devem poder ser acessados/executados pelo usuário epec_user.
b- verificar que novos artefatos sejam criados nas partições corretas, determinadas pelo COI
 

7) Atualizar o banco de dados do DW da NFC-e na instância SRVNFCE01HML de acordo com scripts disponibilizados nos seguintes subdiretorios:
\\CASPCSF250802\RDMs\NFC-e\CRQ0000000xxxxx_hml\DB\ETL_NFCe 
 
Importante: 
a- verificar que novos artefatos devem poder ser acessados/executados pelo usuário nfce_etl_user.
b- verificar que novos artefatos sejam criados nas partições corretas, determinadas pelo COI
 

8) Extrair os dacpacs dos bancos das máquinas SRVEPEC01HML e SRVNFCE01HML modificados pelos scripts acima e colocá-los em \\CASPCSF250802\RDMs\NFC-e\CRQ0000000xxxxx_hml\DB\dacpac , através dos seguintes comandos:
  
select 'SqlPackage.exe /a:Extract /ssn:SRVEPEC01HML\EPEC01 /sdn:"'+ name + '" /tf:"D:\SCRIPTS\BACPAC\' + name + '.dacpac" ' from sys.databases
 
select 'SqlPackage.exe /a:Extract /ssn:SRVNFCE01HML /sdn:"'+ name + '" /tf:"D:\SCRIPTS\BACPAC\' + name + '.dacpac" ' from sys.databases
 
sendo que o sqlpackage.exe está normalmente na seguinte pasta \Program Files (x86)\Microsoft SQL Server\110\DAC\bin
 

9) Após autorização dos developers (que devem aguardar o item F e a verificação dos dacpacs acima), ligar logicamente o sistema EPEC:
<Máquina SRVEPEC01HML>
USE NFCe_EPEC_Constraint
GO
UPDATE EPEC.ConfigSistema
SET   valor = 'ON' 
WHERE parametro = 'ONLINE'

-- Fim procedimento EPEC / DW​
 
### Tarefas DBA Homologação Sem Parar Sistema

-- Início procedimento NFC-e
 
1) Atualizar o banco de dados da NFC-e na instância SRVNFCE01HMLCAS de acordo com scripts disponibilizados nos seguintes subdiretórios:
\\caspcsf250802\RDMs\NFC-e\CRQ0000000xxxxx_hml\DB\NFCe_In
\\caspcsf250802\RDMs\NFC-e\CRQ0000000xxxxx_hml\DB\NFCe_In_XML
\\caspcsf250802\RDMs\NFC-e\CRQ0000000xxxxx_hml\DB\NFCe_Out
\\caspcsf250802\RDMs\NFC-e\CRQ0000000xxxxx_hml\DB\NFCe_Out_XML
\\caspcsf250802\RDMs\NFC-e\CRQ0000000xxxxx_hml\DB\NFCe_Out_Constraint
 
Importante: 
a- verificar que novos artefatos devem poder ser acessados/executados pelo usuário nfce_user.
b- verificar que novos artefatos sejam criados nas partições corretas, determinadas pelo COI
   
2) Extrair os dacpacs dos bancos (NFCe_<...>) da máquinas SRVNFCE01HMLCAS e colocá-los em \\caspcsf250802\RDMs\NFC-e\CRQ0000000xxxxx_hml\DB\dacpac , através do seguinte comando:
 
select 'SqlPackage.exe /a:Extract /ssn:SRVNFCE01HMLCAS\NFCE01CAS /sdn:"'+ name + '" /tf:"D:\SCRIPTS\BACPAC\' + name + '.dacpac" ' from sys.databases
  
sendo que o sqlpackage.exe está normalmente na seguinte pasta \Program Files (x86)\Microsoft SQL Server\110\DAC\bin
 
3) Avisar os developers do CDS e aguardar que eles verifiquem os dacpacs.

-- Fim procedimento NFC-e
 
-- Início procedimento EPEC / DW
 
4) Atualizar o banco de dados do EPEC na instância SRVNFEHML de acordo com scripts disponibilizados nos seguintes subdiretórios:
\\caspcsf250802\RDMs\NFC-e\CRQ0000000xxxxx_hml\DB\NFCe_EPEC_Constraint
\\caspcsf250802\RDMs\NFC-e\CRQ0000000xxxxx_hml\DB\NFCe_EPEC_Dados
\\caspcsf250802\RDMs\NFC-e\CRQ0000000xxxxx_hml\DB\NFCe_EPEC_XML 
 
Importante: 
a- verificar que novos artefatos devem poder ser acessados/executados pelo usuário epec_user.
b- verificar que novos artefatos sejam criados nas partições corretas, determinadas pelo COI
 
 
5) Atualizar o banco de dados do DW da NFC-e na instância SRVNFEHML de acordo com scripts disponibilizados nos seguintes subdiretorios:
\\caspcsf250802\RDMs\NFC-e\CRQ0000000xxxxx_hml\DB\ETL_NFCe 
 
Importante: 
a- verificar que novos artefatos devem poder ser acessados/executados pelo usuário nfce_etl_user.
b- verificar que novos artefatos sejam criados nas partições corretas, determinadas pelo COI
 
 
6) Extrair os dacpacs dos bancos das máquinas SRVNFEHML e colocá-los em \\caspcsf250802\RDMs\NFC-e\CRQ0000000xxxxx_hml\DB\dacpac , através dos seguintes comandos:
select 'SqlPackage.exe /a:Extract /ssn:SRVNFEHML /sdn:"'+ name + '" /tf:"D:\SCRIPTS\BACPAC\' + name + '.dacpac" ' from sys.databases
  
sendo que o sqlpackage.exe está normalmente na seguinte pasta \Program Files (x86)\Microsoft SQL Server\110\DAC\bin
 
7) Avisar os developers do CDS e aguardar que eles verifiquem os dacpacs

-- Fim procedimento EPEC / DW
 
## RDM com tarefa manual de Produção

### Tarefas Windows Produção

Algum dos seguintes developers do CDS deve acompanhar (exceto o item (A), backup, que pode ser feito sem acompanhamento): Fábio Nagamine, Francisco Blasi Jr. ou Rafael Assunção.
 
Máquinas envolvidas no deploy:
- NFC-e: SRV72131, SRV72132, SRV72133, SRV72134, SRV72135, SRV72136, SRV72137, SRV72138, SRV72139, SRV72140, SRV72141, SRV72142
- EPEC: SRV11081 e SRV11082, SRV11083, SRV11084, SRV11085, SRV11086, SEFAZNET9
 
A) Realizar back-up da versão atual da aplicação em cada servidor:
 
A.1) SRV72131, SRV72132, SRV72133, SRV72134:
- http://nfce.fazenda.sp.gov.br/NFCePortal
- https://nfce.fazenda.sp.gov.br/ws
- https://www.nfce.fazenda.sp.gov.br/NFCeSiteContribuinte
- https://www.nfce.fazenda.sp.gov.br/NFCeConsultaPublica/
 
A.2) SRV72135, SRV72136, SRV72137, SRV72138
- serviço Sefaz.NFCeWindowsService
 
A.3) SRV72139, SRV72140
- net.tcp://nfcews.intra.fazenda.sp.gov.br/NFCe.WcfServiceApplication
- https://nfcews.intra.fazenda.sp.gov.br/NFCePortalFazendario
 
A.4) SRV72141 e SRV72142
- serviço Sefaz.NFCeLCRService
- serviço Sefaz.NFCeVerificacaoSAT
 
A.5) SRV11081 e SRV11082
- serviço Sefaz.NFCeConciliacaoEPEC
- serviço Sefaz.NFCeBloqueioEPEC
- serviço Sefaz.NFCeAtivacaoEPEC
- executável do DW no diretório D:\Program Files (x86)\SEFAZ_SP\NFCeExtratorDW
 
A.6) SRV11083 SRV11084 SRV11085 SRV11086
- https://nfce.epec.fazenda.sp.gov.br/EPECws
 
A.7) SEFAZNET9
- https://sefaznet9.sede.fazenda.sp.gov.br/NFCeAdministracaoServicoEPEC
 
-- Inicio procedimento NFC-e
 
B) Após a autorização dos developers (que deverão aguardar o item 1 dos DBAs), fazer os procedimentos a seguir, na ordem:
B.1) parar os application pool das aplicações da NFC-e das máquinas web SRV72131, SRV72132, SRV72133, SRV72134
B.2) parar os application pool das aplicações da NFC-e das máquinas de web SRV72139, SRV72140
B.3) parar o serviço Sefaz.NFCeWindowsService nas máquinas SRV72135, SRV72136, SRV72137, SRV72138
B.4) parar o cluster dos serviços Sefaz.NFCeLCRService e Sefaz.NFCeVerificacaoSAT nas máquinas SRV72141 e SRV72142
 
C) Realizar as atualizações:
Observação: em todas as atualizações, sempre apagar o diretório destino antes de copiar o novo código.
 
C.1) Windows-Services
Atualizar o serviço Sefaz.NFCeWindowsService nas máquinas de Windows Service da NFC-e (SRV72135, SRV72136, SRV72137, SRV72138) , de acordo com arquivos em
\\caspcsf250802\RDMs\NFC-e\CRQ0000000XXXXX_prod\WindowsService_SRV72135_36_37_38
 
C.2) LCR Services
Atualizar o serviço Sefaz.NFCeLCRService nas máquinas de Windows Service de LCR da NFC-e (SRV72141 e SRV72142), de acordo com arquivos disponibilizados em
\\caspcsf250802\RDMs\NFC-e\CRQ0000000XXXXX_prod\LCRWindowsService_SRV72141_42
 
C.3) Verificacao SAT
Atualizar o serviço Sefaz.NFCeVerificacaoSAT nas máquinas de Windows Service de Verificação SAT da NFC-e (SRV72141 e SRV72142), de acordo com arquivos disponibilizados em
\\caspcsf250802\RDMs\NFC-e\CRQ0000000XXXXX_prod\NFCeVerificacaoSAT_WindowsService_SRV72141_42
 
C.4) Web-Services
Atualizar os Web Services da NFC-e (https://nfce.fazenda.sp.gov.br/ws) nas máquinas de produção (SRV72131, SRV72132, SRV72133, SRV72134), de acordo com arquivos em
\\caspcsf250802\RDMs\NFC-e\CRQ0000000XXXXX_prod\WebService_SRV72131_32_33_34
 
C.5) Site do Contribuinte
Atualizar o Site do Contribuinte da NFC-e (https://www.nfce.fazenda.sp.gov.br/NFCeSiteContribuinte/) nas máquinas de produção (SRV72131, SRV72132, SRV72133, SRV72134), de acordo com executáveis disponibilizados em
\\caspcsf250802\RDMs\NFC-e\CRQ0000000XXXXX_prod\NFCeSiteContribuinte_SRV72131_32_33_34
 
C.6) Consulta Pública
Atualizar o Site de Consulta Pública da NFC-e (https://www.nfce.fazenda.sp.gov.br/NFCeConsultaPublica/) nas máquinas de produção (SRV72131, SRV72132, SRV72133, SRV72134), de acordo com executáveis disponibilizados em
\\caspcsf250802\RDMs\NFC-e\CRQ0000000XXXXX_prod\NFCeConsultaPublica_SRV72131_32_33_34
 
C.7) Serviço de WCF
Atualizar o serviço de WCF da NFC-e (net.tcp://nfcews.intra.fazenda.sp.gov.br/NFCe.WcfServiceApplication/NFCeService.svc) nas máquinas de produção (SRV72139 e SRV72140), de acordo com executáveis disponibilizados em
\\caspcsf250802\RDMs\NFC-e\CRQ0000000XXXXX_prod\NFCe.WcfServiceApplication_SRV72139_40
 
C.8) Portal Fazendário
Atualizar o Poral Fazedário da NFC-e (https://nfcews.intra.fazenda.sp.gov.br/NFCePortalFazendario) nas máquinas de produção (SRV72139 e SRV72140) de acordo com executáveis disponibilizados em
\\caspcsf250802\RDMs\NFC-e\CRQ0000000XXXXX_prod\NFCePortalFazendario_srv72139_40
 
C.9) Portal NFC-e
Atualizar o Portal da NFC-e (http://www.nfce.fazenda.sp.gov.br) nas máquinas de produção (SRV72131, SRV72132, SRV72133, SRV72134) de acordo com executáveis disponibilizados em
\\caspcsf250802\RDMs\NFC-e\CRQ0000000XXXXX_prod\NFCePortal_SRV72131_32_33_34
 
C.10) Contadores de performance
Instalar contadores de performance, rodando *com permissão de administrador* o instalador disponível em:
\\caspcsf250802\RDMs\NFC-e\CRQ0000000XXXXX_prod\ContadoresPerfmon\WebService_srv72131_32_33_34
\\caspcsf250802\RDMs\NFC-e\CRQ0000000XXXXX_prod\ContadoresPerfmon\WindowsService_srv72135_36_37_38
* As máquinas onde os contadores devem ser instalados estão designadas nos nomes de cada pasta.
* Após a instalação dos contadores, verificar a instalação: abrindo o aplicativo "perfmon.exe" e adicionando os contadores do grupo "NFCe" (conferir com o desenvolvedor que estiver acompanhando a instalação).
 
D) Após autorização dos developers (que devem aguardar o item 4 dos DBAs), reiniciar na ordem
D.1) os windows-services Sefaz.NFCeWindowsService (SRV72135, SRV72136, SRV72137, SRV72138)
D.2) os windows-services Sefaz.NFCeLCRService e Sefaz.NFCeVerificacaoSAT (SRV72141 e SRV72142)
D.3) os applications pool das aplicações da NFC-e das máquinas web (SRV72139 e SRV72140)
D.4) os applications pool das aplicações da NFC-e das máquinas web (SRV72131, SRV72132, SRV72133, SRV72134)
 
-- Fim procedimento NFC-e
 
-- Inicio procedimento EPEC / DW
 
E) Após a autorização dos developers (que devem aguardar o item 5 de DBA), fazer os procedimentos a seguir, na ordem:
E.1) parar o application pool do Site de Administração do EPEC nas máquinas web da SEFAZNET9
E.2) parar o cluster do serviço Sefaz.NFCeConciliacaoEPEC nas máquinas SRV11081 e SRV11082
E.3) parar o cluster do serviço Sefaz.NFCeBloqueioEPEC nas máquinas SRV11081 e SRV11082
E.4) parar o cluster do serviço Sefaz.NFCeAtivacaoEPEC nas máquinas SRV11081 e SRV11082
E.5) parar o application pool das aplicações da NFC-e das máquinas web do EPEC SRV11083, SRV11084, SRV11085 e SRV11086
 
F) Realizar as atualizações:
Observação: em todas as atualizações, sempre apagar o diretório destino antes de copiar o novo código.
 
F.1) Windows-Service Ativação EPEC
Atualizar o serviço Sefaz.NFCeAtivacaoEPEC nas máquinas de Windows Service do EPEC (SRV11081 e SRV11082), de acordo com executáveis disponibilizados em:
\\caspcsf250802\RDMs\NFC-e\CRQ0000000XXXXX_prod\WindowsService_AtivacaoEPEC_SRV11081_82
 
F.2) Windows-Service Bloqueio EPEC
Atualizar o serviço Sefaz.NFCeBloqueioEPEC nas máquinas de Windows Service do EPEC (SRV11081 e SRV11082), de acordo com executáveis disponibilizados em:
\\caspcsf250802\RDMs\NFC-e\CRQ0000000XXXXX_prod\WindowsService_BloqueioEPEC_SRV11081_82
 
F.3) Windows-Service Conciliação EPEC
Atualizar o serviço Sefaz.NFCeConciliacaoEPEC nas máquinas de Windows Service do EPEC (SRV11081 e SRV11082), de acordo com executáveis disponibilizados em:
\\caspcsf250802\RDMs\NFC-e\CRQ0000000XXXXX_prod\WindowsService_ConciliacaoEPEC_SRV11081_82
 
F.4) DW NFC-e
Atualizar o executável do DW (diretório D:\Program Files (x86)\SEFAZ_SP\NFCeExtratorDW) nas máquinas de produção (SRV11081 e SRV11082) de acordo com executáveis disponibilizados em
\\caspcsf250802\RDMs\NFC-e\CRQ0000000XXXXX_prod\DW_srv11081_82
 
F.5) Web-Service EPEC
Atualizar o Web Service do EPEC (https://nfce.epec.fazenda.sp.gov.br/EPECws) nas máquinas de produção (SRV11083, SRV11084, SRV11085 e SRV11086), de acordo com executáveis disponibilizados em:
\\caspcsf250802\RDMs\NFC-e\CRQ0000000XXXXX_prod\EPEC_WebService_srv11083_84_85_86
  
F.6) Site Administração do EPEC
Atualizar o Site de Administração do EPEC (https://sefaznet9.sede.fazenda.sp.gov.br/NFCeAdministracaoServicoEPEC/) nas máquinas de produção (SEFAZNET9) de acordo com executáveis disponibilizados em
\\caspcsf250802\RDMs\NFC-e\CRQ0000000XXXXX_prod\NFCeAdministracaoServicoEPEC_sefaznet9
 
F.7) Contadores de performance
Instalar contadores de performance, rodando *com permissão de administrador* o instalador disponível em:
\\caspcsf250802\RDMs\NFC-e\CRQ0000000XXXXX_prod\ContadoresPerfmon\EPEC_SRV11081_82_83_84_85_86
* As máquinas onde os contadores devem ser instalados estão designadas nos nomes de cada pasta.
* Após a instalação dos contadores, verificar a instalação: abrindo o aplicativo "perfmon.exe" e adicionando os contadores do grupo "NFCe" (conferir com o desenvolvedor que estiver acompanhando a instalação).
 
G) Após autorização dos developers (que devem aguardar 9 dos DBAs), reiniciar na ordem
G.1) o application pool das aplicações da NFC-e na máquina web (SEFAZNET9)
G.2) os windows-service, pelo cluster, Sefaz.NFCeConciliacaoEPEC, Sefaz.NFCeBloqueioEPEC e windows-service Sefaz.NFCeAtivacaoEPEC (SRV11081 e SRV11082)
G.3) o application pool das aplicações da NFC-e das máquinas web do EPEC (SRV11083, SRV11084, SRV11085 e SRV11086)
 
-- Fim procedimento EPEC / DW

### Tarefas Windows Produção sem Parar Sistema

------------------------------------------------------------------------------------- 
Algum dos seguintes developers do CDS deve acompanhar (exceto o item (A), backup, que pode ser feito sem acompanhamento): Fábio Nagamine, Francisco Blasi Jr.
 
Máquinas envolvidas no deploy:
- NFC-e: SRV72131, SRV72132, SRV72133, SRV72134, SRV72135, SRV72136, SRV72137, SRV72138, SRV72139, SRV72140, SRV72141, SRV72142
- EPEC: SRV11081 e SRV11082, SRV11083, SRV11084, SRV11085, SRV11086, SEFAZNET9
 
A) Realizar back-up da versão atual da aplicação em cada servidor:
 
A.1) SRV72131, SRV72132, SRV72133, SRV72134: 
- http://nfce.fazenda.sp.gov.br/NFCePortal
- https://nfce.fazenda.sp.gov.br/ws
- https://www.nfce.fazenda.sp.gov.br/NFCeSiteContribuinte
- https://www.nfce.fazenda.sp.gov.br/NFCeConsultaPublica/
 
A.2) SRV72135, SRV72136, SRV72137, SRV72138
- serviço Sefaz.NFCeWindowsService
 
A.3) SRV72139, SRV72140
- net.tcp://nfcews.intra.fazenda.sp.gov.br/NFCe.WcfServiceApplication
- https://nfcews.intra.fazenda.sp.gov.br/NFCePortalFazendario 
 
A.4) SRV72141 e SRV72142
- serviço Sefaz.NFCeLCRService
- serviço Sefaz.NFCeVerificacaoSAT
 
A.5) SRV11081 e SRV11082
- serviço Sefaz.NFCeConciliacaoEPEC 
- serviço Sefaz.NFCeBloqueioEPEC 
- serviço Sefaz.NFCeAtivacaoEPEC
- executável do DW no diretório D:\Program Files (x86)\SEFAZ_SP\NFCeExtratorDW 
 
A.6) SRV11083 SRV11084 SRV11085 SRV11086
- https://nfce.epec.fazenda.sp.gov.br/EPECws
 
A.7) SEFAZNET9
- https://sefaznet9.sede.fazenda.sp.gov.br/NFCeAdministracaoServicoEPEC 
 
======= Inicio procedimento NFC-e  ======= 
 
B) Realizar as atualizações:
 
Observação 1: em todas as atualizações, sempre apagar o diretório destino antes de copiar o novo código.
 
Observação 2: nas atualizações de web-services ou sites:
- tirar a máquina do balance
- parar o respectivo application pool
- fazer a atualização
- reiniciar o respectivo application pool
- recolocar a máquina no balance
 
Observação 3: nas atualizações de windows-services em cluster
- atualizar o serviço no lado do cluster desligado
- virar o cluster para o lado atualizado
- atualizar o lado recém desligado
 
Observação 4: nas atualizações de windows-services não em cluster
- parar o serviço
- atualizar 
- reiniciar o serviço
Observação 5: nas atualizações de Contadores de performance, rodar *com permissão de administrador* o instalador disponível. Após a instalação dos contadores, verificar a instalação: abrindo o aplicativo "perfmon.exe" e adicionando os contadores do grupo "NFCe" (conferir com o desenvolvedor que estiver acompanhando a instalação).
--------------------

B.1) Máquinas SRV72139 e SRV72140
 
B.1.1) Serviço de WCF
- Atualizar o serviço de WCF da NFC-e (net.tcp://nfcews.intra.fazenda.sp.gov.br/NFCe.WcfServiceApplication/NFCeService.svc) nas máquinas de produção (SRV72139 e SRV72140), de acordo com executáveis disponibilizados em 
\\caspcsf250802\RDMs\NFC-e\CRQ0000000xxxxx_prod\NFCe.WcfServiceApplication_SRV72139_40
  
B.1.2) Portal Fazendário
- Atualizar o Poral Fazedário da NFC-e (https://nfcews.intra.fazenda.sp.gov.br/NFCePortalFazendario) nas máquinas de produção (SRV72139 e SRV72140) de acordo com executáveis disponibilizados em
\\caspcsf250802\RDMs\NFC-e\CRQ0000000xxxxx_prod\NFCePortalFazendario_srv72139_40
 
B.1.3) TesteMonitoracaoNFCe
- Atualizar o aplicativo TesteMonitoracaoNFCe nas máquinas de produção (SRV72139 e SRV72140), localizado em D:\Util\Scripts_Monitoracao_scom, de acordo com arquivos em \\caspcsf250802\RDMs\NFC-e\CRQ0000000xxxxx_prod\TesteMonitoracaoNFCe
 
--------------------
 
B.2) Máquinas SRV72131, SRV72132, SRV72133, SRV72134
 
B.2.1) Site do Contribuinte
- Atualizar o Site do Contribuinte da NFC-e (https://www.nfce.fazenda.sp.gov.br/NFCeSiteContribuinte/) nas máquinas de produção (SRV72131, SRV72132, SRV72133, SRV72134), de acordo com executáveis disponibilizados em 
\\caspcsf250802\RDMs\NFC-e\CRQ0000000xxxxx_prod\NFCeSiteContribuinte_SRV72131_32_33_34
 
B.2.2) Consulta Pública
- Atualizar o Site de Consulta Pública da NFC-e (https://www.nfce.fazenda.sp.gov.br/NFCeConsultaPublica/) nas máquinas de produção (SRV72131, SRV72132, SRV72133, SRV72134), de acordo com executáveis disponibilizados em 
\\caspcsf250802\RDMs\NFC-e\CRQ0000000xxxxx_prod\NFCeConsultaPublica_SRV72131_32_33_34
 
B.2.3) Portal NFC-e
- Atualizar o Portal da NFC-e (http://www.nfce.fazenda.sp.gov.br) nas máquinas de produção (SRV72131, SRV72132, SRV72133, SRV72134) de acordo com executáveis disponibilizados em
\\caspcsf250802\RDMs\NFC-e\CRQ0000000xxxxx_prod\NFCePortal_SRV72131_32_33_34
 
B.2.4) Web-Services
- Atualizar os Web Services da NFC-e (https://nfce.fazenda.sp.gov.br/ws) nas máquinas de produção (SRV72131, SRV72132, SRV72133, SRV72134), de acordo com arquivos em 
\\caspcsf250802\RDMs\NFC-e\CRQ0000000xxxxx_prod\WebService_SRV72131_32_33_34
- Atualizar contadores de performance de acordo com arquivos em \\caspcsf250802\RDMs\NFC-e\CRQ0000000xxxxx_prod\ContadoresPerfmon\WebService_srv72131_32_33_34
 
B.2.4) TesteMonitoracaoNFCe
- Atualizar o aplicativo TesteMonitoracaoNFCe nas máquinas deprodução (SRV72131, SRV72132, SRV72133, SRV72134), localizado em D:\Util\Scripts_Monitoracao_scom, de acordo com arquivos em \\caspcsf250802\RDMs\NFC-e\CRQ0000000xxxxx_prod\TesteMonitoracaoNFCe
 
--------------------
 
B.3) Máquinas SRV72135, SRV72136, SRV72137, SRV72138
 
B.3.1) Windows-Services
- Atualizar o serviço Sefaz.NFCeWindowsService nas máquinas de Windows Service da NFC-e (SRV72135, SRV72136, SRV72137, SRV72138) , de acordo com arquivos em 
\\caspcsf250802\RDMs\NFC-e\CRQ0000000xxxxx_prod\WindowsService_SRV72135_36_37_38
- Atualizar contadores de performance de acordo com arquivos em \\caspcsf250802\RDMs\NFC-e\CRQ0000000xxxxx_prod\ContadoresPerfmon\WindowsService_srv72135_36_37_38
 
--------------------
B.4) Máquinas SRV72141 e SRV72142
 
B.4.1) LCR Services
- Atualizar o serviço Sefaz.NFCeLCRService nas máquinas de Windows Service de LCR da NFC-e (SRV72141 e SRV72142), de acordo com arquivos disponibilizados em 
\\caspcsf250802\RDMs\NFC-e\CRQ0000000xxxxx_prod\LCRWindowsService_SRV72141_42
B.4.2) VerificacaoSAT
- Atualizar o serviço Sefaz.NFCeVerificacaoSAT nas máquinas de Windows Service de Verificacao SAT da NFC-e (SRV72141 e SRV72142), de acordo com arquivos disponibilizados em 
\\caspcsf250802\RDMs\NFC-e\CRQ0000000xxxxx_prod\NFCeVerificacaoSAT_WindowsService_SRV72141_42
   
B.4.3) TesteMonitoracaoNFCe
- Atualizar o aplicativo TesteMonitoracaoNFCe nas máquinas deprodução (SRV72141 e SRV72142), localizado em D:\Util\Scripts_Monitoracao_scom, de acordo com arquivos em \\caspcsf250802\RDMs\NFC-e\CRQ0000000xxxxx_prod\TesteMonitoracaoNFCe
 
======= Fim procedimento NFC-e ======= 
 
=======  Inicio procedimento EPEC / DW =======
 
C) Realizar as atualizações:
 
Observação 1: em todas as atualizações, sempre apagar o diretório destino antes de copiar o novo código.
 
Observação 2: nas atualizações de web-services ou sites:
- tirar a máquina do balance
- parar o respectivo application pool
- fazer a atualização
- reiniciar o respectivo application pool
- recolocar a máquina no balance
 
Observação 3: nas atualizações de windows-services em cluster
- atualizar o serviço no lado do cluster desligado
- virar o cluster para o lado atualizado
- atualizar o lado recém desligado
 
Observação 4: nas atualizações de Contadores de performance, rodar *com permissão de administrador* o instalador disponível. Após a instalação dos contadores, verificar a instalação: abrindo o aplicativo "perfmon.exe" e adicionando os contadores do grupo "NFCe" (conferir com o desenvolvedor que estiver acompanhando a instalação).
  
--------------------
 
C.1) Máquinas SRV11081 e SRV11082
 
C.1.1) Windows-Service Ativação EPEC
- Atualizar o serviço Sefaz.NFCeAtivacaoEPEC nas máquinas de Windows Service do EPEC (SRV11081 e SRV11082), de acordo com executáveis disponibilizados em: 
\\caspcsf250802\RDMs\NFC-e\CRQ0000000xxxxx_prod\WindowsService_AtivacaoEPEC_SRV11081_82
 
C.1.2) Windows-Service Bloqueio EPEC
- Atualizar o serviço Sefaz.NFCeBloqueioEPEC nas máquinas de Windows Service do EPEC (SRV11081 e SRV11082), de acordo com executáveis disponibilizados em:
\\caspcsf250802\RDMs\NFC-e\CRQ0000000xxxxx_prod\WindowsService_BloqueioEPEC_SRV11081_82
 
C.1.3) Windows-Service Conciliação EPEC
- Atualizar o serviço Sefaz.NFCeConciliacaoEPEC nas máquinas de Windows Service do EPEC (SRV11081 e SRV11082), de acordo com executáveis disponibilizados em:
\\caspcsf250802\RDMs\NFC-e\CRQ0000000xxxxx_prod\WindowsService_ConciliacaoEPEC_SRV11081_82
 
C.1.4) DW NFC-e
- Atualizar o executável do DW (diretório D:\Program Files (x86)\SEFAZ_SP\NFCeExtratorDW) nas máquinas de produção (SRV11081 e SRV11082) de acordo com executáveis disponibilizados em
\\caspcsf250802\RDMs\NFC-e\CRQ0000000xxxxx_prod\DW_srv11081_82
 
--------------------
C.2) Máquinas SRV11083, SRV11084, SRV11085 e SRV11086
 
C.2.1) Web-Service EPEC
- Atualizar o Web Service do EPEC (https://nfce.epec.fazenda.sp.gov.br/EPECws) nas máquinas de produção (SRV11083, SRV11084, SRV11085 e SRV11086), de acordo com executáveis disponibilizados em:
\\caspcsf250802\RDMs\NFC-e\CRQ0000000xxxxx_prod\EPEC_WebService_srv11083_84_85_86
- Atualizar contadores de performance de acordo com arquivos disponibilizados em \\caspcsf250802\RDMs\NFC-e\CRQ0000000xxxxx_prod\ContadoresPerfmon\EPEC_SRV11081_82_83_84_85_86
C.2.2) TesteMonitoracaoNFCe
- Atualizar o aplicativo TesteMonitoracaoNFCe nas máquinas deprodução (SRV11083, SRV11084, SRV11085 e SRV11086), localizado em D:\Util\Scripts_Monitoracao_scom, de acordo com arquivos em \\caspcsf250802\RDMs\NFC-e\CRQ0000000xxxxx_prod\TesteMonitoracaoNFCe
  
--------------------
C.3) Máquinas SEFAZNET9 
   
C.3.1) Site Administração do EPEC
- Atualizar o Site de Administração do EPEC (https://sefaznet9.sede.fazenda.sp.gov.br/NFCeAdministracaoServicoEPEC/) nas máquinas de produção (SEFAZNET9) de acordo com executáveis disponibilizados em
\\caspcsf250802\RDMs\NFC-e\CRQ0000000xxxxx_prod\NFCeAdministracaoServicoEPEC_sefaznet9
 
=======  Fim procedimento EPEC / DW =======​

### Tarefas DBA Produção
 
-- Início procedimento NFC-e
 
1) Desligar o sistema da NFC-e logicamente através dos comandos abaixo

(Pode ser feito pelo próprio desenvolvedor através do Portal do Fazendário)
 
<Máquina SRVNFCE01CAS>
USE NFCe_Out_Constraint
GO
UPDATE NFCeOut.ConfigSistema 
SET   valor = 'OFF'
WHERE parametro = 'ONLINE'
 
 
2) Após autorização dos developers (que deverão aguardar o item B do Windows), atualizar o banco de dados da NFC-e na instância SRVNFCE01CAS de acordo com scripts disponibilizados nos seguintes subdiretórios:
\\CASPCSF250802\RDMs\NFC-e\CRQ0000000xxxxx_prod\DB\NFCe_In
\\CASPCSF250802\RDMs\NFC-e\CRQ0000000xxxxx_prod\DB\NFCe_In_XML
\\CASPCSF250802\RDMs\NFC-e\CRQ0000000xxxxx_prod\DB\NFCe_Out
\\CASPCSF250802\RDMs\NFC-e\CRQ0000000xxxxx_prod\DB\NFCe_Out_XML
\\CASPCSF250802\RDMs\NFC-e\CRQ0000000xxxxx_prod\DB\NFCe_Out_Constraint
 
Importante: 
a- verificar que novos artefatos devem poder ser acessados/executados pelo usuário nfce_user.
b- verificar que novos artefatos sejam criados nas partições corretas, determinadas pelo COI
  
 
3) Extrair os dacpacs dos bancos da máquinas SRVNFCE01CAS modificados pelos scripts acima e colocá-los em \\caspcsf236343\RDMs\NFC-e\CRQ0000000xxxxx_prod\DB\dacpac , através do seguinte comando:
 
select 'SqlPackage.exe /a:Extract /ssn:SRVNFCE01CAS\NFCE01CAS /sdn:"'+ name + '" /tf:"D:\SCRIPTS\BACPAC\' + name + '.dacpac" ' from sys.databases
  
sendo que o sqlpackage.exe está normalmente na seguinte pasta \Program Files (x86)\Microsoft SQL Server\110\DAC\bin
 
 
4) Após autorização dos developers (que devem aguardar o item C de Windows, e verificar os dapcapcs acima), ligar logicamente o sistema NFC-e:
 
<Máquina SRVNFCE01CAS>
USE NFCe_Out_Constraint
GO
UPDATE NFCeOut.ConfigSistema 
SET   valor = 'ON'
WHERE parametro = 'ONLINE'
 
-- Fim procedimento NFC-e
 
-- Início procedimento EPEC / DW
 
5) Após autorização dos developers (que devem aguardar o item D dos Windows e fazer os testes), desligar logicamente o sistema EPEC:
<Máquina SRVEPEC01>
USE NFCe_EPEC_Constraint
GO
UPDATE EPEC.ConfigSistema
SET   valor = 'OFF'
WHERE parametro = 'ONLINE'
 
 
6) Após autorização dos developers (que devem aguardar o item E de Windows), atualizar o banco de dados do EPEC na instância SRVEPEC01 de acordo com scripts disponibilizados nos seguintes subdiretórios:
\\CASPCSF250802\RDMs\NFC-e\CRQ0000000xxxxx_prod\DB\NFCe_EPEC_Constraint
\\CASPCSF250802\RDMs\NFC-e\CRQ0000000xxxxx_prod\DB\NFCe_EPEC_Dados
\\CASPCSF250802\RDMs\NFC-e\CRQ0000000xxxxx_prod\DB\NFCe_EPEC_XML 
 
Importante: 
a- verificar que novos artefatos devem poder ser acessados/executados pelo usuário epec_user.
b- verificar que novos artefatos sejam criados nas partições corretas, determinadas pelo COI
 
 
7) Atualizar o banco de dados do DW da NFC-e na instância SRVNFCE01 de acordo com scripts disponibilizados nos seguintes subdiretorios:
\\CASPCSF250802\RDMs\NFC-e\CRQ0000000xxxxx_prod\DB\ETL_NFCe 
 
Importante: 
a- verificar que novos artefatos devem poder ser acessados/executados pelo usuário nfce_etl_user.
b- verificar que novos artefatos sejam criados nas partições corretas, determinadas pelo COI
 
 
8) Extrair os dacpacs dos bancos das máquinas SRVEPEC01 e SRVNFCE01 modificados pelos scripts acima e colocá-los em \\CASPCSF250802\RDMs\NFC-e\CRQ0000000xxxxx_prod\DB\dacpac , através dos seguintes comandos:
  
select 'SqlPackage.exe /a:Extract /ssn:SRVEPEC01\EPEC01 /sdn:"'+ name + '" /tf:"D:\SCRIPTS\BACPAC\' + name + '.dacpac" ' from sys.databases
 
select 'SqlPackage.exe /a:Extract /ssn:SRVNFCE01 /sdn:"'+ name + '" /tf:"D:\SCRIPTS\BACPAC\' + name + '.dacpac" ' from sys.databases
 
sendo que o sqlpackage.exe está normalmente na seguinte pasta \Program Files (x86)\Microsoft SQL Server\110\DAC\bin
 
 
9) Após autorização dos developers (que devem aguardar o item F e a verificação dos dacpacs acima), ligar logicamente o sistema EPEC:
<Máquina SRVEPEC01>
USE NFCe_EPEC_Constraint
GO
UPDATE EPEC.ConfigSistema
SET   valor = 'ON' 
WHERE parametro = 'ONLINE'
 
-- Fim procedimento EPEC / DW

### Tarefas DBA Produção sem Parar Sistema

- Início procedimento NFC-e

1) Atualizar o banco de dados da NFC-e na instância SRVNFCE01CAS de acordo com scripts disponibilizados nos seguintes subdiretórios:
\\srv72043\temp$\NFC-e\RDMs\NFC-e\CRQ0000000xxxxx_prod\DB\NFCe_In
\\srv72043\temp$\NFC-e\RDMs\NFC-e\CRQ0000000xxxxx_prod\DB\NFCe_In_XML
\\srv72043\temp$\NFC-e\RDMs\NFC-e\CRQ0000000xxxxx_prod\DB\NFCe_Out
\\srv72043\temp$\NFC-e\RDMs\NFC-e\CRQ0000000xxxxx_prod\DB\NFCe_Out_XML
\\srv72043\temp$\NFC-e\RDMs\NFC-e\CRQ0000000xxxxx_prod\DB\NFCe_Out_Constraint

Importante: 
a- verificar que novos artefatos devem poder ser acessados/executados pelo usuário nfce_user.
b- verificar que novos artefatos sejam criados nas partições corretas, determinadas pelo COI

2) Extrair os dacpacs dos bancos (NFCe_<...>) da máquinas SRVNFCE01CAS e colocá-los em \\srv72043\temp$\NFC-e\RDMs\NFC-e\CRQ0000000xxxxx_prod\DB\dacpac , através do seguinte comando:
select 'SqlPackage.exe /a:Extract /ssn:SRVNFCE01CAS\NFCE01CAS /sdn:"'+ name + '" /tf:"D:\SCRIPTS\BACPAC\' + name + '.dacpac" ' from sys.databases

sendo que o sqlpackage.exe está normalmente na seguinte pasta \Program Files (x86)\Microsoft SQL Server\110\DAC\bin

3) Avisar os developers do CDS e aguardar que eles verifiquem os dacpacs.

-- Fim procedimento NFC-e

 

-- Início procedimento EPEC / DW

4) Atualizar o banco de dados do EPEC na instância SRVEPEC01 de acordo com scripts disponibilizados nos seguintes subdiretórios:
\\srv72043\temp$\NFC-e\RDMs\NFC-e\CRQ0000000xxxxx_prod\DB\NFCe_EPEC_Constraint
\\srv72043\temp$\NFC-e\RDMs\NFC-e\CRQ0000000xxxxx_prod\DB\NFCe_EPEC_Dados
\\srv72043\temp$\NFC-e\RDMs\NFC-e\CRQ0000000xxxxx_prod\DB\NFCe_EPEC_XML 

Importante: 
a- verificar que novos artefatos devem poder ser acessados/executados pelo usuário epec_user.
b- verificar que novos artefatos sejam criados nas partições corretas, determinadas pelo COI


5) Atualizar o banco de dados do DW da NFC-e na instância SRVCORP01 de acordo com scripts disponibilizados nos seguintes subdiretorios:
\\srv72043\temp$\NFC-e\RDMs\NFC-e\CRQ0000000xxxxx_prod\DB\ETL_NFCe 


Importante: 
a- verificar que novos artefatos devem poder ser acessados/executados pelo usuário nfce_etl_user.
b- verificar que novos artefatos sejam criados nas partições corretas, determinadas pelo COI


6) Extrair os dacpacs dos bancos das máquinas SRVEPEC01 (NFCe_EPEC_<...>)e SRVCORP01 (ETL_NFCe) e colocá-los em \\srv72043\temp$\NFC-e\RDMs\NFC-e\CRQ0000000xxxxx_prod\DB\dacpac , através dos seguintes comandos:
select 'SqlPackage.exe /a:Extract /ssn:SRVEPEC01\EPEC01 /sdn:"'+ name + '" /tf:"D:\SCRIPTS\BACPAC\' + name + '.dacpac" ' from sys.databases
select 'SqlPackage.exe /a:Extract /ssn:SRVCORP01 /sdn:"'+ name + '" /tf:"D:\SCRIPTS\BACPAC\' + name + '.dacpac" ' from sys.databases

sendo que o sqlpackage.exe está normalmente na seguinte pasta \Program Files (x86)\Microsoft SQL Server\110\DAC\bin
 
7) Avisar os developers do CDS e aguardar que eles verifiquem os dacpacs.

-- Fim procedimento EPEC / DW

# Itens depreciados (necessário rever)

## [Deprecated] Itens de Configuração que precisamos lembrar manualmente em ambientes que não temos acesso
IIS

Configurar para limitar tamanho de requisição em 500K (http://etc.intra.fazenda.sp.gov.br/sites/cds_equipe/Wiki%20CDS/Erro%20HTTP%20413%20-%20Request%20Entity%20Too%20Large.aspx)
Configurar redirect para o portal, tem que habilitar a feature de redirect

Configurar WCF (http://etc.intra.fazenda.sp.gov.br/sites/cds_equipe/Documentos%20Compartilhados/Documentos%20Wiki/Configurando%20WCF%20via%20net%20tcp.pptx)

Configurar contadores de performance

## [Deprecated] Procedimentos para o merge Dev -> Main  (Deploy manual no ambiente de DI):

0) Se for a primeira vez que está rodando:
Conferir acesso e permissões de escrita nos seguintes caminhos:
\\srv72066\NFCe.WcfServiceApplication
\\srv72067\NFCeConsultaPublica
\\srv72067\NFCeAdministracaoServicoEPEC
\\srv72067\NFCePortal
\\srv72067\NFCePortalFazendario
\\srv72067\NFCeSiteContribuinte
\\srv72067\ws
\\srv72067\EPECws
SRV72068: C:\Program Files (x86)\SEFAZ_SP\NFCeLCRService
SRV72068: C:\Program Files (x86)\SEFAZ_SP\NFCeWindowsService
SRV72068: C:\Program Files (x86)\SEFAZ_SP\NFCeAtivacaoEPEC
SRV72068: C:\Program Files (x86)\SEFAZ_SP\NFCeBloqueioEPEC
SRV72068: C:\Program Files (x86)\SEFAZ_SP?\NFCeConciliacaoEPEC
SRV72068: C:\Program Files (x86)\SEFAZ_SP?\NFCeExtratorDW
I) Fazer o merge:
 
Verificar que não há checkouts pendentes de checkin, mesmo que em outros branches (fazer Undo ou Shelve se houver)
Fechar todas as solutions e abrir o Source Control Explorer
Clicar com botão direito sobre branches Dev e Main, selecionar "Get Latest Version"
Clicar com botão direito sobre o branch Dev, selecionar Merge, to Main, Selecionar changeset específicos
Verificar se a build que incrementa automaticamente foi rodada, ou seja, se um dos changesets é o “***NO_CI***”. Caso positivo, rodar o merge
Abrir a Solution NFCeSolution.sln do branch Main (verificar que build flavor = Release)
Verificar os checkouts, se todos os Assembly-Info mergeados foram alterados
Compilar com opção “Release” (“Clean Solution” e depois “Rebuild Solution”) 
 
 
II) Gerar scripts do banco de dados:

Criar uma pasta para a versão que será liberada (p. ex. 6.0.4.0) e criar sub-pastas para cada database
Para criar os scripts, selecionar “Publish” no servidor srvdes01 e gerar o script (não fazer o publish direto!!!)
Convém dar publish mesmo se vc souber que não há alteração naquele banco, para ter certeza.
Analisar e editar cada script, principalmente quando tiver mudança em colunas de tabelas
Verificar no Pending-Changes se houve alteração em script de tabela de domínio; se houve, adicionar script no deploy.
Salvar os scripts na pasta criada, os scripts serão usados também para homologação
 
II) Executando o Deploy:
 
Avisar o Kido/Daniel  antes de começar o deploy
Começar o deploy parando os 5 serviços Windows – evita erros caso haja mudança em banco.
Pode-se também alterar a flag de ConfigSistema de ONLINE para OFFLINE tanto na NFC-e quanto no EPEC
Executar os scripts de banco de dados
Por precaução, pode-se fazer o schema-compare do Projeto com banco-DI de todos os databases.
No caso dos Windows-Services/DW, fazer um backup da pasta C:\Program Files (x86)\SEFAZ_SP na pasta D:\NFCe\Backup
No publish dos serviços Web e WCF, sempre conferir se o servidor é o correto; após o publish verificar no "Output" se não teve erros
Rodar Publish dos projetos:
- NFCe.WcfServiceApplication
- NFCeConsultaPublica
- NFCeAdministracaoServicoEPEC
- NFCePortal
- NFCePortalFazendario
- NFCeSiteContribuinte
- ws
- EPECws
No Remote Desktop:
Conectar na máquina SRV72068
Parar os serviços NFCeLCRService e NFCeWindowsService e os serviços do EPEC (Ativação, Bloqueio e Conciliação)
Copiar os arquivos dos serviços:
SRV72068: C:\Program Files (x86)\SEFAZ_SP\NFCeLCRService
SRV72068: C:\Program Files (x86)\SEFAZ_SP\NFCeWindowsService
SRV72068: C:\Program Files (x86)\SEFAZ_SP\NFCeAtivacaoEPEC
SRV72068: C:\Program Files (x86)\SEFAZ_SP\NFCeBloqueioEPEC
SRV72068: C:\Program Files (x86)\SEFAZ_SP?\NFCeConciliacaoEPEC
SRV72068: C:\Program Files (x86)\SEFAZ_SP?\NFCeExtratorDW
Religar os 5 serviços windows
Alterar a flag de ConfigSistema de OFFLINE para ONLINE tanto na NFC-e quanto no EPEC
III) Testar o sistema:
No Banco de Dados, alterar a flag de ConfigSistema de ONLINE para ON.
No SoapUI, rodar o teste "Completo"
No Browser, testar Consulta Pública e os portais do Contribuinte, do Fazendário, do EPEC (ver links aqui)
Verificar números de versão no site e nos binários dos servidores (66,67,68)

IV) No Visual Studio (após o teste estar OK):
Fazer Check-In das mudanças, usar a Task 171272 ("Tasks"), comentário "Merge Dev->Main para publicação em Testes DI" 
Rodar build "NFC-e_incrementar_versao_AssemblyInfo__DEV"
Buscar "Ocorrências Aguardando Entrega" e mover todas para "Em Teste" com o Andre Kido

VI) Enviar e-mail conforme template abaixo

(Para copiar as ocorrências, fazer query do que foi movido para "Em Teste" , copiar para o Excel e remover as colunas que não aparecem no e-mail acima.)


-------------------------------------------------------------------------------------------------------------------------------------------------------------------------
Para: Saulo Rodrigues do Nascimento; Andre Ivo Kido; Fábio Loureiro Dickfeldt
Cc: nfcedesenv;
Assunto: Versão x.0.y.0 da NFC-e em Testes-DI

Pessoal, 

    Acabamos de colocar a versão x.0.y.0 da NFC-e no ambiente de testes da DI. As ocorrências a seguir foram resolvidas e designadas para o André Kido 

ID

Title

Caso de Uso

Created By

178228

NfeAutorizacao - Sistema está aceitando requisições com codificação XML diferente de UTF-8  (4.1.9.1-D03)

0

Andre Ivo Kido

 

Observações: nenhuma 

    Aguardamos um OK da DI para subirmos essa versão para homologação, nossa intenção é fazer isso na quarta-feira que vem, se possível.   

    []s,​

## [Deprecated] Antigo procedimento usando a ferramenta Release Management

A ferramenta Release Management não é mais suportada pela Microsoft. Desse modo, as instruções abaixo são mantidas apenas para lembrar de que precisamos atualizar essas instruções. As imagens foram deixadas de fora de proósito porque ocupavam muito espaço.

1) Na página inicial do ReleaseManagement, clicar em "Releases"
RM_TelaInicial.png

2) Clicar no botão "New" (selecionar o tipo default)
RM_NewRelease.png

3) Preencher os parâmetros conforme figura abaixo, e clicar no botão "Start"
RM_ParametrosDeploy.png


4) Na página de Release, clicar em "My Approval Requests"
RM_ApprovalRequests.png


5) Haverá uma linha de aprovação para cada passo do processo. 
A granularidade e quantidade de passos pode variar, no momento cada passo corresponde a um agrupamento de serviços semelhantes.
Ao clicar em Details" (botão com reticências "...") , é mostrado uma descrição do passo (imagem seguinte)
RM_ApprovalRequestsDetail.png


6) A descrição do passo serve apenas para informação. Para continuar, feche esse janela.
RM_ApprovalRequestsDetail2.png


7) Para aprovação do passo, selecione a linha a ser aprovada e clique no botão "Approve" 
RM_ApprovalRequestsAprovar.png

8) Aparecerá uma caixa pedindo confirmação e um comentário opcional - preencher esse comentário apenas se houver algo fora do normal:
RM_Confirm2.png


9) Após aprovar o passo, na tela Release, clicar em "View Log"
RM_ApprovalRequestsVerLogs.png


10) Clicar no botão "Details" (reticências)
RM_ViewDeployLog.png


11) Aparecerá uma listagem de todos os passos já feitos. Para saber o output do script, clicar em "View Log"
RM_ViewDeployLog2.png


12) O status de execução varia dependendo do que o script faz. No caso do "StopApplicationPool" (abaixo), mostra se o web pool do IIS foi parado.
No caso de script de backup, lista todos os arquivos salvos, etc.
RM_ViewDeployLog3.png


13) Assim que verificar que o passo foi executado corretamente, feche a janela "Deployment Log" para continuar as aprovações dos passos seguintes.
RM_ViewDeployLog4.png

14) Na janela Releases, clique novamente em "My Approval Requests"
e repita novamente as etapas de Aprovação/VerificarLogs, até que não restem mais passos pendentes
RM_ApprovalRequestsNovamente.png


14) Após aprovar e acompanhar todos os passos com sucesso, voltar à página de Release e clicar no botão "Release"
RM_Release.png




15) Caso algum dos passos deva ser rejeitado, deixe para rejeitar por último. 
Após qualquer rejeição, não é mais possível aprovar passo algum e todos os passos pedentes devem ser manualmente rejeitados.
RM_Confirm.png

 16) Caso algum passo não seja aprovado, deve-se Abandonar a release ao final do processo (mesmo que quase todos os passos tenham sido executados)
       Sem isso, a release fica com status pendente.
RM_Abandon.png



### Valores para Variáveis da Build Definition

------------------------------------------------------------------------------------------------------------------
WCF (no IIS)
BackupFolder: D:\Backup
DropFolder:   NFCe.WcfServiceApplication\_PublishedWebsites\NFCe.WcfServiceApplication
Hml PathName: D:\Inetpub\Secure\NFCe.WcfServiceApplication​
Hml PoolName: NFCe.WcfServiceApplicationPool

PortalFazendario (WebSite)
BackupFolder: D:\Backup
DropFolder:   NFCeUI.PortalFazendario\_PublishedWebsites\NFCeUI.PortalFazendario
Hml PathName: D:\inetpub\secure\NFCePortalFazendario
Hml PoolName: NFCePortalFazendarioPool

------------------------------------------------------------------------------------------------------------------​ 
Portal NFC-e (WebSite)
BackupFolder: D:\Backup\RM\
DropFolder:   NFCeUI.Portal\_PublishedWebsites\NFCeUI.Portal
Hml ​PathName: X
Hml PoolName: X

ConsultaPublica (WebSite)
BackupFolder: D:\Backup
DropFolder:   NFCeUI.ConsultaPublica\_PublishedWebsites\NFCeUI.ConsultaPublica
Hml PathName: D:\inetpub\secure\NFCeConsultaPublica
Hml PoolName: NFCeConsultaPublicaPool

SiteContribuinte (WebSite)
BackupFolder: D:\Backup
DropFolder:   NFCeUI.SiteContribuinte\_PublishedWebsites\NFCeUI.SiteContribuinte
Hml PathName: D:\inetpub\secure\NFCeSiteContribuinte
Hml PoolName: NFCeSiteContribuinte

NFCe WS (Web Service)
BackupFolder: D:\Backup
DropFolder:   NFCeWebService\_PublishedWebsites\NFCeWebService
Hml PathName: D:\inetpub\WebServices\ws
Hml PoolName: NFCewsPool

------------------------------------------------------------------------------------------------------------------
NFCe WinSvc (Windows Service)
BackupFolder: D:\Backup
DropFolder:   NFCeWindowsService
Hml PathName: D:\Program Files (x86)\SEFAZ_SP\NFCeWindowsService
ServiceName:  Sefaz.NFCeWindowsService

------------------------------------------------------------------------------------------------------------------
LCR (Windows Service)
BackupFolder: D:\Backup
DropFolder:   LCRService
Hml PathName: D:\Program Files\Sefaz\Sefaz.NFCe.LCR.Service
ServiceName:  Sefaz.NFCeLCRService

Verificação Sat (Windows Service)
BackupFolder: D:\Backup
DropFolder:   NFCeVerificacaoSAT 
Hml PathName: D:\Program Files (x86)\Sefaz_SP\NFCeVerificacaoSAT
ServiceName:  Sefaz.NFCeVerificacaoSAT 
  
------------------------------------------------------------------------------------------------------------------
Adm EPEC (WebSite)
BackupFolder: D:\Backup
DropFolder:   NFCeUI.AdministracaoServicoEPEC\_PublishedWebsites\NFCeUI.AdministracaoServicoEPEC
Hml PathName: E:\inetpub\Secure\NFCeAdministracaoServicoEPEC
Hml PoolName: NFCeAdministracaoServicoEPEC_Pool

------------------------------------------------------------------------------------------------------------------
EPEC (WebService)
BackupFolder: D:\Backup
DropFolder:   EPEC_WebService\_PublishedWebsites\EPEC_WebService
Hml PathName: D:\inetpub\secure\EPECws
Hml PoolName: EPECws

​------------------------------------------------------------------------------------------------------------------​
AtivaEPEC (WindowsService)
BackupFolder: D:\Backup
DropFolder:   NFCeAtivacaoEPEC
Hml PathName: D:\Program Files (x86)\SEFAZ_SP\NFCeAtivacaoEPEC
ServiceName:  Sefaz.NFCeAtivacaoEPEC

BloqueiaEPEC (WindowsService)
BackupFolder: D:\Backup
DropFolder:   NFCeBloqueioEPEC
Hml PathName: D:\Program Files (x86)\SEFAZ_SP\NFCeBloqueioEPEC
ServiceName:  Sefaz.NFCeBloqueioEPEC

ConciliaEPEC (Windows Service)
BackupFolder: D:\Backup
DropFolder:   NFCeConciliacaoEPEC
Hml PathName: D:\Program Files (x86)\SEFAZ_SP\NFCeConciliacaoEPEC
ServiceName:  Sefaz.NFCeConciliacaoEPEC

DW (Executável chamado pelo Control-M)
BackupFolder: D:\Backup
DropFolder:   Br.Gov.Sp.Fazenda.DFeDW.NFCeOutConsole
Hml PathName: D:\Program Files (x86)\SEFAZ_SP\NFCeExtratorDW
