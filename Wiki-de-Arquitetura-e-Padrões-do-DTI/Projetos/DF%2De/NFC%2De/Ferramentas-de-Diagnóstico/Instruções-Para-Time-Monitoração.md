# Instruções sobre Teste Monitoração NFC-e 

[[_TOC_]]

## Qual a mensagem do alerta?

Se _"EPEC simulacao para utilizacao do EPEC disponivel para o contribuinte"_  ver **Item 0) Status Serviços NFC-e/EPEC**
Se _"NFCe simulacao interna de comunicacao WCF indisponivel"_               ver **Item 3) Teste de Comunicação com WCF**
Se _"NFCe PRD Alerta na Sonda TesteMonitoracaoNFCe"_                            ver **Item 4) Verificação da Última Leitura da Sonda Monitoração NFC-e**

## 0) Status Serviços NFC-e/EPEC

O Alerta "**EPEC simulacao para utilizacao do EPEC disponivel para o contribuinte**" é gerado por um ou mais dos motivos abaixo:
Motivo i) Falha de comunicação entre São Paulo e Campinas
​Motivo ii) Intermitência (geralmente BD) no sistema EPEC em São Paulo
Motivo iii) Intermitência (geralmente BD) no sistema NFC-e em Campinas
Motivo iv) Detecção de irregularidade na SONDA
Motivo v) Falha de execução na SONDA
Motivo vi) Falha crítica no sistema EPEC em São Paulo
Motivo vii) Falha crítica no sistema NFC-e em Campinas

### O diagnóstico pode ser feito da seguinte forma:
Verificar site do EPEC (https://sefaznet9.sede.fazenda.sp.gov.br/NFCeAdministracaoServicoEPEC/) item "HISTÓRICO", e verificar a mensagem de ativação. Para cada caso:
&nbsp; - _"Falha verificando NFC-e online"_                                                                        -> Motivo i, iii ou vii
&nbsp; - _"Ligação preventiva devido a falha de verificação em agendamento em execução"_ -> Motivo ii ou vi
&nbsp; - _"Ligação preventiva devido a falha em banco de dados NFC-e"_                             -> Motivo iii ou vii
&nbsp; - "Ligação preventiva devido à sinalização da Sonda de que a NFC-e está offline"     -> Motivo iv ou v
&nbsp; - Qualquer outra mensagem de ativação (agendamento, ativação manual, etc)        -> Não há motivo de erro associado

### O que fazer para confirmar a suspeita de cada motivo de erro:
Motivo i)   rodar teste **1) Status Serviço NFC-e**
Motivo ii)  aguardar 20 minutos e rodar teste **2) Status Serviço EPEC**​ (veja que o status esperado nesse caso é "Bad")
Motivo iii) rodar teste **1) Status Serviço NFC-e**
Motivo iv) ​rodar teste **4) Verificação da Última Leitura da Sonda Monitoração NFC-e​**
Motivo v)  rodar teste **4) Verificação da Última Leitura da Sonda Monitoração NFC-e​**
Motivo vi) nesse caso, nada do EPEC estará funcionando, um teste possível é verificar se o site do EPEC está fora do ar
Motivo vii)nesse caso, nada da NFC-e estará funcionando, rodar teste **1) Status Serviço NFC-e** e tentar abrir o Portal NFC-e (http://www.nfce.fazenda.sp.gov.br/NFCePortal/)


# 1) Status Serviço NFC-e
 
O teste de Status Servico da NFC-e é feito utilizando o executável instalado nas máquinas da NFC-e, no diretório D:\Util\Scripts_Monitoracao_scom
 Para a sua utilização no SCOM foram montados em homologação e produção testes da seguinte forma:
 
## 1.1) Testes individuais
 
Cada servidor Web da NFC-e roda periodicamente (a cada 2 minutos) o Teste do Status Serviço NFC-e e registra o retorno no log de eventos do windows.

Para rodar manualmente o comando, ver exemplo abaixo.
_Exemplos:_
&nbsp; - para consultar o status do serviço NFC-e de Produção deve-se fazer a seguinte chamada: "TesteMonitoracaoNFCe.exe nfcestatusservico prod" 
&nbsp; - para consultar o status do serviço NFC-e de Homologação deve-se fazer a seguinte chamada: "TesteMonitoracaoNFCe.exe nfcestatusservico homolog" 

Os seguintes eventos de retorno são possíveis:
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; - NFCeStatusServico_Retorno_Good = 30000
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; - NFCeStatusServico_Retorno_Bad  = 30001
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; - NFCeStatusServico_Excecao      = 30002
 
O retorno esperado nesse caso é sempre o status "Good". Caso seja um dos demais, Bad ou Exceção, haverá disparo de um alerta do tipo **Crítico** no SCOM informando que aquele determinado servidor não obteve sucesso no teste e solicitando a verificação do motivo.
Caso o resultado persista em "Bad" envolver equipe de Conexão.
 
Servidores nos quais o teste é executado:
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; - Produção: SRV72131, SRV72132, SRV72133, SRV72134
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; - Homologação: SRV72085, SRV72086, SRV72087, SRV72088
 
## 1.2) Teste de simulação de uso pelo contribuinte
 
Nesse caso utiliza-se um servidor Web do EPEC para disparar o teste de Status da NFC-e para simular o uso por um contribuinte (a cada 2 minutos).
 
Os eventos de retorno são mesmos que do teste acima:
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; - NFCeStatusServico_Retorno_Good = 30000
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; - NFCeStatusServico_Retorno_Bad  = 30001
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; - NFCeStatusServico_Excecao      = 30002
 
O retorno esperado também é sempre o status "Good". Caso seja um dos demais, Bad ou Exceção, haverá disparo de um alerta do tipo Critical no SCOM informando que o Serviço da NFC-e pode estar indisponível.
 
Servidores nos quais o teste é executado:
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Produção: SRV11083
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Homologação: SRV11030
 
# 2) Status Serviço EPEC
 
Para rodar manualmente o comando, ver exemplo abaixo.
**Exemplos:** 
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; - para consultar o status do serviço EPEC de Produção deve-se fazer a seguinte chamada: "TesteMonitoracaoNFCe.exe epecstatusservico prod"
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; - para consultar o status do serviço EPEC de Homologação deve-se fazer a seguinte chamada: "TesteMonitoracaoNFCe.exe epecstatusservico homolog"


Para a sua utilização no SCOM foram montados em homologação e produção testes da seguinte forma:
 
## 2.1) Testes individuais
 
Cada servidor Web do EPEC roda periodicamente (a cada 2 minutos) o Teste do Status Serviço EPEC e registra o retorno no log de eventos do windows.
 
Os seguintes eventos de retorno são possíveis:

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; - EPECStatusServico_Retorno_Good = 30010
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; - EPECStatusServico_Retorno_Bad  = 30011
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; - EPECStatusServico_Excecao      = 30012

O retorno esperado nesse caso é o status "Bad", pois o EPEC normalmente fica desativado por se tratar de uma contingência à NFC-e. Caso seja um dos demais, Good ou Exceção, haverá disparo de um alerta do tipo **Crítico** no SCOM informando que aquele determinado servidor não obteve o retorno esperado para o teste e solicitando a verificação do motivo (EPEC ligado ou exceção).
 
Servidores nos quais o teste é executado:
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Produção: SRV11083, SRV11084, SRV11085, SRV11086
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Homologação: SRV11030, SRV11031
 
## 2.2) Teste de simulação de uso pelo contribuinte
 
Nesse caso utiliza-se um servidor Web da NFC-e para disparar o teste de Status do EPEC para simular o uso por um contribuinte (a cada 2 minutos).


Os eventos de retorno são mesmos que do teste acima:

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; - EPECStatusServico_Retorno_Good = 30010
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; - EPECStatusServico_Retorno_Bad  = 30011
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; - EPECStatusServico_Excecao      = 30012

O retorno esperado também é sempre o status "Bad". Caso seja um dos demais, Good ou Exceção, haverá disparo de um alerta do tipo **Warning** no SCOM informando que o Serviço do EPEC foi ligado e pode significar a indisponibilidade da NFC-e, no caso do retorno Good e um alerta do tipo **Warning** informando falha no teste em caso do retorno ser uma Exceção.
 
Servidores nos quais o teste é executado:
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Produção: SRV72131
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Homologação: SRV72085
 
## 2.3) Alerta de Oscilação no status do Serviço EPEC
 
Para complementar a monitoração da contingência EPEC e por consequência da disponibilidade da NFC-e, foi criado um alerta de janela temporal, que observa quantidade de eventos dentro de um prazo determinado.
 
Estipulou-se que seria de bom grado um disparo de alerta do tipo **Crítico** quando o SCOM detectasse numa janela de 48 horas que o EPEC tenha ficado Online e Offline de maneira oscilante por mais de 10 vezes.
 
# 3) Teste de Comunicação com WCF
 
O teste de comunicação com WCF é realizado periodicamente (a cada 2 minutos) verificando se é possível realizar uma consulta em banco percorrendo o caminho Servidor Web -> WCF -> Banco.

Para rodar manualmente, seguir o exemplo abaixo.
**Exemplos:** 
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; - para verificar a ultima leitura WCF de produção deve-se fazer a seguinte chamada: "TesteMonitoracaoNFCe.exe wcf prod"
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; - para verificar a ultima leitura WCF de homologação deve-se fazer a seguinte chamada: "TesteMonitoracaoNFCe.exe wcf homolog"

 
Nesse caso os retornos possíveis são:
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; - WCF_Retorno_Good = 30020
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; - WCF_Retorno_Bad  = 30021
 
Quando há o retorno Bad, um alerta to tipo **Crítico** é gerado informando que o determinado servidor está com problemas de comunicação com o WCF e solicitando a análise da questão.
 
Servidores nos quais o teste é executado:
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Produção: SRV72131, SRV72132, SRV72133, SRV72134
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Homologação: SRV72085, SRV72086, SRV72087, SRV72088
 
# 4) Verificação da Última Leitura da Sonda Monitoração NFC-e
 
Esse teste apenas faz uma consulta periódica (a cada 5 minutos) em banco via WCF e traz os registros da última leitura realizada pela Sonda Monitoração NFC-e.

Para rodar manualmente, seguir o exemplo abaixo.
**Exemplos:** 
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; - para verificar a ultima leitura da sonda de produção deve-se fazer a seguinte chamada: "TesteMonitoracaoNFCe.exe sonda prod"
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; - para verificar a ultima leitura da sonda de homologação deve-se fazer a seguinte chamada: "TesteMonitoracaoNFCe.exe sonda homolog"

 
O retorno é simplificado utilizando a flag nfceAtiva, sendo esta calculada pela própria procedure da Sonda (nfceAtiva = 1 -> good | nfceAtiva = 0 -> bad).
Sendo assim, seguem os possíveis retornos:
 
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; - Sonda_Retorno_Good = 30040
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; - Sonda_Retorno_Bad  = 30041
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; - Sonda_Excecao      = 30042
 
O retorno esperado é sempre Good, que significa segundo a lógica da Sonda que a NFC-e está com comportamento considerado normal.
 
Caso haja um retorno Bad, um alerta do tipo **Crítico** é disparado solicitando a verificação de um possível problema com a NFC-e devido a variação comportamental fora do padrão estimado.
 
No caso de retornar uma Exceção, há geração de um alerta do tipo **Warning**, solicitando as devidas medidas cabíveis para que o teste volte a funcionar adequadamente.
 
Servidores nos quais o teste é executado:
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Produção: SRV72131
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Homologação: SRV72085

**No caso  de retorno Bad:** Solicitar à equipe DTI-DBA para verificar e retornar o resultado da “Sonda NFC-e” (query abaixo):


----------------------------------------------------------------------------------------------------------------------------

_--DTI-DBA: colocar também os nomes dos campos junto dos respectivos valores na resposta da consulta_

`SELECT TOP 1 * FROM [NFCe_Out].[NFCeOut].[SondaMonitoracaoSefaz] (NOLOCK) order by timestampReg desc`

----------------------------------------------------------------------------------------------------------------------------

_SE_ o campo “nroErrosDownloadsLCR” retornado pelo DTI-DBA estiver com valor > 15 _ENTÃO_
​Enviar a informação para DTI-Windows, que utilizará na avaliação do problema.
_SENÃO_
Enviar a informação para “nfcedesenv”, que utilizará na avaliação do problema.
 
# 5) Verificação do Serviço SAT Ativo
 
Esse teste verifica o retorno do web-service SAT-Ativo periodicamente (a cada 2 minutos):
 
&nbsp; - https://wssatsp.fazenda.sp.gov.br/SatAtivo/SatAtivo.asmx (ambientes de produção e homologação)
&nbsp; - https://wssatsptst.fazenda.sp.gov.br/SatAtivo/SatAtivo.asmx (ambientes da DI e desenvolvimento)
 
Nesse caso os retornos possíveis são:
 
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; - SAT_Ativo_Good    = 30050
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; - SAT_Ativo_Bad     = 30051
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; - SAT_Ativo_Excecao = 30052
 
O retorno esperado é Good. Quando o retorno é Bad ou Exceção, um alerta do tipo **Warning** é disparado solicitando a verificação desse serviço provido pelo SAT.
 
Servidores nos quais o teste é executado:
 
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Produção: SRV72139, SRV72140, SRV72141 e SRV72142
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Homologação: SRV72106, SRV72107, SRV72100 e SRV72108
 
​
# 6) Verificação do Serviço de Ativação do EPEC
 
Esse teste verifica se o Serviço de Ativação do EPEC está registrando que está em funcionamento ('ping').
 
Os retornos possíveis são:
 
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; - Servico_Ativacao_EPEC_Good                = 30060
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; - Servico_Ativacao_EPEC_Bad                 = 30061
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; - Servico_Ativacao_EPEC_Excecao             = 30062
 
O retorno esperado é Good. Quando o retorno é Bad ou Exceção, um alerta do tipo **Warning** é disparado solicitando a verificação do Serviço de Ativação do EPEC.
 
Servidores nos quais o teste é executado:
 
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Produção: SRV11083
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Homologação: SRV11030
