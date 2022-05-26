## Recepção SVD
### Implementação
Thread dentro do Windows Service CTe Envio Eventos. 
Algoritmo Producer-Consumer para cada UF
Producer: 
  * Acessa continuamente o web-service do SVD-RS usando NSU da tabela [CTe_Out].[SVD].[RECEPCAO_UF] para cada órgão: 
    * 35 (SP) 
    * 90 (simulando RFB - sincronismo de chave)
    * 95 (SVC-SP - representa as UFs que são autorizadas por SP normalmente como Sefaz Virtual - atualmente PE, RR e AP)
  * Coloca o lote em [CTe_Out].[SVD].[RECEPCAO_XML]

Consumer: 
  * Lê [CTe_Out].[SVD].[RECEPCAO_XML]. Dependendo do documento:
    * Se for sincronismo de chave, armazena cada elemento em [CTe_Out]: 
      * [SVD].[RECEPCAO_NSU] 
      * [SVC].[Evento]
    * Se forem docs de interesse de SP, armazena cada elemento em [CTe_Out_UFs]: 
      * [dbo].[ProtocoloCTe] 
      * [dbo].[DFe_XML] 
      * [dbo].[CTe] (ou CTeOS/GVTe), 
      * [CTe_Out].[SVD].[RECEPCAO_NSU]
    * Se forem docs autorizados para SP, armazenda cada elemento em [CTe_Out]: 
      * [SVD].[RECEPCAO_NSU], 
      * [CTeOut].[Protocolo], 
      * [CTeOut].[CTe_Constraint] , 
      * [CTeOut].[CTe], 
      * [CTeOut].[CTeDadosApoio] 
      * [CTe_Out_XML].[CTeOut].[CTeXml], 
      * e outras - tabelas acima podem variar pelo tipo de documento (CT-e / CT-eOS / GVT-e, Eventos, Inutilização)
  * Se houver erro, enfileira registro em [CTe_Out]: [SVD].[QUEUE_DATA_REPROCESSAMENTO] e [SVD].[QUEUE_REPROCESSAMENTO]
  * Atualiza último NSU em [CTe_Out].[SVD].[RECEPCAO_UF]

Eventuais exceções serão incluidas na tabela [CTe_Out].[SVD].[LOG_SISTEMA], mas nesses casos o registro também será incluído nas tabelas de reprocessamento e portanto, devem ser consultadas para verificar o erro.

### Pontos a destacar
Existe uma thread para cada cOrgão (UF) da tabela [CTe_Out].[SVD].[RECEPCAO_UF]. Nessa tabela temos:

  * RECE_UF: código da UF
  * RECE_TIPO_SOLICITACAO: representa a tag <indDFe> enviada para o SVD-RS. Segundo o BT 2019.003 v1.06:
      * 0: DF-e autorizados para o solicitante
      * 1: DF-e destinados ao solicitante
      * 2: Todos DF-e de interesse do solicitante
  * RECE_IS_SINC_CHAVES: 1 indica que será apenas sincronismo de chaves, 0 significa que o XML será recuperado. Invertendo-se dá exatamente a tag <indRetXML> descrita no BT 2019.003 v1.06:
      * 0: Retorna o lote apenas com dados de controle de cada DF-e (Substitui sincronismo de chaves)
      * 1: Retorna o lote completo, contendo o proc do DF-e compactado.
  * RECE_ULTIMO_NSU: valor a ser preenchido na tag <ultNSU>, representa o valor do último NSUSVD recebido, e que portanto será colocado na tag <ultNSU> no próximo pedido.

O valor <indDFe> = 0 é usado para cOrgao = 35 (SP) em conjunto com <indRetXML>=1, o que trará documentos autorizados para o solicitante (SP). Aqui são EPECs ou documentos de SP que foram autorizados pelo RS em contingência, quando SP está fora do ar.
O valor <indDFe> = 1 é usado para cOrgao = 35 (SP) em conjunto com <indRetXML>=1, o que trará documentos de interesse do solicitante (SP) e serão armazenados na base CTe_Out_UFs.
O valor <indDFe> = 1 é usado para cOrgao = 90 (simulando RFB) em conjunto com <indRetXML>=0, o que trará documentos de interesse da RFB (ou seja, todos) mas apenas para sincronismo de chaves (só que os documentos autorizados em SP não são armazenados).
O valor <indDFe> = 1 é usado para cOrgao = 95, <indRetXML>=1, o que trará documentos autorizados para o solicitante. Aqui são documentos das UFs conveniadas na Sefaz Virtual de SP que foram autorizados pelo RS em contingência, quando SP está fora do ar. Aqui cabe a observação que, pelo manual, deveria ser zero, mas o RS não implementou de acordo com o manual; testes práticos mostraram que zero não traz nenhum documento, enquanto que 1 ou 2 trazem os mesmos documentos.
O valor <indDFe> = 2 não é usado na Recepção SVD, mas somente em outro módulo, o [Reprocessamento Recepção SVD](http://tfs.intra.fazenda.sp.gov.br:8080/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/238/Reprocessamento-Recep%C3%A7%C3%A3o-SVD)

O local que define quais são as UFs conveniadas na Sefaz Virtual de SP e quais são atendidas quando o RS está fora do ar (autorização em contingência) está na classe CTeUfUtils. Lá se pode ver, por exemplo, que MT e MS não são atentidas nunca por SP, portanto nem precisaria fazer o sincronismo de chave dessas UFs.

Existem duas numerações distintas de NSUs envolvidos na Recepção SVD e que são retornados pelo SVD-RS. De acordo com o BT 2019.003 v1.06:

  * NSUSVD: Número sequencial único do SVD para o órgão solicitante . Ou seja:
      * é gerado pelo SVD-RS para o órgão que solicitou - no caso utilizamos 35, 90 e 95 como órgãos. 
      * na tabela [SVD].[RECEPCAO_NSU] é armazenado na coluna RENS_NSU_SVD. 
      * o respectivo órgão solicitante é armazenado na coluna RENS_UF.
  * NSUAut: Número sequencial único do Ambiente autorizador do DF-e. Ou seja, 
      * é gerado pela UF que autorizou o documento. 
      * na tabela [SVD].[RECEPCAO_NSU] é armazenado na coluna  RENS_NSU_AUTORIZADOR. 
      * a UF solicitante não é armazenada nessa tabela.

O NSUSVD é que é usado para fazer o request para o SVD-RS. O NSUAut só é armazenado e não é lido/utilizado (exceto para análises).

### Principais tabelas envolvidas

  * [CTe_Out].[SVD].[RECEPCAO_UF]. -> controle dos parâmetros enviados para o RS
  * [CTe_Out].[SVD].[RECEPCAO_XML] -> armazena os lotes do RS
  * [CTe_Out].[SVD].[RECEPCAO_NSU] -> armazena informação de cada documento recebido do SVD-RS
  * [CTe_Out].[SVD].[QUEUE_DATA_REPROCESSAMENTO] -> registros para reprocessamento (erro na recepção) . Para o caso do processo aqui documentado, QDRE_ORIGEM = 3 (CTeOrigemReprocessamentoSvd.ERRO_RECEPCAO)

[Script para acompanhamento (clique para abrir)](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/240/Script-Recep%C3%A7%C3%A3o)

Classes C# que implementam a lógica principal
  * CTeRecepcaoSvdBll (Producer)
  * CTeRecepcaoSvdProcessItemBLL_v1_00 (Consumer)
