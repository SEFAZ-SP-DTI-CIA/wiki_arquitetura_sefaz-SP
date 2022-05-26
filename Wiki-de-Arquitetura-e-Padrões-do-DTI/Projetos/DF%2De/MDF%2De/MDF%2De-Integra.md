O Manifesto Eletrônico de Documentos Fiscais é importado do site da Sefaz do Rio Grande do Sul, _https://mdfe.svrs.rs.gov.br/ws/mdfeconsultadfe/mdfeconsultadfe.asmx_, via um serviço Windows, MDF-e integra. Segue abaixo uma breve descrição do processo de integração, assim como um fluxo das operações:
## Fluxo do processo - Br.Gov.Sp.Fazenda.MDFeBusiness.Facade.MDFeRecebimentoController.Start()

1. Método **FillConfiguration()**,

   a. Seta informações de configuração sobre os parâmetros, provenientes tanto do arquivo de configuração quanto do banco de dados (tabelas [MDFe].[Configuracao] e [MDFe].[ConfiguracaoTipo]): MDFeRecebimento, MDFeRecebimentoExceptionPolicy, Conexão com BD, Ambiente (Desenvolvimento, Homologação e Produção),tempo de sleep de thread, número máximo de loop com erros, versão do schema, versão de aplicativo, habilita compressão, carrega certificado, quantidade de theads, endereço de consulta WS, Inicializar/Parar o serviço.
 
   b. Força o reset da flag que reinicia o serviço, através da procedure [MDFe].[usp_MDFeIntegra_Reset_Force_Restart_Flag] que atualiza a tabela [MDFe].[Configuracao].

2. **ControlSendRequest()**: Thread “TaskSendRequest“ método chama o WebService para recuperar um lote de MDF-e's e respectivos eventos, faz o parsing e armazena o lote em filas em banco de dados. O parsing do lote recebido é feilto pelo método ValidateXMLResponseEGravaFilaLogXMLQueue():
 
   a. Caso a tag cStat seja 118, significa retorno com documentos. Nesse caso, o lote é armazenado pela stored procedure [MDFeRecebimento].[usp_MDFe_LogXML_Create_And_UpdateUltNSU], que armazena nas filas [MDFeRecebimento].[LogXML] e [MDFeRecebimento].[LogXML_Queue], e também atualiza o último NSU recebido (que será usado na próxima iteração).

   b. Caso a tag cStat seja 117 significa que não possui documentos para serem processados. 

   c. Caso a tag cStat seja outro valor, uma exceção é lançada e será capturada em um nível superior para ser armazenado o erro em [MDFeRecebimento].[LogXML], juntamente com o request e a resposta enviados.

3. **ControlGravaFilaEmMemoria()**:  Thread “TaskGravaFilaEmMemoria“ Método responsável pelo processamento da fila armazenado em banco. Utiliza o paradigma Producer x Consumer.
 
   i. Produce(): chama a procedure [MDFeRecebimento].[usp_MDFe_Obter_LogXML_Queue] que retorna o próximo lote válido gravado nas tabelas [MDFeRecebimento].[LogXML] e [MDFeRecebimento].[LogXML_Queue]. Cada documento do lote é enfileirado em uma lista em memória.
 
   ii. Consume(): para cada documento em memória enfileirado pelo Produce, faz o 'unmarshall' e grava o documento no banco de dados:
 
      - Se for MDF-e, chama a procedure [MDFeIntegra].[usp_MDFeRecebimento_Insert], que insere o documento nas tabelas [MDFe].[Protocolo], [MDFe].[MDFe_Constraint], [MDFe].[MDFe], [MDFe_XML].[MDFe].[MDFe_Xml] , [MDFeIntegra].[IntegraNSU]
      - Se for evento de MDF-e, chama a procedure [MDFeIntegra].[usp_MDFeEventoRecebimento_Insert], que insere o documento nas tabelas [MDFe].[Protocolo], [MDFe].[Evento_Constraint], [MDFe].[Evento], [MDFe_XML].[MDFe].[EventoXml] e [MDFeIntegra].[IntegraNSU] 
 
   iii. GravaResultadoExecucao() – Grava log com o resultado da execução do lote, utilizando a procedure [MDFeRecebimento].[usp_MDFe_Grava_Resultado_Execucao_TVP] que remove o lote da fila e armazena log de erro, se houver, nas tabelas [MDFeRecebimento].[RegistrosLoteErro] e  [MDFeRecebimento].[LogXML].

4. **ControlCfgMonitor()** – Thread “TaskCfgMonitor” Monitora a configuração informações estão no Banco de Dados.

   a. Método control config reinicia flag forçando o serviço executado novamente MDFeRecebimentoController. ControlCfgMonitor()  MDFeRecebimento. ReadForceRestartFlag(). Utiliza a stored procedure [MDFe].[usp_MDFeIntegra_Obter_Force_Restart_Flag]  consulta na tabela MDFe.Configuracao.
 
   b. Restart() Após verificações sobre o restart o método faz uma chamada para o método Start() e reinicia o processo. 
 
### Principais classes

| **Namespace**                           | **Classe**                       |
|-----------------------------------------|----------------------------------|
|||
| Br.Gov.Sp.Fazenda.MDFeBusiness.Facade   | MDFeRecebimentoController|
| Br.Gov.Sp.Fazenda.MDFeBusiness          | MDFeRecebimento   |
| Br.Gov.Sp.Fazenda.MDFeBusiness          | MDFeRecebimentoProducerEConsumer|
| Br.Gov.Sp.Fazenda.MDFeBusiness.Data     | MDFeRecebimentoData|

### Principais tabelas e procedures

| **Banco de dados**   | **Tabela**            | **Procedure**   |
|----------------------|-----------------------|------------------|
||||
| MDFe_Out   | MDFeIntegra.IntegraNSU| [usp_MDFeEventoRecebimento_Insert] |
| MDFe_Out   | [Protocolo]| [usp_MDFeEventoRecebimento_Insert] |
| MDFe_Out   | [Evento_Constraint]| [usp_MDFeEventoRecebimento_Insert] |
| MDFe_Out   | [Evento]| [usp_MDFeEventoRecebimento_Insert] |
| MDFe_Out   | MDFeIntegra.IntegraNSU| [usp_MDFeEventoRecebimento_Insert] |
| MDFe_XML   | MDFe.EventoXml| [usp_MDFeEventoRecebimento_Insert] |
| MDFeIntegra| MDFeRecebimento.LogXML_Queue| [usp_MDFe_Grava_Resultado_Execucao_TVP] |
| MDFeIntegra   | MDFeRecebimento.LogXML| [usp_MDFe_Grava_Resultado_Execucao_TVP] |
| MDFeIntegra   | TVP_RegistrosLoteErro| [usp_MDFe_Grava_Resultado_Execucao_TVP] |
| [MDFeIntegra]| MDFe.Configuracao| [usp_MDFeIntegra_Obter_Force_Restart_Flag] |

### Servidores

O serviço está implantado no cluster SRVCS25 em produção.