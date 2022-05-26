## Serviço Autorização
 
### Verificações:

 * BlocoA  (Validação do Certificado Digital do Transmissor (protocolo SSL))
   * ICP-Brasil
   * Extensão de CNPJ
   * Uso indevido
     * StoredProc NFCeOut.**usp_ConfigSistemaSelect** lê `STATUS_CACHE_USO_INDEVIDO` na tabela `[NFCe_Out_Constraint].[NFCeOut].[ConfigSistema]`
     * Se habilitada a Verificação de Uso Indevido
       * StoredProc NFCeOut.**usp_ConfigSistemaSelect** lê `MAX_REQUISICOES` na tabela `[NFCe_Out_Constraint].[NFCeOut].[ConfigSistema]`
     * Se Contador == MAX_REQUISICOES+1
       * StoredProc `NFeOut.usp_LogUsoIndevidoInsert (não portado)`

 * BlocoB (Validação Inicial da Mensagem no Web Service)
   * Tamanho do XML de dados (<= 500KB) de acordo com TipoMensagem, mas todos estão iguais (exceto Eventos)
   * Verifica status do servidor de processamento
StoredProc NFCeOut.**usp_ConfigSistemaSelect** lê `ONLINE` na tabela `[NFCe_Out_Constraint].[NFCeOut].[ConfigSistema]`

 * Bloco GAP (Validação do parâmetro indSync)
   * Se indSinc == 1 (chamada síncrona), não pode processar lotes com mais de uma NF
### Ações:

 * StoredProc NFCeIn.**usp_LoteInsert**
   * lê tabela `[NFCeIn].[FilaProcessamento]` e retorna divisão por zero se COUNT > 30000
   * insere em tabelas `[NFCeIn].[Lote], [NFCeIn].[LoteStaging], [NFCeIn].[FilaProcessamento]`
   * se ipOrigem não nulo, insere em `[NFCe_In_XML_DB].[NFCeIn].[LoteCompl]`
 * Se lote pKey retornado é diferente de default(long)
   * `StoredProc NFCeOut.usp_DateTimeUTCSelect` pega `SYSDATETIMEOFFSET()` do banco
 * Se lote pKey retornado é igual a default(long)
   * StoredProc NFCeIn.**usp_TempoMedioProcessamentoSelect** lê `[NFCeIn].[TempoMedioProcessamento]`

### Retorno:

 * Número de recibo

## &nbsp; * Somente Síncrona:

### Em lote:

 * BlocoC (Validação das informações de controle da chamada ao Web Service)
   * cabeçalho SOAP (`nfeCabecMsg`): UF == 35? versão dos dados <= **3.10m** ? (esse teste é diferente para Consulta e diferente para Evento)

 * BlocoD (Validação da Mensagem do Pedido)
   * Validação contra XML Schema `"enviNFe_v3.10.xsd"` (é diferente para cada Serviço)
   * Verifica Namespace (é diferente em PRODUCAO), Prefixo no Namespace, Espaços em branco, Encoding UTF-8

 * BlocoG_Lote (Validações do parâmetro indSinc)
   * **Para cada NF:** Verifica se todas as NF tem o mesmo modelo

### Para Cada NF:

BlocoE (Validação do Certificado Digital utilizado na Assinatura Digital)
 * Verifica se certificado existe, se versão != 3, keyUsage define "assinatura digital" e "não recusa", certificado NÃO é de AC,
 * Loga erros; loga o Id da primeira infNFe _**(é diferente para LoteZip e Inutilização e não trata outros tipos)**_; StoredProc NFCeOut.**usp_ValidacaoCertificadoLogInsert** insere em `[NFCeOut].[ValidacaoCertificadoLog]`
 * Verifica prazo de validade do certificado; StoredProc NFCeOut.**usp_DateTimeUTCSelect** pega `SYSDATETIMEOFFSET()` do banco
 * Verifica CNPJ, cadeia de certificação, se certificado revogado, certificado raiz

 * BlocoF (Validação da Assinatura Digital)
   * Verifica se existe exatamente 1 (uma) assinatura digital; se o CNPJ do certificado é igual ao da NF

 * BlocoG (Validação de regras de negócios da NF-e)
   * GA: Dados na NF
     * Chave da NF começa com "NFe", tem 47 caracteres e segue a regra de formação de acordo com os valores (UF, CNPJ, data, modelo, série, etc) passados na NFF; Guarda CNPJ base.
   * GB: Identificação da NF
     * **Obs:** tem algumas verificações removidas nesse sub-bloco
     * Emitente é de SP (código 35), modelo == 65, série fora da faixa 0-899, contém data de entrada ou saída, tipo de operação é entrada, tipo de operação é != de interna, código do município é válido, início do código do município difere do código da UF, código da impressão difere de 4-NFCe ou 5-NFCe_EM_MSG_ELETRONICA, tipo de emissão é CONTINGÊNCIA(_OFFLINE|_SCAN|_SVC_AN|_SVC_RS|_DPEC), tipo de emissão é CONTINGÊNCIA(_FS|_DPEC|_FS_DA|_OFFLINE_NFCE) e campos dhCont e xJust não foram preenchidos, chave da NF tem 44 caracteres (tirando "NFe") e dígito verificador está correto, tpAmb é diferente do ambiente do serviço (PRODUCAO|HOMOLOGACAO), finNFe != NORMAL,  indFinal == 0-NAO (não consumidor final), indPres != PRESENCIAL|ENTREGA_DOMICILIO, procEmi != APLICATIVO_CONTRIBUINTE|APLICATIVO_FISCO_PELO_CONTRIBUINTE, tipo de emissão é NORMAL mas campos dhCont ou xJust foram preenchidos, NFref != null

     * Requisição tem `indPres == ENTREGA_DOMICILIO` quando não habilitada; StoredProc NFCeOut.**usp_ConfigSistemaSelect**  lê `ENTREGA_EM_DOMICILIO_HABILITADA` na tabela `[NFCe_Out_Constraint].[NFCeOut].[ConfigSistema]`
     * Requisição tem emissão tipo 1-normal (ou contingência 3, 6 ou 7) e feita há menos de 5 minutos (StoredProc NFCeOut.**usp_DateTimeUTCSelect** pega `SYSDATETIMEOFFSET()` do banco)
     * Se emissão em contingência (dhCont != default), dhCont não deve ser maior que data do sistema (StoredProc NFCeOut.`usp_DateTimeUTCSelect` pega `SYSDATETIMEOFFSET()` do banco), nem anterior a 30 dias antes da dhEmi

   * GC: Identificação do Emitente
     * CNPJ base do emitente vazio, != 14dígitos, igual a 00000000000000 ou com dígito verificador incorreto, diferente do CNPJ base da primeira NF
     * NF com CPF e ProcEmi != AVULSA(_PELO_CONTRIBUINTE|_PELO_FISCO), CPF nulo/vazio, na lista de exceções (00000000000, 11111111111, 222.., etc), != 11 dígitos ou com dígito verificador incorreto
     * cMun == 0000000, na lista de exceções ou com dígito verificador inválido, iniciado com código diferente do código da UF, UF != 35
     * IE nula/vazia, não tem 1 a 12 dígitos, == 000000000000, dígito verificador incorreto, IEST != nulo (IE do Substituto Tributário deveria ser nula)

   * GD: Identificação do fisco emitente (NF avulsa)
     * Contribuinte emitiu no grupo avulsa, que é exlcusivo do fisco (avulsa != nulo e ProcEmi != AVULSA_PELO_FISCO)

   * GE: Identificação do destinatário
     * indPres == ENTREGA_DOMICILIO e destinatário (dest) == nulo, CNPJ do destinatário informado mas inválido, CPF do destinatário informado mas inválido
     * Se ambiente != PRODUCAO, xNome deve ser `"NF-E EMITIDA EM AMBIENTE DE HOMOLOGACAO - SEM VALOR FISCAL"`
     * indPres == ENTREGA_DOMICILIO , mas dest/dest.enderDest == null
     * codMun == 0000000, na lista de exceções, ou com dígito verificador inválido (exceto se for operação no exterior "EX")
     * idDest == OPERACAO_INTERNA e indFinal == 0-NAO (_**esse teste nunca é exercitado, pois já falha em GB25a e tem um comentário de TO DO no código, avisando para verificar**_) e enderEmit.UF != enderDest.UF
     * idDest != OPERACAO_COM_EXTERIOR e cPais != 1058 (Brasil)
NF tem IE ou Inscrição na Suframa do destinatário (dest.IE != nulo, dest.ISUF != nulo)

   * GF: Local da retirada
     * **Obs:** cada teste desse sub-bloco só roda se infNFe.retirada != nulo
     * caso não-nulo, testa validade de retirada.CNPJ e caso não-nulo, testa validade de retirada.CPF
     * caso retirada.UF == EX, testa se cMun != 9999999 (cMun para retirada no exterior); caso retirada.UF != EX, validar retirada.cMun e verificar se 2 primeiros dígitos de retirada.cMun != código de retirada.UF

   * GG: Local da entrega
     * **Obs:** cada teste desse sub-bloco só roda se infNFe.entrega != nulo
     * caso não-nulo, testa validade de entrega.CNPJ, idem para entrega.CPF
     * caso entrega.UF == EX, testa se cMun != 9999999 (cMun para entrega no exterior); caso retirada.UF != EX, validar retirada.cMun e verificar se 2 primeiros dígitos de retirada.cMun != código de retirada.UF

   * GI: Produtos e serviços
     * **Obs:** há diversos testes para implementação futura nesse sub-bloco
     * cEAN == 0 ou dígito de controle inválido
     * produto.NCM == (77|98|99) (Capítulo inválido da Nomeclatura Comum Mercosul)
     * tpNF==0 (operação de entrada) e produto.CFOP inicia com (5|6|7) (códigos fiscais de operação e prestação de saída)
     * idDest != OPERACAO_INTERNA e CFOP inicia com 1 ou 5 (**_código não exercitado na NFC-e, pois idDest sempre será de operação interna, comentário no código já indica isso_**)
     * CFOP começa com 1 e UF emitente != UF destinatário e IE destinatário != vazio e modFrete != 9(sem frete) e prod.comb.UFcons == UF emitente; mesma coisa para CFOP começando com 5, mas sem o teste do modFrete.
     * CFOP começa com 7 e tag "exporta" vazio (sem local de embarque)
     * CFOP != (5101|5102|5115|5401|5403|5405|5656|5933)
     * Se vProd (valor total) = vUnCom (valor unitário) * qCom (quantidade) com tolerância de 0,01 (um centavo)
     * produto.cEAN não conversível em long, igual a 0 (zero) ou tem dígito verificador inválido
     * produto.indTot == 0

   * GJ: Item / Veículos novos
     * Algum infNFe.prod.veicProd != nulo

   * GK: Item /Medicamentos
     * Algum infNFe.prod.med != nulo

   * GL: Item / Armamentos
     * Algum infNFe.prod.arma != nulo

   * GL1: Item / Combustível (apesar do cabeçalho não indicar, inclui papel imune)
     * Algum infNFe.prod.comb != nulo
     * Algum infNFe.prod.nRECOPI != nulo (papel imune)

   * GN: Item / tributo IMCS
     * **_tem um if(result.Valido) que parece ser sempre verdadeiro_**
     * CRT == 1 e imposto.ICMS.CST não vazio (Código de Regime Tributário / Código da Situação Tributária)
     * CST == (50|51) (suspensão|diferimento)
     * det.imposto.ICMS.ICMSPart != nulo (partilha entre UF)
     * CRT != 1 e imposto.ICMS.CSOSN não vazio
     * Para CST == (00|10|20|51|70), se algum dos valores não foi informado ou se vIcms != vBc * pIcms (com tolerância de 1); exceto se CST==51 e nenhum dos 3 valores foi informado;
     * **_(continuar depois, não tem leitura de DB nesse sub-bloco)_**

   * GO: Item / tributo IPI
     * imposto.IPI != nulo (**_tem um teste nunca exercitado, GO07_**)

   * GP: Item / tributo II
     * imposto.II != nulo

   * GR: Item / tributo PIS ST
     * imposto.PISST != nulo

   * GT: Item / tributo COFINS ST
     * imposto.COFINSST != nulo

   * GU: Item / tributo ISSQN
     * Se tem ISSQN, mas não tem IM (inscrição municipal); se cMunGF (código município do FG) é inválido; se tem ISSQN e ICMS, mas `"EMISSAO_DE_NOTA_SOMENTE_COM_ISSQN_HABILITADA"` é falso (StoredProc NFCeOut.**usp_ConfigSistemaSelect** lê na tabela `[NFCe_Out_Constraint].[NFCeOut].[ConfigSistema])`

   * GW: Total da NF
     * StoredProc NFCeOut.**usp_ConfigSistemaSelect** lê `VALOR_LIMITE_ICMS` na tabela `[NFCe_Out_Constraint].[NFCeOut].[ConfigSistema]`
     * StoredProc NFCeOut.**usp_ConfigSistemaSelect** lê `VALOR_LIMITE_ICMSST` na tabela `[NFCe_Out_Constraint].[NFCeOut].[ConfigSistema]`
     * StoredProc NFCeOut.**usp_ContribuintesHabilitadosSelectPorCnpj** lê `[NFCe_Out_Constraint].[NFCeOut].[ContribuintesHabilitados]`
     * StoredProc NFCeOut.**usp_ConfigSistemaSelect** lê `VALOR_LIMITE_NFCE` na tabela `[NFCe_Out_Constraint].[NFCeOut].[ConfigSistema]` 
     * StoredProc NFCeOut.**usp_ConfigSistemaSelect** lê `VALOR_LIMITE_NFCE_DESTINATARIO_NAO_IDENTIFICADO` na tabela `[NFCe_Out_Constraint].[NFCeOut].[ConfigSistema]` 
     * **_Esse sub-bloco é enorme, To Do: verificar demais regras se for necessário_**

   * GX: Transporte da NF
     * (transp.modfrete != 9 ou transp.transporta != nulo) e indPres != ENTREGA_DOMICILIO
     * transp.(retTransp | veicTransp | reboque | vagao | balsa ) != nulo

   * GY: Dados de cobranca
     * infNFe.cobr != nulo

   * GYA: Formas de pagamento
     * infNFe.pag == nulo

   * GZA: Comércio exterior
     * infNFe.exporta != nulo

   * GZB: Informação de compra
     * infNFe.compra != nulo

   * GZC: Informações do Registro de Aquisição de Cana
     * infNFe.cana != nulo

   * G1C: Banco de Dados: Emitente
     * IE emitente != IE contribuinte habilitado; contribuinte habilitado não ativo;
     * Contribuinte habilitado suspenso (**_cStat 301, * nesse caso, não invalida_**)
     * StoredProc NFCeOut.**usp_ConfigSistemaSelect** lê `VALIDACAO_CADESP_ATIVO` na tabela `[NFaCe_Out_Constraint].[NFCeOut].[ConfigSistema]`
     * Se `VALIDACAO_CADESP_ATIVO` == true, roda StoredProc **USP_Consulta_Estabelecimento_NFCe**
e **USP_Consulta_EstabelecimentoPorIE_NFCe** no `Banco de Dados do CADESP`
     * Contribuinte habilitado nulo ou inativo (**_cStat 301, * nesse caso, não invalida_**)
     * Contribuinte habilitado nulo (**_cStat 245, nesse caso invalida_**)
     * **Não implementado>** Se exception: StoredProc NFeOut.**usp_CadespLogConsultaInsert** não implementada; tabela `[NFeOut].[CadespLogConsulta]` não implementada

   * G2B: banco de Dados: Chave da NF
     * StoredProc NFCeOut.**usp_ProtocoloSelectPorIdNFe2** lê `[NFCe_Out_Constraint_DB].[NFCeOut].[NFCe_Constraint]` inner join `[NFCeOut].[Protocolo]` left join `[NFCeOut].[NFCe]` left join `[NFCeOut].[DadosApoio]`
     * NF já cadastrada e cStat != 102 e chave da NF no banco != chave da requisição
     * NF já cadastrada e cStat == (100 | 101 | 102 | 110 | 150 | 151 | 301 | 302)
(**_Verificação G2B08.5 não implementada por questão de performance_**)


### Ações:

Pre-validações:

 * StoredProc NFCeIn.**usp_FilaProcessamentoSelect** lê e deleta (@maxRows== tamanhoBuffer) linhas da tabela `[NFCeIn].[FilaProcessamento]` (limitado a 2 últimos dias) e atualiza `[NFCeIn].[LoteStaging]` ; acessa `[NFCeIn].[Lote]` inner join `[NFCe_In_XML_DB].[NFCeIn].[LoteCompl]`
 * StoredProc NFCeIn.**usp_LoteUpdate** atualiza `[NFCeIn].[Lote]` (tempoProcessamento) e (caso status != 103 e 105) insere em `[NFCeIn].[ProcessamentoLog]` (em ambos os casos, limitado a lotes recebidos nas últimas 48 horas)

Pós-Validações:

 * StoredProc NFCeOut.**usp_NFCeInsert**
   * executa StoredProc NFCeOut.**usp_GetAutonumber** , que insere em `[NFCeOut].[AutonumberProtocolo]` se ano ímpar, ou insere em `[NFCeOut].[AutonumberProtocolo2]` caso contrário; e retorna o contador de registro `(SCOPE_IDENTITY())`
   * insere na tabela `[NFCeOut].[Protocolo]` gerando pKey
   * insere nas tabelas, usando pKey 
     * `[NFCeOut].[NFCe]`
     * `[NFCe_Out_Constraint_DB].[NFCeOut].[NFCe_Constraint]`
     * `[NFCe_OUT_XML_DB].[NFCeOut].[NFCeXml]`
     * `[NFCeOut].[ProtocoloRejeitado]`
   * se NF existe e cStat in (100,110,150,301,302), executa StoredProc NFCeOut.`usp_DadosTransmissaoInsert`
     * lê pKeyCertificado na tabela `[NFCeOut].[Certificado]` e insere/gera se não encontra
     * insere pKeyCertificado na tabela `[NFCe_Out_XML_DB].[NFCeOut].[Certificado]`
     * insere na tabela **[NFCeOut].[DadosTransmissao]**
 * **Obs:** tenta executar a Autorização mais uma única vez se der exceção
 * StoredProc NFCeIn.**usp_LoteUpdate** atualiza `[NFCeIn].[Lote]` (tempoProcessamento) e (caso status != 103 e 105) insere em `[NFCeIn].[ProcessamentoLog]` (em ambos os casos, limitado a lotes recebidos nas últimas 48 horas)

### Retorno:

None​