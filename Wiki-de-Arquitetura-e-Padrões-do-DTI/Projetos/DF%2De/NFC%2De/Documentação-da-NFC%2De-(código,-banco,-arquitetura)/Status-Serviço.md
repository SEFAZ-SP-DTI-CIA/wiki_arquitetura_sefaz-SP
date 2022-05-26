# Serviço Status
 
## Verificações:

### Em lote:

 * BlocoA (Validação do Certificado Digital do Transmissor (protocolo SSL))
   * ICP-Brasil
   * Extensão de CNPJ
   * Uso indevido
     * StoredProc NFCeOut.**usp_ConfigSistemaSelect** lê `STATUS_CACHE_USO_INDEVIDO` na tabela `[NFCe_Out_Constraint].[NFCeOut].[ConfigSistema]`
     * Se Uso Indevido
       * StoredProc NFCeOut.**usp_ConfigSistemaSelect** lê `MAX_REQUISICOES` na tabela `[NFCe_Out_Constraint].[NFCeOut].[ConfigSistema]`
     * Se Contador == MAX_REQUISICOES+1
       * StoredProc **_NFeOut.usp_LogUsoIndevidoInsert (não portado)_**

 * BlocoB (Validação Inicial da Mensagem no Web Service)
   * Tamanho do XML de dados (<= 500KB) de acordo com TipoMensagem, mas todos estão iguais (exceto Evento)
   * Verifica status do servidor de processamento
     * StoredProc NFCeOut.**usp_ConfigSistemaSelect** lê `ONLINE` na tabela `[NFCe_Out_Constraint].[NFCeOut].[ConfigSistema]`

 * BlocoC (Validação das informações de controle da chamada ao Web Service)
   * Validação das informações de controle da chamada ao Web Service
     * cabeçalho SOAP (**nfeCabecMsg**): UF == 35? versão dos dados <= **3.10m** ? (esse teste é diferente para Consulta e Evento)

 * BlocoD (Validação da Mensagem do Pedido)
   * Validação contra XML Schema "**consStatServ_v3.10.xsd**" (é diferente para cada Serviço)
   * Verifica Namespace (é diferente em PRODUCAO), Prefixo no Namespace, Espaços em branco, Encoding UTF-8

 * BlocoK (Validação do Pedido de Consulta de Status de Serviço – Regras de Negócios)
   * Verifica se Tipo de Ambiente (PRODUCAO/HOMOLOGACAO) e UF são válidos (UF == 35)
   * Verifica status do servidor de processamento (INDISPONIVEL | TEMPORARIAMENTE_INDISPONIVEL)
     * StoredProc NFCeOut.**usp_ConfigSistemaSelect** lê `ONLINE` na tabela `[NFCe_Out_Constraint].[NFCeOut].[ConfigSistema]`
   * StoredProc NFCeIn.**usp_TempoMedioProcessamentoSelect** lê `[NFCeIn].[TempoMedioProcessamento]`

### Ações:

 * None 

### Retorno:

 * Status do Serviço e tempo médio de resposta