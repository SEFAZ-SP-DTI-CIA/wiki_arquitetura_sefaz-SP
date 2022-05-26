# Serviço RetornoAutorização
(consultar resultado da chamada assíncrona Serviço Autorização)
 
## Verificações:
 
### Em lote:
 * BlocoA (Validação do Certificado Digital do Transmissor (protocolo SSL))
   * ICP-Brasil
   * Extensão de CNPJ
   * Uso indevido
     * StoredProc NFCeOut.**usp_ConfigSistemaSelect** lê `STATUS_CACHE_USO_INDEVIDO` na tabela `[NFCe_Out_Constraint].[NFCeOut].[ConfigSistema]`
     * Se habilitada a Verificaçao de Uso Indevido
       * StoredProc NFCeOut.**usp_ConfigSistemaSelect** lê `MAX_REQUISICOES` na tabela `[NFCe_Out_Constraint].[NFCeOut].[ConfigSistema]`
     * Se Contador == MAX_REQUISICOES+1
       * StoredProc NFeOut.**_usp_LogUsoIndevidoInsert (não portado)_**

 * BlocoB (Validação Inicial da Mensagem no Web Service)
   * Tamanho do XML de dados (<= 500KB) de acordo com TipoMensagem, mas todos estão iguais (exceto Eventos)
   * Verifica status do servidor de processamento
     * StoredProc NFCeOut.**usp_ConfigSistemaSelect** lê `ONLINE` na tabela `[NFCe_Out_Constraint].[NFCeOut].[ConfigSistema]`

 * BlocoC (Validação das informações de controle da chamada ao Web Service)
   * Validação das informações de controle da chamada ao Web Service
     * cabeçalho SOAP (`nfeCabecMsg`): UF == 35? versão dos dados <= **3.10m** ? (esse teste só é diferente para os tipos Consulta e Evento)

 * BlocoD (Validação da Mensagem do Pedido)
   * Validação contra XML Schema `"consReciNFe_v3.10.xsd"`
   * Verifica Namespace (é diferente em PRODUCAO), Prefixo no Namespace, Espaços em branco, Encoding UTF-8

 * BlocoE2 (Validação das Regras de Negócio da Consulta Recibo)
   * Verifica se Tipo de Ambiente (PRODUCAO/HOMOLOGACAO), UF e Tipo autorizador do recibo são válidos
   * StoredProc NFCeIn.**usp_LoteStatusSelect** lê tabela `[NFCeIn].[Lote]` do último mês
   * Verifica qual fila está o lote (103 ou 105) e se CNPJ do transmissor é o mesmo da consulta
   * StoredProc NFCeOut.**usp_ProtocoloSelectPorLote** lê `[NFCeOut].[Protocolo]` inner join `[NFCeOut].[NFCe]` inner join `[NFCe_Out_Constraint_DB].[NFCeOut].[NFCe_Constraint]` dos últimos 2 meses
   * StoredProc NFCeOut.**usp_ProtocoloRejeitadoSelectPorLote**  lê `[NFCeOut].[ProtocoloRejeitado]` dos últimos 2 meses

### Ações:

 * Roda StoredProc se retornar nenhum protocolo: NFCeOut.**usp_DateTimeUTCSelect** pega `SYSDATETIMEOFFSET()` do banco

### Retorno:

 * Lista de protocolos por lote, lista de protocolos rejeitados por lote