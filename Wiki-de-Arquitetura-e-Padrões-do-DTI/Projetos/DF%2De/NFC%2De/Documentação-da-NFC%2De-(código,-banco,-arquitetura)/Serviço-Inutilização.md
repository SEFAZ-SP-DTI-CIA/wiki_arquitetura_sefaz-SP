# Serviço Inutilização
 
## Verificações:
 
### Em lote:
 * **BlocoA (Validação do Certificado Digital do Transmissor - protocolo SSL)**
   * ICP-Brasil
   * Extensão de CNPJ
   * Uso indevido
     * StoredProc NFCeOut.**usp_ConfigSistemaSelect** lê `STATUS_CACHE_USO_INDEVIDO` na tabela `[NFCe_Out_Constraint].[NFCeOut].[ConfigSistema]`
     * Se Uso Indevido
       * StoredProc NFCeOut.**usp_ConfigSistemaSelect** lê `MAX_REQUISICOES` na tabela `[NFCe_Out_Constraint].[NFCeOut].[ConfigSistema]`
     * Se Contador == MAX_REQUISICOES+1
       * StoredProc **_NFeOut.usp_LogUsoIndevidoInsert (não portado)_**

 * **BlocoB (Validação Inicial da Mensagem no Web Service)**
   * Tamanho do XML de dados (<= 500KB) de acordo com TipoMensagem, mas todos estão iguais (exceto Evento)
   * Verifica status do servidor de processamento
     * StoredProc NFCeOut.**usp_ConfigSistemaSelect** lê `ONLINE` na tabela `[NFCe_Out_Constraint].[NFCeOut].[ConfigSistema]`

 * **BlocoC (Validação das informações de controle da chamada ao Web Service)**
   * Validação das informações de controle da chamada ao Web Service
     * cabeçalho SOAP (`nfeCabecMsg`): UF == 35? versão dos dados <= **3.10m** ? (esse teste é diferente para Consulta e diferente para Evento)

 * **BlocoD (Validação da Mensagem do Pedido)**
   * Validação contra XML Schema "`inutNFe_v3.10.xsd`" (é diferente para cada Serviço)
   * Verifica Namespace (é diferente em PRODUCAO), Prefixo no Namespace, Espaços em branco, Encoding UTF-8

 * **BlocoE (Validação do Certificado Digital utilizado na Assinatura Digital)**
   * Certificado Inválido (Inexistente / Versão difere de "3" / Basic Constraint não pode ser de AC / Key Usage não define "Assinatura Digital" e "Não Recusa")
   * Log de erros:
     * loga o Id da primeira infNFe (é diferente para LoteZip e Inutilização e **não** trata outros tipos);
     * StoredProc NFCeOut.**usp_ValidacaoCertificadoLogInsert** insere em `[NFCeOut].[ValidacaoCertificadoLog]`
   * Validade do certificado (data inicio - data fim): StoredProc NFCeOut.**usp_DateTimeUTCSelect** pega `SYSDATETIMEOFFSET()` do banco
   * Falta da extensão CNPJ no certificado
   * Cadeia do Certificado (AC emissora não cadastrada / AC revogado / não assinado pela AC emissora)
   * Falta ou erro na LCR - **_não implementado_**
   * Certificado Revogado
   * Raiz difere de "ICP-Brasil

 * **Bloco F (Validação da Assinatura Digital)**
   * Assinatura difere do padrão do sistema (Falta atributo Id / falta Transform Algorithm) - **_essas validações são implementadas pelo Schema XML da Signature_**
   * SignatureValue difere do calculado
   * CNPJ-base do emitente difere do CNPJ-base do certificado

 * **Bloco I (Pedido de Inutilização de numeração de NFC-e - Regras de Negócio)**
   * Tipo de Ambiente / tipo de UF diferem do Web Service
   * Validação de datas (inutilização não pode ser < 2006 e não pode ser > ano atual): StoredProc NFCeOut.**usp_DateTimeUTCSelect** pega `SYSDATETIMEOFFSET()` do banco
   * Validação de Faixas (Inicial < Final)
   * Sefaz/Sefaz Virtual - Série entre 0 - 889 / SCAN - Série entre 900 - 999
   * Quantidade máxima de numeração a inutilizar = 10000
   * Validação do campo Id
   * Verificação de cadastro de contribuinte (Emitente não autorizado / Situação Fiscal irregular)
     * StoredProc **NFCeOut.usp_ContribuintesHabilitadosSelectPorCnpj** (NFCeOut_Constraint_DB) lê as informações da tabela `[NFCe_Out_Constraint].[NFCeOut].[ContribuintesHabilitados]`
     * Se der exceção, a StoreProc **NFeOut.usp_CadespLogConsultaInsert** **_(que não está implementada)_** deveria inserir na tabela `[NFe_Out].[NFeOut].[CadespLogConsulta]` **_(que também não existe)_** o método que deu exceção, cnpj, ie.
     * StoredProc **NFCeOut.usp_ConfigSistemaSelect** lê `VALIDACAO_CADESP_ATIVO` na tabela `[NFCe_Out_Constraint].[NFCeOut].[ConfigSistema]`
     * Se `VALIDACAO_CADESP_ATIVO` == true, roda StoredProc **USP_Consulta_Estabelecimento_NFCe**
e **USP_Consulta_EstabelecimentoPorIE_NFCe** no `Banco de Dados do CADESP`

   * Verificação se **_já existe pedido de inutilização igual_**
   * Verificação se **_algum número da faixa de inutilização pertence a uma faixa anterior_**
   * Verificação se **_existe NFC-e utilizada na faixa de inutilização solicitada_**
     * Storeproc **NFCeOut.usp_InutNFCeCheck2** realiza as checagens acima lendo as tabelas `[NFCe_Out_Constraint_DB].[NFCeOut].[NFCe_Constraint]` , `[NFCeOut].[Protocolo]` e `[NFCeOut].[InutNFCe]`
   * Passada a validação a Storeproc NFCeOut.usp_InutNFCeInsert2 insere as informações da inutilização em [NFCeOut].[InutNFCe]

### Retorno:

 * Intervalo (inicio - fim) das NF inutilizadas