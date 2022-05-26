# Serviço RecepcaoEvento
 
## Verificações

 * **Validação de Lote de Eventos**

   * Bloco A (Validação do Certificado Digital do Transmissor)
     * (A01 a A05) - são realizadas pelo protocolo SSL e não precisam ser implementadas
     * (A06) - ICP-Brasil
     * (A07) - Extensão CNPJ no certificado
     * Uso Indevido  - Evitar que um uso indevido do serviço se alastre até os blocos de validação mais pesados

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; A validação de Uso Indevido lê via StoredProc **NFCeOut.usp_ConfigSistemaSelect** o parametro = "`STATUS_CACHE_USO_INDEVIDO`" na tabela `[NFCe_Out_Constraint].[NFCeOut].[ConfigSistema]` para saber se a verificação deve ser efetuada.
Se sim, recupera o valor de `parametro = "MAX_REQUISICOES"` e faz o controle das requisições.
Assim que se estoura o valor limite de requisições é registrado o Uso Indevido **_(ainda não portada)_**

   * Bloco B (Validação Inicial da Mensagem no Web Service)
     * (B01) - Tamanho do XML de Dados (deve ser <= 500 Kbytes)
     * (B02) - Verifica se o Servidor de Processamento está Paralisado Momentaneamente
     * (B03) - Verifica se o Servidor de Processamento está Paralisado sem Previsão

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; - As validações B02 e B03 lêem via StoredProc **NFCeOut.usp_ConfigSistemaSelect** o status de operação do sistema (parametro `ONLINE`) presente na tabela `[NFCe_Out_Constraint].[NFCeOut].[ConfigSistema]`

   * Bloco C (Validação das Informações de Controle da Chamada ao Web Service)
     * (C01) - Elemento nfeCabecMsg inexistente no SOAP Header
     * (C02) - Campo cUF inexistente no elemento nfeCabecMsg do SOAP Header
     * (C03) - Verificar se a UF informada no campo cUF é atendida pelo Web Service (UF = 35?)
     * (C04) - Campo versaoDados inexistente no elemento nfeCabecMsg do SOAP Header
     * (C05) - Versão dos Dados informada é superior à versão vigente (versão 1.0 para Evento)
     * (C06) - Versão dos Dados não suportada

   * Bloco D1 (Validação de Forma da Área de Dados)
     * (D01a) - Em caso de falha de Schema, verificar se existe a tag raiz esperada para o lote
     * (D01b) - Em caso de falha de Schema, verificar se existe o atributo versão para a tag raiz da mensagem
     * (D01c) - Em caso de falha de Schema, verificar se o conteúdo do atributo versão difere do conteúdo da versaoDados informado no SOAPHeader
     * (D01d) - Verifica a existência de qualquer namespace diverso do namespace padrão da NFe
     * (D01e) - Verifica a existência de caracteres de edição no início ou no fim da mensagem ou entre as tags
     * (D02) - Verifica o uso de prefixo no namespace
     * (D03) - XML utiliza codificação diferente de UTF-8

 * **Validação de um Único Evento**

   * Bloco D2 (Validação dos Eventos do Lote e do Schema XML do Evento) - validação contra XML Schema "e110111_v1.00.xsd"
     * (D04) - Verifica se o tpEvento é valido
     * (D05) - Verifica se o verEvento é válido
     * (D06) - Verifica se o detEvento atende ao respectivo schema XML

   * Bloco E (Validação do Certificado Digital de Assinatura)
     * (E01) - Certificado Inválido se:
       * Inexistente (teste já realizado na validação de Schema)
       * Versão diferente de 3
       * Basic Constraint se informado,  não pode ser de AC 
       * Key Usage não define "Assinatura Digital" e "Não Recusa" 
     * (E02) - Validade do certificado (Data Inicio e Data Fim)
     * (E03) - falta a extensão do CNPJ no certificado (OtherName - OID = 2.16.76.1.3.3)
     * (E04) - Verificação da cadeia de Certificação (AC emissora não cadastrada na Sefaz / AC revogado / Não assinado pela AC emissora do certificado)
     * (E05) - LCR do certificado da assinatura (falta o endereço da LCR / erro de acesso ou LCR inexistente) (não implementado)
     * (E06) - Certificado de Assinatura revogado
     * (E07) - Certificado Raiz difere da ICP-Brasil (verificado pelo Thumbprint)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; - A validação (E02) utiliza a StoredProc **NFCeOut.usp_DateTimeUTCSelect** (NFCeOut_DB) para recuperar o tempo do sistema a fim de comparar com a data início e data fim do certificado.

   * Bloco F (Validação da Assinatura Digital)
     * (F01) - Assinatura difere do padrão do Sistema **_(não implementado)_**
       * Não assinado o atributo "ID" (falta "Reference URI" na assinatura) (*validado também pelo Schema)
       * Faltam os "Transform Algorithm" previstos na assinatura ("C14N" e "Enveloped"

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; * Estas validações são implementadas pelo Schema XML da Signature

   * * (F02) - Valor da assinatura (SignatureValue) difere do valor calculado
     * (F03) - CNPJ-Base do Emitente difere do CNPJ-Base do Certificado Digital

   * Bloco G (Validação de Regras de Negócio do Registro de Eventos - Parte Geral)
     * (G01) - Tipo de Ambiente difere do ambiente do Web Service
     * (G02) - UF divergente
     * (G03) - CNPJ do autor do evento inválido
     * (G04) - CPF do autor do evento inválido
     * (G04f) - Chave de acesso inválida (diferente de 65) - não implementada pois é = (G05f)
     * (G05a) - Chave de acesso com dígito verificador inválido
     * (G05b) - Chave de acesso inválida (UF inválida)
     * (G05c) - Chave de acesso inválida (Ano < 05 ou Ano > Ano Corrente)
     * (G05d) - Chave de acesso inválida (Mês =0 ou Mês>12)
     * (G05e) - Chave de acesso inválida (CNPJ zerado ou dig.verificador inválido)
     * (G05f) - Chave de acesso inválida (diferente de 65)
     * (G05g) - Chave de acesso inválida (número NF = 0)
     * (G05) - Validação do atributo Id (concat. de ID + tpEvento + chNFe +nSeqEvento)
     * (G06) - Chave de acesso inexistente para o tpEvento que exige a existência da NF-e
     * (G07) - Verifica duplicidade de evento (tpEvento + chNFe + nSeqEvento)
     * (G08) - Se evento do emissor, CNPJ do Autor diferente do CNPJ da Chave de Acesso da NF-e
     * (G09) - Se evento do destinatário, CNPJ do Autor diferente do CNPJ base do destinatário da NF-e - **_não implementada. No código só eventos de cancelamento e carta de correção (evento de emissor) estão na lista tpEventos em_** `Br.Gov.Sp.Fazenda.NFCe.Lib.Conf.Configuration`. **_Os demais são rejeitados na validação D04._**
     * (G10) - **_não implementada_**
     * (G11) - Data do evento não pode ser menor que a data de emissão da NF-e, se existir
     * (G12) - Data do evento não pode ser maior que a data de processamento (aceitar uma tolerância de até 5 minutos) (NT 2012.003)
     * (G13) - Data do evento não pode ser menor que a data de autorização para NF-e não emitida em contingência se a NF-e existir

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; - A StoredProc **NFCeOut.usp_ProtocoloSelectPorIdNFe2** (NFCeOut_DB) é utilizada para recuperar informações da NFC-e (ChNFCe) utilizadas nas validações G06, G08, G11
via function `NFCeOut.ufn_ConsultaProtocolo` que lê
[( `[NFCe_Out_Constraint_DB].[NFCeOut].[NFCe_Constraint]` inner join `[NFCeOut].[Protocolo]` ) left join `[NFCeOut].[NFCe]` ] left join `[NFCeOut].[DadosApoio]`

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; - A StoredProc **NFCeOut.usp_EventoSelect** (NFCeOut_DB) é utilizada para verificar se há duplicidade de evento (tpEvento + chNFE + nSeqEvento) - validação (G07)

   * Bloco GA (Validação de Regras de Negócio do Registro de Eventos - Parte Específica Cancelamento)
     * (GA01) - Campo serie – na autorização pela SEFAZ Autorizadora: não aceitar série diferente de 0-899
     * (GA02) - Campo serie – na autorização pelo SCAN: não aceitar série diferente de 900-999 - **_Não Implementado - Referente ao Portal Nacional_**
     * (GA03) - Acesso Cadastro Contribuinte: Verificar Emitente não autorizado a emitir NF-e
     * (GA04) - Verificar Situação Fiscal irregular do Emitente
     * (GA05) - Verificar se a NF-e está autorizada (não pode estar cancelada nem denegada) **_o status = 150 (autorização fora do prazo) atualmente não está na NFC-e_**
     * (GA06) - Verificar se Pedido de cancelamento é tempestivo (7 dias da autorização) - **_Conforme e-mail do Alfonso do dia 23/04, foi pedido para aumentar o prazo para 31 dias_**
     * (GA06a) - Verificar se NFC-e está autorizada a mais de 30 minutos.
     * (GA07) - Verificar o sequencial do evento (HP15 - nSeqEvento) é um valor válido (=1)
     * (GA08) - Verificar se o número Protocolo informado difere do nro. Protocolo da NF-e
     * (GA09) - Verificar recebimento da NF-e pelo Destinatário (implementação futura)
     * (GA10) - **_não aplicável para NFC-e_**
     * (GA11) - **_Validação cancelada a pedido do Clovis em e-mail do dia 26/01/2012, com o título "Funcionalidades"_**
     * (GA12) - não aplicável para NFC-e

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; - A StoredProc **NFCeOut.usp_ContribuintesHabilitadosSelectPorCnpj** (NFCeOut_Constraint_DB) é utilizada para recuperar informações da tabela `[NFCe_Out_Constraint].[NFCeOut].[ContribuintesHabilitados]` e realizar as validações:

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; GA03: Contribuinte nulo ou com o campo "ativo" = 0

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; GA04: Contribuinte com campo "suspensa" = 1

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; - Na validação GA04 caso a verificação não dê o contribuinte como suspenso ainda é realizada uma consulta na base do Cadesp (duas procedures relativas ao database do Cadesp podem ser acionadas: **USP_Consulta_Estabelecimento_NFCe** e **USP_Consulta_EstabelecimentoPorIE_NFCe**)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; - A StoredProc **NFCeOut.usp_ProtocoloSelectPorIdNFe2** é acionada para recuperar informações relativas a NFC-e utilizadas nas validações GA05, GA09
via function `NFCeOut.ufn_ConsultaProtocolo` que lê
[( `[NFCe_Out_Constraint_DB].[NFCeOut].[NFCe_Constraint]` inner join `[NFCeOut].[Protocolo]` ) left join `[NFCeOut].[NFCe]` ] left join `[NFCeOut].[DadosApoio]`

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; - A StoredProc **NFCeOut.usp_DateTimeUTCSelect** é acionada para ler a data do sistema a fim de fazer validações de tempestividade (GA06, GA06a)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; - A StoredProc **NFCeOut.usp_CancExtemporaneoPermitido** é acionada na validação GA06 para verificar na tabela `[NFCe_Out].[NFCeOut].[CancExtemporaneo]` se para determinada NFCe a flag timestampValidade > `GETDATE()`