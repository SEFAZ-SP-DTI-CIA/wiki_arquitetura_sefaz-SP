# Serviço Consulta
 
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
       * StoredProc NFeOut.**_usp_LogUsoIndevidoInsert (não portado)_**

 * BlocoB (Validação Inicial da Mensagem no Web Service)
     * Tamanho do XML de dados (<= 500KB) de acordo com TipoMensagem, mas todos estão iguais (exceto Evento)
     * Verifica status do servidor de processamento
       * StoredProc NFCeOut.**usp_ConfigSistemaSelect** lê `ONLINE` na tabela `[NFCe_Out_Constraint].[NFCeOut].[ConfigSistema]`

 * BlocoC (Validação das informações de controle da chamada ao Web Service)
   * Validação das informações de controle da chamada ao Web Service
     * cabeçalho SOAP (`nfeCabecMsg`): UF == 35? versão dos dados <= **3.1m** ? (esse teste é diferente para os demais tipos de serviço e diferente para Evento)

 * BlocoD (Validação da Mensagem do Pedido)
   * Validação contra XML Schema `"consSitNFe_v3.10.xsd"` (é diferente para cada Serviço)
   * Verifica Namespace (é diferente em PRODUCAO), Prefixo no Namespace, Espaços em branco, Encoding UTF-8

### Por NF:

 * BlocoJ (Regras de Negócio específicas para Consulta)
   * Ambiente (HOMOLOGACA/PRODUCAO) da NF igual do WebService? UF atendida?
   * Chave de acesso (idNFCe) válida?
     * Dígito verificador
     * UF IBGE
     * Ano >= 6 e <= current: StoredProc NFCeOut.**usp_DateTimeUTCSelect** pega `SYSDATETIMEOFFSET()` do banco
Mês !=0 e <=12
     * CNPJ vazio, zerado, !=14dígitos ou inválido
     * Modelo != 65
     * Número da NF == 0
     * Consulta idNFCe: StoredProc NFCeOut.**usp_NFCeProtocoloSelectPorIdNFe**
       * StoredProc NFCeOut.**usp_ProtocoloSelectPorIdNFe2** lê `[NFCe_Out_Constraint_DB].[NFCeOut].[NFCe_Constraint]` inner join `[NFCeOut].[Protocolo]` left join `[NFCeOut].[NFCe]` left join `[NFCeOut].[DadosApoio]`  
       * Deleta linhas com cStat == 102 ou chave diferente da consultada
       * lê tabela `[NFCe_OUT_XML_DB].[NFCeOut].[NFCeXml]`  
     * Converte códigos 301 e 302 para 110 (Uso denegado); qualquer código diferente de (101,150,151,301,302) é convertido em 100 (Autorizado)

### Ações:

 * Verifica eventos (Cancelamentos) **por NF**: storedProc NFCeOut.**usp_ProtocoloEventoSelectPorIdNFe** lê tabelas `[NFCeOut].[Evento_Constraint]` inner join `[NFCeOut].[Protocolo]` inner join `[NFCeOut].[Evento]` inner join `[NFCe_OUT_XML_DB].[NFCeOut].[EventoXml]` 
(a Proc acima é lida dentro do bloco da validação J)

### Retorno:

 * Status completo da NF, lista de eventos tipo Cancelamento