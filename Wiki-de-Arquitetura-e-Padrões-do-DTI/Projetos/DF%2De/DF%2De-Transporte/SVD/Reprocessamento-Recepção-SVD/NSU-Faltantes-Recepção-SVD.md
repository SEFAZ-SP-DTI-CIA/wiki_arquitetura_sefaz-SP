## NSU Faltantes Recepção SVD
### Implementação
Thread dentro do Windows Service CTe Envio Eventos 

Loop infinito processando registros da tabela [CTe_Out]. [SVD].[NSU_FALTANTE_UF] , cada um tendo a informação de uma UF e do último NSU Faltantes processado por ela.

Para cada registro:

  * Chama a proc [SVD].[USP_NSU_FALTANTES], para cada registro. Essa proc pega o último NSU faltantes e faz rodadas de 50 em 50 NSUs a partir desse valor, até um máximo de 5000 nsUs
    * Procura NSU na tabela [SVD].[RECEPCAO_XML] (que contém lotes)
    * Verifica se não está na tabela de NSUs recebidos [SVD].[RECEPCAO_NSU]
    * Se não estiver, reenfileira para a Recepção SVD, adicionando registro em [SVD].[QUEUE_DATA_REPROCESSAMENTO] e [SVD].[QUEUE_REPROCESSAMENTO]
  * Ao final do Process, atualiza o último NSU Faltante para a UF em [SVD].[NSU_FALTANTE_UF] 

### Pontos a destacar
O conceito de "NSU Faltantes" se refere a um NSU estar na tabela de lotes [SVD].[RECEPCAO_XML] mas não ter sido processado/recebido (não estar na tabela [SVD].[RECEPCAO_NSU]). 

Em outros sistemas, como da NF-e, como não há 'pulos' no NSU distribuído pelo AN, pode-ser verificar se existe alguma falha na sequência dos NSUs recebidos. Porém isso só seria válido para o SVD se os requests para SVD-RS tivessem <indDFe> igual a 2 (veja mais sobre essa tag em  [[DocumentacaoRecepcaoSVD|Recepção SVD]]); como não é o caso, adotou-se a abordagem de verificação do banco.

### Principais tabelas envolvidas
  * [CTe_Out].[SVD].[NSU_FALTANTE_UF] -> controle da lógica
  * [CTe_Out].[SVD].[RECEPCAO_XML]  -> contém lotes de registros recebidos da SVD-RS
  * [CTe_Out].[SVD].[RECEPCAO_NSU]  -> contém informações de todos os documentos recebidos da SVD-RS

[Script para acompanhamento (clique para abrir)](http://tfs.intra.fazenda.sp.gov.br:8080/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/242/Script-NSU-Faltantes-Recep%C3%A7%C3%A3o)

### Classes C# que implementam a lógica principal
  * CTeNsuFaltantesRecepcaoSvdController