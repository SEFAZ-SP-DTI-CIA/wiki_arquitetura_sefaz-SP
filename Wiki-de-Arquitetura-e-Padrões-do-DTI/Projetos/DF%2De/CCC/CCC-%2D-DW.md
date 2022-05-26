**Stored Procedures**

PROCEDURE [CCC].[DW].[usp_CCC_select]

  - Apaga N registros da tabela [CCC].[DW].[QUEUE]
  - Insere os N registros na tabela [ETL_NFE].[DWCCC].[ProcessamentoQueue], adicionado GUID passado
  - Seleciona dados das tabelas [CCC].[CCC].[Contribuinte] e [CCC].[CCC].[ContribuinteHistorico] cujos registros tenham mesmos NSUCCC e NSUCCCMovto dos registros da fila acima

PROCEDURE [ETL_NFE].[DWCCC].[usp_Grava_Resultado_Com_Sucesso_Por_UID]

  - Apaga registros da [ETL_NFE].[DWCCC].[ProcessamentoQueue] com determinado GUID
  - Insere registros apagados na [ETL_NFE].[DWCCC].[ResultadoProcessamento], com indicação houveErro=0 e situaçãoFinal=0

PROCEDURE [ETL_NFE].[DWCCC].[usp_LogErro]

  - Insere registro na tabela [ETL_NFE].[DWCCC].[LogErros], com o texto do erro e stack-trace.
  - Se foi passado GUID válido:
    - Apaga registros da [ETL_NFE].[DWCCC].[ProcessamentoQueue] (independente do GUID, estranho .... poderia ter um WHERE aqui)
    - Insere registro na [ETL_NFE].[DWCCC].[ResultadoProcessamento], indicando houveErro=1 e situacaoFinal=255 

**Fluxo**

Verifica se pode rodar: verifica flag do Sybase, só executa se for zero

Verifica se deve rodar nesse instante (flag do banco de config - chave "EXECUTAR_AGORA")

  - se true, já roda nesse instante, setando imediatamente a flag para false
  - se false, só deixa rodar se estiver no horário de execução; se não estiver no horário de execução, dorme até esse horário.

Passa a flag de controle do Sybase para 1

Método BaseMainWorker::Process()

  - Lê maior valor de NSUAtu  (equivalemte a NSUCCCMovto) da tabela [CCC].[DW].[QUEUE]
  - Loop enquanto (1) quantidade registros lidos < limite e (2) NSUAtu extraído menor do que o máximo
    - Lê registro da fila, proc [CCC].[DW].[usp_CCC_select]
    - Faz o parse (método CCCMainWorker::ParseAndEnqueueDados), que acaba chamando BaseSerializer::Serialize, que escreve registro no arquivo físico
  - Chama o LoadTable do arquivo

 Se não houve erro, passa a flag de controle do Sybase para 2