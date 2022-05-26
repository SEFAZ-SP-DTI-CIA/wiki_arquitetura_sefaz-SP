# SCOM

O SCOM é um software que possui ampla quantidade de ferramentas de monitoração e foi configurado para verificar diversos pontos da NFC-e e do EPEC a fim de gerar alertas e informes para que as equipes responsáveis possam zelar pela disponibilidade dos sistemas.

Para a equipe de desenvolvimento a visão de produção é fornecida pela Console Web: http://consolescom/operationsmanager/ (o ambiente de homologação ainda está pendente de configuração).

A seguir listam-se as funcionalidades configuradas para NFC-e/EPEC:

![SCOMAplicacaoNFCeEPEC.jpg](/.attachments/SCOMAplicacaoNFCeEPEC-cfd09c1b-abee-44a5-b889-5ee044fa78ee.jpg)

## Aplicação NFC-e e EPEC (conjuntamente):

&nbsp; - Active/Closed Alerts: Lista de alertas abertos/fechados relativos a anormalidades da NFC-e ou EPEC que estejam degradando ou indisponibilizando as aplicações. São listados por ordem cronológica decrescente e sempre mostrando os alertas Críticos antes dos alertas de Warning (é possível filtrar Critical|Warning).

![SCOM_Alerts.jpg](/.attachments/SCOM_Alerts-bf87ecfe-fa1b-4b12-a25d-3f842244cff2.jpg)

 

&nbsp; - Estado da Base SQL AlwaysOn AG_EPEC01: Visão do estado (saúde) das bases que compõem o AG EPEC NFCE_EPEC_CONSTRAINT (SRVEPEC01CAS e SRVEPEC01).

![SCOMAG_EPEC01.jpg](/.attachments/SCOMAG_EPEC01-8fd8f46c-a9b0-4774-abde-4055e6cc265c.jpg)

 

&nbsp; - Estado da Base SQL AlwaysOn AG_NFCe01: Visão do estado (saúde) das bases que compõem o AG NFC-e NFCE_OUT_CONSTRAINT (SRVNFCE01CAS e SRVNFCE01).

![SCOMAG_NFCE01.jpg](/.attachments/SCOMAG_NFCE01-7685ee80-eb5d-4a7c-84c1-8dcf06898a16.jpg)

 

&nbsp; - TCP Port State: Monitoração de estado de todas as portas utilizadas para aos relacionamentos entres servidores presentes no modelo NFC-e/EPEC.

![SCOM_TCP_PORT_STATE.jpg](/.attachments/SCOM_TCP_PORT_STATE-7e9a8cb2-e5f2-464a-b0f9-e09bfa5013ea.jpg)


## Aplicação EPEC

&nbsp; - Active/Closed Alerts: Idem ao citado no tópico acima, só que contando apenas com eventos relativos a EPEC.

&nbsp; - EPEC Dashboard: Dashboard relativa aos contadores de desempenho do EPEC.

![SCOM_EPEC_Dashboard.jpg](/.attachments/SCOM_EPEC_Dashboard-529ce4f2-7c25-4f09-abfa-050914b0b817.jpg)


&nbsp; - OLE DB State: Monitoração periódica (a cada 10 minutos) de comunicação entre servidores (watcher nodes) e bancos de dados

![SCOM_EPEC_OLEDB.jpg](/.attachments/SCOM_EPEC_OLEDB-58942064-1496-46bf-a05c-ebdecb44eb63.jpg)


&nbsp; - Serviços: Monitoração dos Serviços do EPEC, por servidor (se estiver parado em determinado servidor é marcado com um Warning).

&nbsp; - Performance Counters: Permite a geração de gráficos personalizados selecionando os contadores de desempenho individualmente por cada servidor.

&nbsp; - Topologia: Simples representação gráfica da topologia do EPEC (conforme documento de desenho).

 

## Aplicação NFC-e

&nbsp; - Active/Closed Alerts: Idem ao citado no tópico acima, só que contando apenas com eventos relativos a NFC-e.

&nbsp; - .NET Distribuition Application: Visão de Aplicação Distribuida abrindo as camadas Web, App, Banco, com os devidos estados (saúde) de cada um.

![SCOM_DistribApp.jpg](/.attachments/SCOM_DistribApp-4dca96cd-8953-47c3-b4db-9eca4dea05d3.jpg)

&nbsp; - NFC-e Dashboards: Dashboards relativas aos contadores de desempenho da NFC-e, divididas de acordo com a seguinte lógica funcional:

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 1- Autorização Assíncrona Recepção

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 2- Autorização Assíncrona Fila

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 3- Autorização Assíncrona Processamento

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 4- Autorização Síncrona

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 5- Cancelamento

![SCOM_NFCe_Dashboard.jpg](/.attachments/SCOM_NFCe_Dashboard-db3045b7-0d20-45bb-8d70-703968ddcad5.jpg)


&nbsp; - OLE DB State: Monitoração periódica (a cada 10 minutos) de comunicação entre servidores (watcher nodes) e bancos de dados.

&nbsp; - Serviços: Monitoração dos Serviços da NFC-e, por servidor (se estiver parado em determinado servidor é marcado com um Warning).

![SCOM_NFCe_Servicos.jpg](/.attachments/SCOM_NFCe_Servicos-6465009c-19f4-474b-92a0-ebf19ddd2bb8.jpg)

&nbsp; - Performance Counters Web/App: Permite a geração de gráficos personalizados selecionando os contadores de desempenho relativos aos servidores Web/App da NFC-e individualmente por cada servidor.

![SCOM_NFCe_WebLotePerf.jpg](/.attachments/SCOM_NFCe_WebLotePerf-edc935f1-ad86-4288-a3fb-5d90367a2c53.jpg)


 

&nbsp; - Web Sites: Monitoração do estado (saúde) dos Web Sites da NFC-e.

![SCOM_NFCe_Websites.jpg](/.attachments/SCOM_NFCe_Websites-1dd8136d-b254-49ac-9c0e-cf3074641765.jpg)

&nbsp; - Topologia: Simples representação gráfica da topologia da NFC-e (conforme documento de desenho).

 

## Alertas Customizados

Durante a montagem da monitoração da NFC-e e EPEC surgiram algumas necessidades de alertas customizados:

&nbsp; - Alerta Monitoração de Cluster do Serviço LCR: avisa quando os dois nós do cluster estão com o serviço LCR ativo.

&nbsp; - Alerta Monitoração de Cluster dos Serviços EPEC: avisa quando os dois nós do cluster estão com os mesmos serviços do EPEC ativos (Ativação, Bloqueio e Conciliação)

&nbsp; - Alerta Monitoração de Cluster do Serviço Verificação SAT: avisa quando os dois nós do cluster estão com o serviço de Verificação SAT ativo.

&nbsp; - Alertas para o Teste de Monitoração NFC-e Status Serviço: informam problemas em retornos do teste periódico (interno e visão contribuinte) relativos ao Status da NFC-e

&nbsp; - Alertas para o Teste de Monitoração EPEC Status Serviço: informam problemas em retornos do teste periódico (interno e visão contribuinte) relativos ao Status do EPEC

&nbsp; - Alertas para o Teste de Monitoração de Comunicação com WCF: informam se há algum problema com a comunicação entre servidores Web da NFC-e -> WCF -> Bancos de Dados.

&nbsp; - Alertas para o Teste de Monitoração Sonda NFC-e: avisam quando o valor do bit nfceAtiva é diferente de 1, significando comportamento anômalo da NFC-e. 

 

## APM - Application Performance Monitor

O APM´é um módulo destinado a monitorar desempenho da aplicação por meio de indicadores do tipo: threshold de tempo de cada requisição, percentuais de eventos com exceções por segundo, percentuais de eventos que estouraram o tempo de threshold dentre outros.

Na NFC-e (produção) está configurado para os seguintes componentes:

![SCOM_APM.jpg](/.attachments/SCOM_APM-fe981bdc-8840-48dd-aa50-b64611775350.jpg)

Como padrão de configuração tem-se:

&nbsp; - Web Sites geram alertas de performance caso o tempo de requisição estoure **15.000ms (Warning)**

&nbsp; - Web Services geram alertas de performance caso o tempo de requisição estoure **3.000ms (Warning)**

No caso da Autorização de NFC-e e de EPEC, criou-se um alerta crítico para as requisições que ultrapassarem 30.000ms
(atualmente esse tempo foi reduzido para 14.000ms para poder registrar mais facilmente eventos ocasionados por problemas com certificados que estão sendo investigados).

Apesar do nome se referir a Performance, **Exceções** ocorridas no funcionamento dessas aplicações também **geram alertas** que devem ser investigados.

 

## Monitoração por Namespaces e Métodos

Há ainda uma monitoração orientada por Namespaces e por Métodos presentes na aplicação.

Na NFC-e a configuração padrão fica da seguinte maneira:

&nbsp; - Namespaces: monitoração padrão ligada para todos os namespaces;

&nbsp; - Métodos: monitoração específica nos métodos essenciais para a Autorização de NFC-e e EPEC, partindo desde o método mais geral presente nos Web Services.

Todos os métodos possuem threshold de 3.000ms (devem ser registrados pela monitoração caso ultrapassem esse tempo).

Quando o threshold é ultrapassado, liga-se a varredura top-down do método para compor o detalhamento de tempos utilizados em cada chamada dali em diante. Essa informação fica amarrada ao evento gerado pelo APM e deve ser visualizado pelo App Diagnostics.

Ultimamente colocou-se métodos relativos ao BuildChain da classe X509Certificates como tentativa de obter alguma informação mais apurada do problema recorrente de atrasos ocasionados pela validação das cadeis de certificado do cliente.

 

## App Diagnostics

O App Diagnostics é uma ferramenta que permite visualizar todos os eventos registrados de performance ou erros (exceções) relativas a uma determinada aplicação.

O acesso se dá pelo endereço: http://consolescom/appdiagnostics/

Para a NFC-e e EPEC foram montados os grupos NFCE PRD e EPEC PRD (a configuração de homologação ainda está pendente).

"NFCE PRD":

![APP_Diagnostics_NFCePRD.jpg](/.attachments/APP_Diagnostics_NFCePRD-c05200bd-3150-4591-b349-713b16f584c4.jpg)


"EPEC PRD":

![APP_Diagnostics_EPECPRD.jpg](/.attachments/APP_Diagnostics_EPECPRD-1dbbe3b9-aaea-4e86-b1b3-36c9129cafc1.jpg)


Em cada um desses grupos é possível visualizar os eventos de Performance ou de Erros de Aplicação.

Exemplo de Eventos de Performance da NFC-e:

![APP_Diagnostics_Performance.jpg](/.attachments/APP_Diagnostics_Performance-5331707b-0486-4cb2-88cb-3d73a845fec9.jpg)

Exemplo de Eventos de Erro de Aplicação da NFC-e:

![APP_Diagnostics_Errors.jpg](/.attachments/APP_Diagnostics_Errors-4afb0c5e-3226-4c77-9c13-e1e8db7d81e9.jpg)

Como pode-se observar são gerados muitos eventos, as vezes sendo erros até mesmo gerados por má utilização do cliente, como no caso de tentar acessar uma página inexistente.

Para uma melhor busca, a ferramenta permite que se filtre uma série de detalhes sobre o evento, como por exemplo:

&nbsp; - definir intervalos de datas da ocorrência do evento;

&nbsp; - eventos ainda não visualizados;

&nbsp; - duração do evento (maior ou menor que...)

&nbsp; - aplicação a qual o evento se refere;

&nbsp; - servidor da aplicação em que ocorreu o evento;

&nbsp; - IP do cliente;

&nbsp; - Sessão do cliente.

Para cada um desses eventos ainda é possível clicar sobre o mesmo e abrir uma nova janela contendo informações detalhadas.

![APP_Diagnostics_Event.jpg](/.attachments/APP_Diagnostics_Event-c0565cd5-f808-47a5-8db8-7d64c0c9d5b0.jpg)


No caso de eventos de performance é possível visualizar a árvore de chamadas, contendo os métodos e seus respectivos tempos. Isso só não é possível quando o evento é classificado como light event (ainda há uma investigação sobre como forçar os eventos a serem todos detalhados).

Exemplo de detalhe da árvore de chamadas dos métodos:

![APP_Diagnostics_EventPerformance.jpg](/.attachments/APP_Diagnostics_EventPerformance-09f3f8b9-c727-4b9d-9563-29b2dc08aaac.jpg)