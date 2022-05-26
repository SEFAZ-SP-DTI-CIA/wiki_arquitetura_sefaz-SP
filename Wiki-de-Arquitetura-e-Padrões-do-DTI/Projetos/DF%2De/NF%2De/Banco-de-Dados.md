# NFe

  * ​DW 
    * Tabelas​ 
    * Stored Procedures

  * ​Crédito Acumulado
    * Tabelas​ 


|Nome  | Banco de Dados  | Descrição |
|--|--|--|
|NFeOut.NFe_Constraint​	|​NFe_Out	|​Tabela de unicidade |
|​NFeOut.NFe	​|NFe_Out	|​Tabela com os dados da nota fiscal eletrônica |
|NFeEventos.Evento_Constraint​	|​NFe_Out	​|Tabela de |
|NFe.NFeXml	|​DFe_XML ​|​Tabela contendo o layout XML compactado |
|NFeUFs.DFeInfoCompl	​|NFe_Out_UFs	|​Informações complementares |
|​dbo.ProtocoloNFe	​|NFe_Out_UFs	|​Tabela de protocolos |
|​dbo.ProtocoloNFeLog	​|NFe_Out_UFs	|Tabela de protocolos utilizada no legado |
|​NFeEventos.Evento	|​NFe_Out_UFs​	​|Tabela de eventos |
 
  * ​Crédito Acumulado
    * Stored Procedures

|​Nome	​|Banco de Dados	|Aplicações​	|​​Descrição	|​Entrada	|Saída​|
|--|--|--|--|--|--|
|​​​NFeUFs.usp_CreditoAcumulado_ConsultaNFeEmLote |NFe_Out_Ufs |CreditoAcumulado (ConsultarLoteSp, ConsultarLoteIdNFeSp​) |​​Insere na tabela #NFes a lista de dados do tipo NFeEntity, atualiza as informações de NSU, IDNFE, TimeStampRecebimento a partir de um join da tabela NFeUFs.DFeInfoCompl; atualiza o campo status e TimestamReg de uma junção da tabela ProtocoloNFeLog; exclui as linhas que tenham o código diferente de 101 (canceladas) caso tenha o número de idNFe repetidos; Das informações restantes caso tenham algumas com a situação do tipo 100 (Autorizado), o campo status é atualizado para 101 desde que tenha correspondentes nas colunas tpEvento e timestampReg da tabela NfeEventos.Evento. |@dadosNFe [NFeUFs].[DadosNFeTable] |Ao final tem se o resultset das tabelas temporária #NFes mais DFe_XML relacionadas pelos campos NSU e TimestampRecebimento.​ |
|NFeOut.usp_CreditoAcumulado_ConsultaNFeEmLote  |NFe_Out |​CredutoAcumulado(ConsultarLoteUfs) |Insere os dados vindos do parâmetro de entrada na tabela #NFes, após é feita uma atualização, a partir do relacionamento da tabela NFeOut.NFe_Constraint, nos campos pKey, idNFe, status e timestampReg. Com o resultado, verifica se existe alguma linha com a informação igual a 100 no campo status. Caso tenha alguma tupla que contenha a informação é feita uma nova atualização do campo status de 100 (autorizado) para 101 ( cancelado). |@dadosNFe [NFeOut].[DadosNFeTable] |​Ao final tem se o resultset das tabelas #NFes e [DFe_XML].[NFe].[NFeXml].​ |
