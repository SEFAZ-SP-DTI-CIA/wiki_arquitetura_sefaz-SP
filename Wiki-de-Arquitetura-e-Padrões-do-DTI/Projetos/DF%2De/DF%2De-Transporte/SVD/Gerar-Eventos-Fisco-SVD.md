## Gerar Eventos Fisco SVD

### Implementação

Thread dentro do Windows Service CTe Envio Eventos 
Algoritmo Producer-Consumer
Producer: 

  * Desenfileira dados chamando a procedure [SVD].[USP_EVENTO_FISCO_DEQUEUE] para pegar dados da fila [SVD].[QUEUE_EVENTO_FISCO] e [SVD].[QUEUE_DATA_EVENTO_FISCO], gerando uma fila de objetos do tipo CTeGerarEventoFiscoDTO.
  * Obs: a procedure apaga o registro da tabela [SVD].[QUEUE_EVENTO_FISCO]

Consumer: 

  * Para cada registro a ser consumido, verifica primeiro se nSeq <= 99. Se não for, finaliza processo atualizando status na tabela [SVD].[QUEUE_DATA_EVENTO_FISCO] (coloca QDEF_STATUS = 999 e QDEF_XML_RESPONSE com '<erroNSeq>Erro</erroNSeq>', com a coluna QDEF_NSEQ indicando o valor do nSeq)
  * Caso contrário, monta o XML do evento, assina e envia para o web-service da UF o qual o deverá autorizar. Dependendo da resposta, uma atitude é tomada:
    * sucesso (cStatus = 134, 135, 136): finaliza processo (*) 
    * eventos não contornáveis: finaliza o processo (*)
    * duplicado (cStatus = 631): 
      * Se não veio informação de “nProt” ou do "CNPJ" no detalhe da resposta, reenfileira registro
      * Se o CNPJ do detalhe da resposta for o da Sefaz autorizadora, finaliza processo (*)
      * Caso contrário, incrementa o número do evento (NSeq) e reenfileira registro
    * outros (não houve retorno, ou deu exceção, ou veio outro retorno de erro) reenfileira registro.

Sendo que:

  * finalizar processo significa atualizar status, XML resposta, protocolo e Data de Envio de resposta e tempo do Web Service na tabela [SVD].[QUEUE_DATA_EVENTO_FISCO]
  * reenfileirar significa adicionar novamente o registro na tabela [SVD].[QUEUE_EVENTO_FISCO] com data futura para processamento (quanto maior o número de tentativas falhas, mais longe estará esse tempo). Gera log em [SVD].[QUEUE_LOG_EVENTO_FISCO]
  * eventos não contornáveis são aqueles que não adianta retentar pois o fisco destino irá muito possivelmente retornar o mesmo erro. São identificados por cstat = 217, 216, 600, 236, 732 e 721 (alguns deles não estão ainda em produção). A área de negócios foi avisada e irá decidir o que fazer com esses registros. Por exemplo, caso cstat = 600 significa que a chave natural está correta mas a chave de 44 dígitos está errada, não temos como saber qual a chave certa.
  * a UF onde será autorizada pode ser SP mesmo (no caso de eventos de AP, PE, RR e SP), RS (no caso de eventos de AC, AL, AM, BA, CE, DF, ES, GO, MA, PA, PB, PI, RJ, RN, RO, RS, SC, TO) ou então na UF do evento (no caso de eventos de PR, MT, MS e MG).


### Pontos a destacar
O BT 2019.001 v1.50 "Eventos Gerados pelo Fisco" do CT-e é que define os requisitos desse sistema.

Os eventos que são tratados por esse mecanismo "Gerar Eventos Fisco" do SVD são apenas esses:

  * AutorizadoSubcontratacao     : 440150
  * AutorizadoRedespacho         : 440130
  * AutorizadoRedespachoIntermed : 440140
  * AutorizadoVinculadoMultimodal: 440160
  * GTVeCTe_OS_Autorizado        : 240180
  * GTVeCTe_OS_Cancelado         : 240181

Outros tipos de eventos são tratados de outras formas:

  * Alguns são autorizados apenas pelo SVC-RS (por exemplo eventos de MDF-e). 
  * Outros ainda são autorizados gerando-se o XML, assinando e inserindo diretamente na base aqui de SP, pois são eventos destinados a SP ou àquelas UFs conveniadas com SP. Essa parte é implementada por threads dentro do serviço de autorização do CT-e.

Quando SP está fora do ar e a SVD-RS assume as autorizações por SP, a SVD-RS é responsável por gerar e autorizar esses eventos dos CT-e's e respectivos eventos autorizados em contingência. 
Simetricamente, quando RS está fora, SP é que gera e autoriza esses eventos para todos os documentos que estiver autorizando em contingência; o interessante nesse caso é que os eventos vão com o CNPJ do certificado da Sefaz-SP.

A coluna de status (QDEF_STATUS) da tabela [SVD].[QUEUE_DATA_EVENTO_FISCO] armazena ou o código de retorno da autorização (tag<cstat>), que tem 3 dígitos, ou então códigos de erro interno do enum DFeStatusQueue, com 4 dígitos.

O enfileiramento na fila [SVD].[QUEUE_EVENTO_FISCO] é feito na autorização do evento em SP, que chama a procedure [SVD].[USP_EVENTO_FISCO_ENQUEUE] (além da inserção normal do documento nas outras tabelas por conta da autorização). E é nesse ponto que as UFs para as quais esse evento será compartilhado são definidas pelo método SvdUtils.FillUFsCompartilhar(). 

A tabela de LOG tem uma coluna [SVD].[QUEUE_LOG_EVENTO_FISCO] cuja coluna representa o cstat retornado pela autorizadora, ou se for >= 1000 é um código de erro interno. Os principais valores retornados tem sido:

  * 109 = Serviço Paralisado sem previsão
  * 216 = Chave de Acesso difere da cadastrada
  * 236 = Chave de Acesso com dígito verificador inválido.
  * 600 = Chave de Acesso difere da existente em BD
  * 631 = Duplicidade de evento
  * 633 = O autor do evento não é um órgão autorizado a gerar o evento.
  * 634 = A data do evento não pode ser menor que a data de emissão do CT-e
  * 678 = Consumo Indevido
  * 732 = Chave de acesso inválida(modelo diferente de 57)
  * 999 = Erro não catalogado
  * 1000 = Erro na serialização de entidade
  * 1001 = Resposta nula do web-service
  * 1002 = Exceção ocorrida 
  * 1003 = Erros mútilplos

### Principais tabelas envolvidas

  * [SVD].[QUEUE_EVENTO_FISCO] - controle de fila dos eventos a serem autorizados
  * [SVD].[QUEUE_DATA_EVENTO_FISCO] - contém os dados a serem usados para construção do XML do evento a ser autorizado, e também o resultado do envio, incluído o XML de resposta.
  * [SVD].[QUEUE_LOG_EVENTO_FISCO] - contém log de reenfileiramento

[Script para acompanhamento (clique para abrir)](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/249/Script-Gerar-Evento-Fisco)

### Classes C# que implementam a lógica principal

  * CTeGerarEventoFiscoBLL
  * DFeBaseQueueBLL<CTeGerarEventoFiscoDTO>