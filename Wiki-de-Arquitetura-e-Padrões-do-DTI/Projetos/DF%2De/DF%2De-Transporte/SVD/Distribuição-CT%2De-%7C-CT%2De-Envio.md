## Distribuição CT-e / CT-e Envio
### Implementação
Web Service CTeDistNSUAut.asmx
Implementa o "Serviço de Distribuição CT-e" descrito no BT 2019.003 v1.60

  * Recebe uma requisição<distCTeAut> contendo o útimo NSU que já foi processado por quem está pedindo.
  * Faz validações no request (blocos A, B, D e M) de acordo com o BT
  * Se validações retornaram sucesso, acessa o banco de dados pela procedure [CTe_Out].[SVD].[USP_NSU_SEL] para pegar os dados de NSUs imediatamente maiores do que o NSU requisitado.
  * Sistema responde com <retDistCTeAut> contendo um lote de até 50 documentos, cada um com um NSU único, sendo que para cada NSU é informado para quais órgãos (UFs) aquele documento deverá ser compartilhado pela SVD-RS.
  * Se configurado, é gerado log dessa requisição na tabela [CTe_Out].[SVD].[LOG_ENVIO] com os tempos (em ms) para execução da validação, retorno da Proc e construção do XML de retorno.

### Pontos a destacar
Todos os documentos de transporte autorizados por SP (incluíndo CTe, CTeOS, GTVe, Inutilização e Eventos de SP, SVSP e contingência para outras UFs) inserem um registro na tabela [CTe_Out].[SVD].[NSU], sendo que a coluna SNSU_PKEY é preenchida utilizando o valor 'next' do sequence [CTe_Out].[SVD].[SQ_NSU]. Dessa forma, nesse serviço de Distribuição CT-e o NSU gerado na resposta é o valor dessa coluna SNSU_PKEY.

A procedure [CTe_Out].[SVD].[USP_NSU_SEL] acessa também a tabela [CTe_Out].[CTeOut].[Protocolo] para retornar os dados, além das tabelas específicas para cada tipo de documento de transporte para  (por exemplo, no GTC-e acessa [CTe_Out].[GTVe].[GTVe] e [CTe_Out_XML].[GTVe].[GTVeXml] ).

Outras colunas importantes da tabela [CTe_Out].[SVD].[NSU], lembrando que ela é populada na autorização por SP dos documentos de transporte:

  * NSU_UFS_COMPARTILHAR: tem a lista dos órgãos para os quais esse documento deverá ser compartilhado.
  * NSU_DADOS: já tem informações 'mastigadas' para a construção da resposta
  * NSU_PROTOCOLO: foreign-key para a pkey da tabela [CTe_Out].[CTeOut].[Protocolo]

O log da requisição gerado na tabela [SVD].[LOG_ENVIO] tem bastantes dados de performance, pois o RS já reclamou da performance desse web-service. Colunas relativas a isso:

  * LOE_TIME_QUERY representa o tempo em ms de acesso ao banco pela procedure [CTe_Out].[SVD].[USP_NSU_SEL]
  * LOE_TIME_VALIDATION representa o tempo em ms das validações A, B, D e M.
  * LOE_TIME_BUILDRETURN representa o tempo em ms para montar a resposta.

### Principais tabelas envolvidas
  * [CTe_Out].[SVD].[NSU]  -> informações de cada NSU a ser recuperado por esse serviço
  * [CTe_Out].[SVD].[LOG_ENVIO] -> log de cada requisição

[Script para acompanhamento (clique para abrir)](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/246/Script-Distribui%C3%A7%C3%A3o-CTe)

### Classes C# que implementam a lógica principal
  * CTeEnvioSvdBLL

### Exemplo de requisição e resposta
### Request:


```
<distCTeAut versao="1.00" xmlns="http://www.portalfiscal.inf.br/cte">
   <tpAmb>2</tpAmb>
   <ultNSU>000000000003300</ultNSU> MAIOR NSU RECEBIDO
</distCTeAut>
```


### Resposta:


```
<retDistCTeAut versao="1.00" xmlns="http://www.portalfiscal.inf.br/cte">
  <tpAmb>3</tpAmb>
  <verAplic>SP-CTe-2021-05-21-1</verAplic>
  <cStat>118</cStat>                     117 QUANDO NÃO EXISTE REGISTRO MAIOR QUE O NSU SOLICITADO
  <xMotivo>DF-e localizados</xMotivo>
  <ultNSU>000000000003350</ultNSU>       MAIOR NSU RETORNADO
  <ultNSUAut>000000000003509</ultNSUAut> MAIOR NSU EXISTENTE NA BASE
  <loteDist>
    <proc schema="procCTeOS_v3.00.xsd" NSU="000000000003301">
    <infOrgaoDist>
      <cOrgao>29</cOrgao>                LISTA DE ORGÃOS PARA ONDE SVD DEVE COMPARTILHAR
      <cOrgao>31</cOrgao>
    </infOrgaoDist>
    <CTe>
      <procComp>.....</procComp>         DOCUMENTO COMPLETO COMPACTADO GZIP
      <chCTe>35210446377222000129677320000026741000262233</chCTe>
      <tpCTe>0</tpCTe>
      <dhRecbto>2021-04-01T10:36:01-03:00</dhRecbto>
      <nProt>135210000560156</nProt>
    </CTe>
      .....
    </proc>
    <proc schema="procCTeOS_v3.00.xsd" NSU="000000000003302">
      .....
    </proc>
    .....
    <proc schema="procCTeOS_v3.00.xsd" NSU="000000000003350">
      .....
    </proc>
  <loteDist>
</retDistCTeAut>
```
