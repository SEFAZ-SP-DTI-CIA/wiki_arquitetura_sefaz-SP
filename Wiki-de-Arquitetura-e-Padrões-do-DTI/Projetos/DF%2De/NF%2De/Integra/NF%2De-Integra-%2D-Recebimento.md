**NFeRecebimento**
 
Utiliza o serviço NFeDistribuicaoRFB2, passando indNSU=1 (NSU dos DF-e de interesse da UF que não foram autorizados pela UF)
Retorna NSU e NSUAN
- NSU (número único para o estado interessado - BUSCA pois indNSU=1), armazenado em
  - [NFe_Out_UFs].[dbo].[IntegraNSU], coluna nsuBusca , no caso de recebimento de outras UFs
  - [NFe_Out].[NFeIntegra].[IntegraNSU], coluna nsuAutorizacaoBusca (sinalizando fkIntegraTipo=3,4 ou 5), no caso de SCAN
- NSUAN (número único no AN todo), armazenado em
  - [NFe_Out_UFs].[dbo].[IntegraNSU]              , coluna NSU (chave)
  - [NFe_Out_UFs].[dbo].[NFe]                     , coluna NSU (chave)
  - [NFe_Out_UFs].[dbo].[ProtocoloNFe]            , coluna NSU (chave)
  - [NFe_Out_UFs].[dbo].[cancNFe]                 , coluna NSU (chave)
  - [NFe_Out_UFs].[dbo].[DadosTransmissao_AN]     , coluna NSU (chave)
  - [NFe_Out_UFs].[NFeUFs].[DFeInfoCompl]         , coluna NSU (chave)
  - [NFe_Out_UFs].[NFeUFs].[DFeRecebimento]       , coluna NSU 
  - [NFe_Out_UFs_XML].[dbo].[DFe_XML]             , coluna NSU (chave)
  - [NFe_Out_UFs_XML].[NFeEventos].[IntegraEvento], coluna NSU (chave)

_Nota_:
 - ​fkintegraTipo = 1 => Envio
 - fkintegraTipo = 2 => Recebimento - Outras UFs
 - fkintegraTipo = 3 => Recebimento Contigência-SCAN ou SVC-AN
 - fkintegraTipo = 4 => Recebimento - Eventos Ambiente Nacional
 - fkintegraTipo = 5 => Recebimento - Eventos Contingência AN

_Principais Tabelas:_
 - [NFe_Out_UFs].[dbo].[LOG_XML] - log da fila do Recebimento e NSUFaltantes. Deve-se analisar erros na coluna LOXM_TX_ERRO.
 - [NFe_Out_UFs].[dbo].[LOG_XML_QUEUE] - fila de registros sendo processados pelo Recebimento e NSUFaltantes. Não pode ser grande.
 
### 1) Thread "thrExecute" -> ControlExecute()
 
São 5 threads desse tipo, cada uma responsável por buscar documentos no AN por uma faixa de NSUs. 
Existem 5 faixas de NSU, e o que determina a faixa do NSU é o quinto dígito do NSU contado da direita para a esquerda (em homologação e desenvolvimento é o terceiro).
Assim o NSU 00030000 pertence à faixa 3, assim como o NSU 00080000. O NSU 000000000 e o NSU 000500000 pertencem à faixa 0. E assim por diante.
Cada thread thrExecute[i] requisita apenas NSUs da sua faixa. Quando chega no final da faixa, ela pula para o próximo NSU da mesma faixa, ou seja, 50000 a mais, e pede NSUs a partir daí.

Loop da thread "i":

 - Faz algumas verificações se deve rodar, só seguindo em frente quando todas as condições forem satisfeitas
   - Verifica se o Extrator do DW está extraindo dados das bases NFe_Out_UFs / NFe_Out_UFs_XML
   - Verifica se a thread "i" está muito adiantada em relação à thread mais atrasada
   - Verifica se a fila do Recebimento está muito grande 
 - Gera XML Versão 2.00 (quase sempre o mesmo, só muda o ultNSU, atualizado por RecebimentoConfigurationEntity2.NSU_INICIAL[i] )
 - _Conceito_: o ultNSU é o que se chama "NSU-Busca", o qual é gerado "por UF"
 - Envia request (NFeDistribuicaoRFB2.asmx/nfeDistribuicaoLote2) para o AN
 - _Conceito_: existe o "NSUAN", retornado no serviço, é o antigo "NSU diferente para todos os documentos"
 - Valida resposta:
   - casos de erro: gera exceção que insere tanto o request, a resposta e o erro nas tabelas [NFe_Out_UFs].[dbo].[LOG_XML]
   - casos ok (cStat: 117 nenhum documento / 118 algum documento)
     - 117: 
       - Se o NSU pedido for diferente do NSU retornado, incrementa-se contador de erro. Isso não deveria acontecer, pois estou pedindo um NSU, o AN diz que não tem (stat 117) mas retorna um outro valor.
       - Se o contador de erro exceder um limite, grava-se o NSU retornado e zera-se o contador de erro.
         - Procedure usp_NFeIntegra_Salvar_Ultimo_NSU: grava o NSU na tabela [dbo].[ULTIMO_NSU_RECEBIMENTO] de acordo com a faixa
     - 118:
       - Se o lote veio com buracos, incrementa contador de erro 
       - Se o lote não veio com buracos ou se o contador de erros atingiu limite
         - Grava o lote em banco, procedure [dbo].[usp_NFeOutUFs_LOG_XML_Create]:
           - Insere o request e a resposta nas tabelas [NFe_Out_UFs].[dbo].[LOG_XML]; se não houve erro, insere na [NFe_Out_UFs].[dbo].[LOG_XML_QUEUE]
         - Grava o NSU em banco:
           - Procedure [dbo].[usp_NFeIntegra_Salvar_Ultimo_NSU]: grava o NSU na tabela [dbo].[ULTIMO_NSU_RECEBIMENTO] de acordo com a faixa
   - Armazena estatísticas do request enviado, procedure [dbo].[usp_NFeIntegra_Criar_EstatisticaRecebimento]

### 2) Thread "thrRecebimentoPumper" -> ControlExecutePumper()
 
Loop:

 - N Queues são criadas se não existirem, onde N é o número de tipo de documentos (emissão, cancelamento, inutilização, evento)
 - Dicionário de Controle é criado se não existir.
 - Proc [dbo].[usp_NFeOutUFs_Obter_LOG_XML_Queue]:
   - Pega os N primeiros registros de [NFe_Out_UFs].[dbo].[LOG_XML_QUEUE] e os respectivos registros de retorno do AN em [NFe_Out_UFs].[dbo].[LOG_XML] (usa aquele esquema de ler dados antigos tb, depois de um timeout)
   - Atualiza LOXM_FL_READ = 1 para o item da fila lido
   - Cada um dos registros tem seus XML extraídos e cada um dos XMLs é armazenado em uma Queue de acordo com o tipo. Nesse ponto pode ser XML tanto do Recebimento quanto do NSUFaltantes.
   - Erros de tipo de documento não reconhecido ou cStat != 124 são logados no event-viewer e guarda uma estrutura com o erro
   - Adiciona o número do lote no Dicionário de Controle
   - Depois de todas as filas esvaziarem, chama proc [NFe_Out_UFs].[dbo].[usp_NFeOutUFs_Grava_Resultado_Execucao_TVP] para cada lote:
     - apaga registros de [NFe_Out_UFs].[dbo].[LOG_XML_QUEUE]:
     - registra os erros de NSUs na tabela [NFe_Out_UFs].[dbo].[LOG_XML], atualizando campo LOXM_TX_ERRO e timestamp.
     - se não houver erro, apaga registro da tabela [NFe_Out_UFs].[dbo].[LOG_XML]

### 3) Thread "thrRecebimentoConsumer(N)" -> ControlExecuteConsumer()
 
Atualmente, há 12 Threads desse tipo em produção.
Loop de cada thread, que pega documento mais prioritário (emissão, depois cancelamento, inutilização e evento) e faz parse (UnMarshall):
 
Caso SVC (Sefaz Virtual de Contingência)- ex "SCAN"
 - _Eventos genéricos_ (exceto Cancelamento e EPEC), chama proc [NFe_Out].[NFeEventos].[usp_NFeIntegra_EventoInsert], que armazena em:
   - NFe_Out.NFeOut.Protocolo
   - NFe_Out.NFeOut.DadosTransmissao_AN
   - NFe_Out.NFeEventos.Evento_Constraint
   - NFe_Out.NFeEventos.Evento
   - NFe_Out.NFeIntegra.IntegraNSU  (fkIntegraTipo=4)
   - NFe_Out.NFeIntegra.IntegraEvento
   - DFe_XML.NFe.IntegraEventoXml
   - DFe_XML.NFe.EventoXML
 - _Eventos de Cancelamento_, chama proc [NFe_Out].[NFeEventos].[usp_NFeIntegra_EventoCancelamentoContingenciaInsert], que
   - grava registros em todas as tabelas de eventos genéricos
   - grava registro em [NFe_Out].[NFeEventos].[Cancelamento]
 - _Eventos de EPEC_, chama proc [NFe_Out].[NFeEventos].[usp_NFeIntegra_EventoEPECInsert], que
   - grava registros em todas as tabelas de eventos genéricos, em todas as tabelas de eventos genéricos
   - grava registro em [NFe_Out].[NFeEventos].[EventoEPEC]
 - _Autorização_, chama proc [NFe_Out].[NFeOut].[usp_NFeIntegra_InsertContingencia], que grava em:
   - NFe_Out.NFeOut.Protocolo
   - NFe_Out.NFeOut.NFe
   - NFe_Out.NFeOut.DadosTransmissao_AN (condicionado a haver dados de transmissão)
   - NFe_Out.NFeOut.NFe_Constraint
   - NFe_Out.NFeOut.InfoContingencia
   - NFe_Out.NFeEventos.DadosApoio
   - NFe_Out.NFeIntegra.IntegraNSU (fkIntegraTipo=3)
   - DFe_XML.NFe.NFeXML
   - DFe_XML.NFe.NFeProtRelacionamento
 - _Cancelamento_, chama proc [NFe_Out].[NFeOut].[usp_NFeIntegra_CancNFeInsertContingencia], que grava em:
   - NFe_Out.NFeOut.Protocolo
   - NFe_Out.NFeOut.NFe_Constraint
   - NFe_Out.NFeOut.CancNFe
   - NFe_Out.NFeOut.DadosTransmissao_AN (condicionado a haver dados de transmissão)
   - NFe_Out.NFeEventos.Evento_Cosntraint
   - NFe_Out.NFeOut.InfoContingencia
   - NFe_Out.NFeIntegra.IntegraNSU  (fkIntegraTipo=3)
   - DFe_XML.NFe.CancNFeXML
 - _Inutilização_, chama proc [NFe_Out].[NFeOut].[usp_NFeIntegra_InutNFeInsertContingencia]
   - NFe_Out.NFeOut.Protocolo
   - NFe_Out.NFeOut.DadosTransmissao_AN (condicionado a haver dados de transmissão)
   - NFe_Out.NFeOut.NFe_Constraint (insere N registros, onde N é o número de notas inutilizadas)
   - NFe_Out.NFeOut.InutNFe
   - NFe_Out.NFeOut.InfoContingencia
   - NFe_Out.NFeIntegra.IntegraNSU  (fkIntegraTipo=3)
   - DFe_XML.NFe.InutNFeXML

Caso OUT_UFs
 - _Eventos genéricos_, chama proc [NFe_Out_UFs].[NFeEventos].[usp_EventoInsert_OutUFs]
   - NFe_Out_UFs.dbo.IntegraNSU
   - NFe_Out_UFs.dbo.DadosTransmissao_AN (condicionado a haver dados de transmissão)
   - NFe_Out_UFs.NFeEventos.Evento
   - NFe_Out_UFs.NFeUFs.DFeRecebimento
   - NFe_Out_UFs_XML.NFeEventos.IntegraEvento
 - _Evento de Carta de Correção_, chama proc [NFe_Out_UFs].[NFeEventos].[usp_CCeInsert_OutUFs], que
   - grava registros em todas as tabelas de eventos genéticos
   - grava registro em NFe_Out_UFs.NFeEventos.CCeNFe
 - _Evento de Cancelamento_, chama proc [NFe_Out_UFs].[NFeEventos].[usp_CancelamentoInsert_OutUFs], que
   - grava registros em todas as tabela de eventos genéricos
   - grava registro em NFe_Out_UFs.NFeEventos.Cancelamento
 - _Autorização_, chama proc [NFe_Out_UFs].[dbo].[usp_NFeOutUFs_Emissao_Create], que grava em:
   - NFe_Out_UFs.dbo.IntegraNSU
   - NFe_Out_UFs.dbo.DadosTransmissao_AN (condicionado a haver dados de transmissão)
   - NFe_Out_UFs.dbo.NFe
   - NFe_Out_UFs.NFeUFs.DFeRecebimento
   - NFe_Out_UFs.dbo.ProtocoloNFe ou NFe_Out_UFs.dbo.ProtocoloNFeLog
   - NFe_Out_UFs.NFeUFs.DFeInfoCompl
   - NFe_Out_UFs_XML.dbo.DFe_XML
 - _Cancelamento_, chama proc [NFe_Out_UFs].[dbo].[usp_NFeOutUFs_Cancelamento_Create], que grava em:
   - NFe_Out_UFs.dbo.IntegraNSU
   - NFe_Out_UFs.dbo.DadosTransmissao_AN (condicionado a haver dados de transmissão)
   - NFe_Out_UFs.dbo.CancNFe
   - NFe_Out_UFs.NFeUFs.DFeRecebimento
   - NFe_Out_UFs.dbo.ProtocoloNFe ou NFe_Out_UFs.dbo.ProtocoloNFeLog
   - NFe_Out_UFs.NFeUFs.DFeInfoCompl
   - NFe_Out_UFs_XML.dbo.DFe_XML
   
No caso do Cancelamento "antigo" (não por evento), que não é mais utilizado, ao final temos que:
 - Na tabela [NFe_Out_UFs].[dbo].[ProtocoloNFe] fica armazenado o nsu do cancelamento, com status 101
 - Na tabela [NFe_Out_UFs].[dbo].[ProtocoloNFeLog] fica armazenado o nsu da nota, com status 100.

### 4) Thread "thrCfgMonitor" -> ControlCfgMonitor()
Reinicia o serviço caso a flag em banco FORCE_RESTART_FLAG for setada para 'true'