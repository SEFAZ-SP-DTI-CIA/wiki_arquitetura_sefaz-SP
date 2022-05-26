## Reprocessamento Recepção SVD
### Implementação

Thread dentro do Windows Service CTe Envio Eventos 
Algoritmo Producer-Consumer
Producer:

  * Desenfileira registros de CTe_Out: [SVD].[QUEUE_DATA_REPROCESSAMENTO] e [SVD].[QUEUE_REPROCESSAMENTO], apagando o registro desta última tabela.
  * Procedure usada: SVD.USP_REPROCESSAMENTO_DEQUEUE

Consumer:
  * Se o registro desenfileirado não contiver o XML - não possui Pkey [QDRE_RECEPCAO_XML_PKEY] (utiliza  xml da tabela [SVD].[RECEPCAO_XML]), nem XML definido na coluna [QDRE_XML] - manda request para o RS e armazena resutado em [CTe_Out].[SVD].[RECEPCAO_XML]
  * Com o XML, chama os métodos da [Recepção SVD](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Projetos/DF%2De/DF%2De-Transporte/SVD/Recepção-SVD) para armazenar o documento.
  * Em caso de sucesso, chama a procedure [SVD].[USP_REPROCESSAMENTO_FINALIZE] passando status de sucesso

Pontos a destacar
O enfileiramento de registros para reprocessamento é feito pela procedure [SVD].[USP_REPROCESSAMENTO_ENQUEUE], que insere novos registros nas duas tabelas. Quem chama:
  * Falta de NSU detectado por [NSU Faltantes Recepção SVD](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/241/NSU-Faltantes-Recep%C3%A7%C3%A3o-SVD)
  * Erro na [Recepção SVD](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Projetos/DF%2De/DF%2De-Transporte/SVD/Recepção-SVD)

Já o reenfileiramento de registros:
  * é feito pelo próprio algoritmo de Reprocessamento Recepção SVD, quando ocorre erro, se não chegou a um número máximo de retentativas (MAX_RETRIES)
  * caso não tenha chegado no MAX_RETRIES mas passe de 2, indica possível erro no XML armazenado em banco; nesse caso, apaga-se o XML para mais 2 tentativas (apagando o link com XML vai forçar a ir no RS)
  * procedure chamada: [SVD].[USP_REPROCESSAMENTO_REENQUEUE]
  * loga erro, se houver, na tabela [SVD].[QUEUE_LOG_REPROCESSAMENTO]

Se houve erro a quantidade de retentativas chegou a um número máximo (MAX_RETRIES), chama a procedure [SVD].[USP_REPROCESSAMENTO_FINALIZE] que 
  * atualiza o status na tabela [SVD].[QUEUE_DATA_REPROCESSAMENTO] 
  * insere log  na tabela [SVD].[QUEUE_LOG_REPROCESSAMENTO]

Em caso de exceção do algoritmo, é gerado ainda um log de erro em [SVD].[LOG_SISTEMA].

Status possíveis do reprocessamento, armazenado na tabela [SVD].[QUEUE_DATA_REPROCESSAMENTO], coluna QDRE_STATUS:

  * 0    NONE                           
  * 1    NAO_PROCESSADO - utilizado quando se passa o registro para ser processado pela Recepção SVD mas essa falha.
  * 2    ERRO                        - situações de erro de processamento
  * 3    ERRO_EXCECAO       - situações de erro de exceção no processamento
  * 4    ERRO_ACESSO_WS  - SVD-RS não retornou status 118 (que significa documento encontrado)
  * 5    ERRO_XML               
  * 6    ERRO_REGISTRO_NAO_ENCONTRADO - XML não encontrado no lote retornado pelo SVD-RS
  * 99  SUCESSO

Origens possíveis do reprocessamento, armazenado na tabela [SVD].[QUEUE_DATA_REPROCESSAMENTO], coluna QDRE_ORIGEM:
  * 0  NONE
  * 1  NSU_FALTANTES
  * 2  NSU_FALTANTES_INEXISTENTE
  * 3  RECEPCAO
  * 4  MANUAL

O envio de request para a SVD-RS é feita utilizando-se 
  * <indDFe> = 2 caso sejam documentos de SP (UF = 35) ou então autorizados para SP pelo RS quando SP está fora (UF = 95)
  * <indDFe> = 0 caso contrário (ou seja, UF = 90, sincronismo de chaves)

Lembrando que a definição de <indDFe> é:
  * 0: DF-e autorizados para o solicitante
  * 1: DF-e destinados ao solicitante
  * 2: Todos DF-e de interesse do solicitante

### Principais tabelas envolvidas
  * [CTe_Out].[SVD].[QUEUE_REPROCESSAMENTO]  -> controle de fila 
  * [CTe_Out].[SVD].[QUEUE_DATA_REPROCESSAMENTO]  -> dados dos registros sendo reprocessados
  * [CTe_Out].[SVD].[QUEUE_LOG_REPROCESSAMENTO] -> logs do Reprocessamento Recepção SVD
  * [CTe_Out].[SVD].[LOG_SISTEMA]  -> logs genéricos do SVD

[Script para acompanhamento (clique para abrir)](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/247/Script-Reprocessamento-Recep%C3%A7%C3%A3o-SVD)

### Classes C# que implementam a lógica principal
  * DFeBaseQueueBLL<CTeReprocessamentoRecepcaoSvdDTO>
  * CTeReprocessamentoRecepcaoSvdBll