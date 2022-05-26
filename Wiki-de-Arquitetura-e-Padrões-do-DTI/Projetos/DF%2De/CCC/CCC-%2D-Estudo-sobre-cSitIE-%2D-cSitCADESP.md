cSit:
 
Proc [CADESP].[CCC].[usp_CCC_Obter_Contribuinte_Fila]
- cSit é obtido da proc [CADESP].[CCC].[UFC_OBTEM_SITUACAO_CONTRIBUINTE_CCC]
-- mapeia ID_SITUACAO_ESTABELECIMENTO em cSit:
-- 1 -> 1
-- 2 -> 1 se ID_OCORRENCIA_CADASTRAL = 41,38,39
-- 2 -> 0 caso contrário
-- 3 -> 0
-- 4 -> 0
-- 5 -> 0
-- x -> 1
- indAtua é o obtido da proc [CADESP].[CCC].[usp_CCC_Obter_Indicador_Atualizacao]
-- 1 se houve alteração de CNPJ, IE, INDST, ID_OCORRENCIA_CADASTRAL, ID_SITUACAO_ESTABELECIMENTO, DT_INICIO_VALIDADE_SITUACAO
-- 2 se houve alteração de nome, REGAPUR, nome-fantasia, ID_CNAE_FISCAL, DT_INICIO_ATIVIDADE, DT_FIM_ATIVIDADE, IE_UNICA
-- 9 se ID_OCORRENCIA_CADASTRAL_ATUAL = 93, 59, 63, 62, 55
-- 0 caso contrário

 
Valores lidos pela proc acima são colocados as is em memória no GeraFila, objeto CccGeraFilaEntity
 
Outra thread do GeraFila coloca os valores as-is na tabela ContribuintesAtualizacoesQueue pela proc do [CCC].[cccEnvio].[usp_CCC_Contribuinte_Atualizacoes_Queue_Insert]
- se tiver outras IEs de SP
-- se for indAtua=9, baixa todas, inserindo registro com cSit=0 indAtua=9 se contribuinte não estava com indAtua=9 .
-- se for indAtua!=9, baixa somente as que tiverem cSit=1, inserindo registro com cSit=0 e indAtua=9 se contribuinte não estava com indAtua=9 .
 
Thread do Envio chama [CCC].[cccEnvio].[usp_CCC_GetFilaEnvio] para ler ContribuintesAtualizacoesQueue, objeto em CccEntity
- cSit é mapeado em cSitIE, sem conversão
- indAtua é lido sem conversão
 
Outra thread do Envio pega o objeto CccEntity e envia request para CCC-RS.
- cSitIE não é alterado
- indAtua não é enviado
 
Ocorrendo sucesso no envio, grava resultado na tabela CCC.CCC.Contribuintes pela proc CCCEnvio.usp_CCC_Grava_Envio_Com_Sucesso:
- não passa cSitIE para a proc
- passa apenas indAtua
- pega (e apaga) dados da tabela ContribuintesAtualizacoesQueue
- atualiza cSit=9 se indAtua=9
- se for contribuinte novo, insere registro na tabela mapeando cSit em cSitIE, sem alteração no valor de indAtua
- se for contribuinte alterado, altera registro na tabela mapeando cSit em cSitIE, sem alteração no valor de indAtua
 
O campo cSit é mapeado pela seguinte lógica:
ID_SITUACAO_ESTABELECIMENTO = 1 ==> cSit = 1
ID_SITUACAO_ESTABELECIMENTO = 2 ==> cSit = 1 se ID_OCORRENCIA_CADASTRAL = 41,38,39
ID_SITUACAO_ESTABELECIMENTO = 2 ==> cSit = 0 caso contrário
ID_SITUACAO_ESTABELECIMENTO = 3 ==> cSit = 0 caso contrário
ID_SITUACAO_ESTABELECIMENTO = 4 ==> cSit = 0 caso contrário (e nesse ponto tem uma lógica que retornaria 1 para ID_OCORRENCIA_CADASTRAL_ATUAL = 93, 59, 63, 62, 55 - mas essa lógica está inativa no códiga, está "comentada")
ID_SITUACAO_ESTABELECIMENTO = 5 ==> cSit = 0 caso contrário
ID_SITUACAO_ESTABELECIMENTO diferente de 1,2,3,4,5 ==> cSit = 1
 
Temos um campo interno chamado indAtua, que é o que comentei no e-mail abaixo, ele vale 9 se ID_OCORRENCIA_CADASTRAL_ATUAL = 93, 59, 63, 62, 55 .
 
Esse campo indAtua, se for 9, vai fazer a alteração que comentei - vai colocar cSit=9 no banco apesar de ter mandado valor 0 no request para o RS.