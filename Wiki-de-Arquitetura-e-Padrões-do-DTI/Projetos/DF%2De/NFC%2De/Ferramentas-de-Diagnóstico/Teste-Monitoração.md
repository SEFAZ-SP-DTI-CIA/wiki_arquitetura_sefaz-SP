**Time de monitoração:** Clique [​AQUI](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/368/Instru%C3%A7%C3%B5es-Para-Time-Monitora%C3%A7%C3%A3o) para instruções mais específicas

[[_TOC_]]

# Teste Monitoração NFC-e ​
  
O projeto TesteMonitoracaoNFCe ou simplesmente Teste Monitoração trata-se de uma aplicação console que agrega alguns testes de diagnósticos utilizados como suporte para a geração de alertas no SCOM relativos à NFC-e e EPEC.
 
A idéia básica da aplicação é oferecer os seguintes tipos de teste (entre parênteses segue o argumento utilizado para chamar o respectivo teste):
 
&nbsp; - Status do Serviço NFC-e versão 3.10 (**nfcestatusservico**)
&nbsp; - Status do Serviço NFC-e versão 4.00 (**nfcestatusservico4**)
&nbsp; - Status do Serviço EPEC (**epecstatusservico**)
&nbsp; - Teste de Comunicação com WCF (**wcf**)
&nbsp; - Verificação da última leitura da Sonda de Monitoração NFC-e (**sonda**)
&nbsp; - Verificação do serviço SAT Ativo (**sat**)
 
A aplicação também permite escolher o ambiente testado através de um segundo argumento passado na chamada da aplicação:
&nbsp; - Localhost (**localhost**)
&nbsp; - Desenvolvimento - SRV72069 (**debug**)
&nbsp; - Testes DI (**release**)
&nbsp; - Homologação (**homolog**)
&nbsp; - Produção (**prod**)
 
Exemplos:
&nbsp; - para consultar o status do serviço EPEC de Produção deve-se fazer a seguinte chamada: "TesteMonitoracaoNFCe.exe epecstatusservico prod"
&nbsp; - para verificar a ultima leitura da sonda de homologação deve-se fazer a seguinte chamada: "TesteMonitoracaoNFCe.exe sonda homolog"
 
O executável descrito acima encontra-se instalado nas máquinas abaixo descritas no diretório D:\Util\Scripts_Monitoracao_scom .
 
# 1) Status Serviço NFC-e
 
O teste de Status Servico da NFC-e é feito utilizando-se do próprio web service NfeStatusServico2.asmx, carregando um certificado cliente específico para testes que foi fornecido pelo COI - "Monitor_EPEC_CadeiaCompleta.pfx".
 
Para a sua utilização no SCOM foram montados em homologação e produção testes da seguinte forma:
 
# 1.1) Testes individuais
 
Cada servidor Web da NFC-e roda periodicamente (a cada 2 minutos) o Teste do Status Serviço NFC-e e registra o retorno no log de eventos do windows.
 
Os seguintes eventos de retorno são possíveis:

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; - NFCeStatusServico_Retorno_Good = 30000
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; - NFCeStatusServico_Retorno_Bad  = 30001
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; - NFCeStatusServico_Excecao      = 30002
 
O retorno esperado nesse caso é sempre o status "Good". Caso seja um dos demais, Bad ou Exceção, haverá disparo de um alerta do tipo **Crítico** no SCOM informando que aquele determinado servidor não obteve sucesso no teste e solicitando a verificação do motivo.
Caso o resultado persista em "Bad" envolver equipe de Conexão.
 
Servidores nos quais o teste é executado:
&nbsp; - Produção: SRV72131, SRV72132, SRV72133, SRV72134
&nbsp; - Homologação: SRV72085, SRV72086, SRV72087, SRV72088
 
# 1.2) Teste de simulação de uso pelo contribuinte
 
Nesse caso utiliza-se um servidor Web do EPEC para disparar o teste de Status da NFC-e para simular o uso por um contribuinte (a cada 2 minutos).
 
Os eventos de retorno são mesmos que do teste acima:

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; - NFCeStatusServico_Retorno_Good = 30000
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; - NFCeStatusServico_Retorno_Bad  = 30001
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; - NFCeStatusServico_Excecao      = 30002
 
O retorno esperado também é sempre o status "Good". Caso seja um dos demais, Bad ou Exceção, haverá disparo de um alerta do tipo Critical no SCOM informando que o Serviço da NFC-e pode estar indisponível.
 
Servidores nos quais o teste é executado:
      Produção: SRV11083
      Homologação: SRV11030
 
# 2) Status Serviço EPEC
 
O teste de Status Serviço EPEC é feito utilizando-se do próprio Web Service EPECStatusServico.asmx, carregando o certificado cliente específico fornecido pelo COI - "Monitor_EPEC_CadeiaCompleta.pfx"
 
Para a sua utilização no SCOM foram montados em homologação e produção testes da seguinte forma:
 
## 2.1) Testes individuais
 
Cada servidor Web do EPEC roda periodicamente (a cada 2 minutos) o Teste do Status Serviço EPEC e registra o retorno no log de eventos do windows.
 
Os seguintes eventos de retorno são possíveis:

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; - EPECStatusServico_Retorno_Good = 30010
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; - EPECStatusServico_Retorno_Bad  = 30011
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; - EPECStatusServico_Excecao      = 30012

O retorno esperado nesse caso é o status "Bad", pois o EPEC normalmente fica desativado por se tratar de uma contingência à NFC-e. Caso seja um dos demais, Good ou Exceção, haverá disparo de um alerta do tipo Crítico no SCOM informando que aquele determinado servidor não obteve o retorno esperado para o teste e solicitando a verificação do motivo (EPEC ligado ou exceção).
 
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
 
Nesse caso os retornos possíveis são:
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; - WCF_Retorno_Good = 30020
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; - WCF_Retorno_Bad  = 30021
 
Quando há o retorno Bad, um alerta to tipo **Crítico** é gerado informando que o determinado servidor está com problemas de comunicação com o WCF e solicitando a análise da questão.
 
Servidores nos quais o teste é executado:
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Produção: SRV72131, SRV72132, SRV72133, SRV72134
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Homologação: SRV72085, SRV72086, SRV72087, SRV72088
 
# 4) Verificação da Última Leitura da Sonda Monitoração NFC-e
 
Esse teste apenas faz uma consulta periódica (a cada 5 minutos) em banco via WCF e traz os registros da última leitura realizada pela Sonda Monitoração NFC-e.
 
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
 
Script:
`cd D:\Util\Scripts_Monitoracao_scom`
`.\TesteMonitoracaoNFCe.exe servicoAtivacaoEPEC`


Os retornos possíveis são:
 
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; - Servico_Ativacao_EPEC_Good                = 30060
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; - Servico_Ativacao_EPEC_Bad                 = 30061
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; - Servico_Ativacao_EPEC_Excecao             = 30062
 
O retorno esperado é Good. Quando o retorno é Bad ou Exceção, um alerta do tipo **Warning** é disparado solicitando a verificação do Serviço de Ativação do EPEC.
 
Servidores nos quais o teste é executado:
 
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Produção: SRV11083
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Homologação: SRV11030
