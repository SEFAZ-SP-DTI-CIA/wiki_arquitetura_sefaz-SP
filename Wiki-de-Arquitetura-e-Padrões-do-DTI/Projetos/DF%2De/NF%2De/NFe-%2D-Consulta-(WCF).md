## Serviços Corindus

São WCF e WebServices que ficam disponíveis para serem consumidos em projetos (de outros times) que precisam utilizar dados da NF-e.  

### Ambientes e servidores hospedados

Os WebServices e os WCFs estão hospedados em servidores diferentes, tanto no ambiente de homologação como o ambiente de Produção.

Abaixo segue o Host e o nome de cada servidor que compoem o mesmo:

_____________________________________________________

###WebServices​
_____________________________________________________

###Homologação:
**Host:** URL: (https://homologacao.nfe.fazenda.sp.gov.br)​

**Servidores:**   
SRV10642
SRV10643

**Produção:**
**Host:** URL: (https://nfe.fazenda.sp.gov.br)​

**Servidores:** 
SRV10814
SRV10815
SRV10816
SRV10817
SRV10818
SRV10819
SRV11079
SRV11080
_____________________________________________________
​
### WCFs
_____________________________________________________

###Homologação:
**Host:** URL: (https://wcf.nfe.intra.fazenda.sp.gov.br)

**Servidores:**   
SRV11199
SRV11200

**Produção:**
**Host:** URL: (https://nfe.fazenda.sp.gov.br)​

**Servidores:** 
SRV11201
SRV11202
_____________________________________________________

## Credito Acumulado (WebService)
 
 * WebMethod _ConsultarLoteSp_, realiza chamada para o método _ConsultarCredAcumSP_ na classe NfeDal passando como argumentos uma lista de DadosNFeEntity, limitado em cinquenta registros. Este por sua vez faz uma consulta ao banco de dados _NFe_Out_ utilizando-se da procedure _NFeOut.usp_CreditoAcumulado_ConsultaNFeEmLote_ que insere os dados vindos do parâmetro de entrada na tabela #NFes, após é feita uma atualização, a partir do relacionamento da tabela NFeOut.NFe_Constraint, nos campos pKey, idNFe, status e timestampReg. Com o resultado, verifica se existe alguma linha com a informação igual a 100 no campo status. Caso tenha alguma tupla que contenha a informação é feita uma nova atualização do campo status de 100 (autorizado) para 101 ( cancelado). Ao final tem se o resultset das tabelas #NFes e [DFe_XML].[NFe].[NFeXml].​
​
 * WebMethod _ConsultarLoteIdNFeSp_, realiza chama para método _ConsultarCredAcumSP_ na classe NfeDal passando como argumentos uma lista de strings, limitado em cinquenta registros, contendo números de notas fiscais. Este por sua vez faz uma consulta ao banco de dados _NFe_Out_ utilizando-se da procedure _NFeOut.usp_CreditoAcumulado_ConsultaNFeEmLote_ que insere os dados vindos do parâmetro de entrada na tabela temporária #NFes, após é feita uma atualização, a partir do relacionamento da tabela NFeOut.NFe_Constraint, nos campos pKey, idNFe, status e timestampReg. Com o resultado, verifica se existe alguma linha com a informação igual a 100 no campo status. Caso tenha alguma tupla que contenha a informação é feita uma nova atualização do campo status de 100 (autorizado) para 101 ( cancelado). Ao final tem se o resultset das tabelas #NFes e [DFe_XML].[NFe].[NFeXml].

 * WebMethod _ConsultarLoteUfs_, realiza chama para o método _ConsultarCredAcumUFs_ na classe NfeDal passando como argumentos uma lista de DadosNFeEntity, limitado em cinquenta registros, contendo números de notas fiscais. Este por sua vez faz uma consulta ao banco de dados _NFe_Out_UFs_ utilizando-se da procedure _[NFeUFs].[usp_CreditoAcumulado_ConsultaNFeEmLote]_ que insere na tabela #NFes a lista de dados do tipo NFeEntity, atualiza as informações de NSU, IDNFE, TimeStampRecebimento a partir de um join da tabela NFeUFs.DFeInfoCompl; atualiza o campo status e TimestamReg de uma junção da tabela ProtocoloNFeLog; exclui as linhas que tenham o código diferente de 101 (canceladas) caso tenha o número de idNFe repetidos; Das informações restantes caso tenham algumas com a situação do tipo 100 (Autorizado), o campo status é atualizado para 101 desde que tenha correspondentes nas colunas tpEvento e timestampReg da tabela NfeEventos.Evento. Ao final tem se o resultset das tabelas temporária #NFes mais DFe_XML relacionadas pelos campos NSU e TimestampRecebimento.​​

| **Ambiente**                            | **URLs**                         |
|-----------------------------------------|----------------------------------|
| Homologação |https://homologacao.nfe.fazenda.sp.gov.br/nfews/creditoacumulado.asmx|
| Produção       | https://nfe.fazenda.sp.gov.br/nfews/creditoacumulado.asmx|​

_____________________________________________________

## Credito Acumulado (WCF)
 
 * WebMethod _ConsultarLoteSp_, realiza chamada para o método _ConsultarCredAcumSP_ na classe NfeDal passando como argumentos uma lista de DadosNFeEntity, limitado em cinquenta registros. Este por sua vez faz uma consulta ao banco de dados _NFe_Out_ utilizando-se da procedure _NFeOut.usp_CreditoAcumulado_ConsultaNFeEmLote_ que insere os dados vindos do parâmetro de entrada na tabela #NFes, após é feita uma atualização, a partir do relacionamento da tabela NFeOut.NFe_Constraint, nos campos pKey, idNFe, status e timestampReg. Com o resultado, verifica se existe alguma linha com a informação igual a 100 no campo status. Caso tenha alguma tupla que contenha a informação é feita uma nova atualização do campo status de 100 (autorizado) para 101 ( cancelado). Ao final tem se o resultset das tabelas #NFes e [DFe_XML].[NFe].[NFeXml].​
​
 * WebMethod _ConsultarLoteIdNFeSp_, realiza chama para método _ConsultarCredAcumSP_ na classe NfeDal passando como argumentos uma lista de strings, limitado em cinquenta registros, contendo números de notas fiscais. Este por sua vez faz uma consulta ao banco de dados _NFe_Out_ utilizando-se da procedure _NFeOut.usp_CreditoAcumulado_ConsultaNFeEmLote_ que insere os dados vindos do parâmetro de entrada na tabela temporária #NFes, após é feita uma atualização, a partir do relacionamento da tabela NFeOut.NFe_Constraint, nos campos pKey, idNFe, status e timestampReg. Com o resultado, verifica se existe alguma linha com a informação igual a 100 no campo status. Caso tenha alguma tupla que contenha a informação é feita uma nova atualização do campo status de 100 (autorizado) para 101 ( cancelado). Ao final tem se o resultset das tabelas #NFes e [DFe_XML].[NFe].[NFeXml].

 * WebMethod _ConsultarLoteUfs_, realiza chama para o método _ConsultarCredAcumUFs_ na classe NfeDal passando como argumentos uma lista de DadosNFeEntity, limitado em cinquenta registros, contendo números de notas fiscais. Este por sua vez faz uma consulta ao banco de dados _NFe_Out_UFs_ utilizando-se da procedure _[NFeUFs].[usp_CreditoAcumulado_ConsultaNFeEmLote]_ que insere na tabela #NFes a lista de dados do tipo NFeEntity, atualiza as informações de NSU, IDNFE, TimeStampRecebimento a partir de um join da tabela NFeUFs.DFeInfoCompl; atualiza o campo status e TimestamReg de uma junção da tabela ProtocoloNFeLog; exclui as linhas que tenham o código diferente de 101 (canceladas) caso tenha o número de idNFe repetidos; Das informações restantes caso tenham algumas com a situação do tipo 100 (Autorizado), o campo status é atualizado para 101 desde que tenha correspondentes nas colunas tpEvento e timestampReg da tabela NfeEventos.Evento. Ao final tem se o resultset das tabelas temporária #NFes mais DFe_XML relacionadas pelos campos NSU e TimestampRecebimento.​​

| **Ambiente**                            | **URLs**                         |
|-----------------------------------------|----------------------------------|
| Homologação |https://wcfhml.nfe.intra.fazenda.sp.gov.br/services/CreditoAcumulado.svc​|
| Produção | https://wcf.nfe.intra.fazenda.sp.gov.br/services/CreditoAcumulado.svc|​

_____________________________________________________​​

## ConsultaAN (WCF)
 * Método _ConsultarNFe_ recebe como parâmetro um request do tipo ConsultarAnRequest e chama o método ConsultaANController.ConsultarNFe passando como parâmetros o idDFe e o tipo de ambiente. Neste método é feita a validação do tipo de ambiente, HOMOLOGACAO ou PRODUÇÃO. Após validado é criado o objeto NFeConsultaLogRequestVo, que recebe os valores: Cstat, VerAplic, Xmotivo. Este por sua vez serve de parâmetro para o método NFeConsultaLogBll().ConstruirMsgRetorno que retorna um objeto do tipo XmlDocument.​

 * Método _ConsultarCTe_ recebe como parâmetro um request do tipo ConsultarAnRequest e chama o método ConsultaANController.ConsultarNFe passando como parâmetros o idDFe e o tipo de ambiente. Neste método é feita a validação do tipo de ambiente, HOMOLOGACAO ou PRODUÇÃO. Após validado é criado o objeto CTeConsultaLogRequestVo, que recebe os valores: Cstat, VerAplic, Xmotivo. Este por sua vez serve de parâmetro para o método NFeConsultaLogBll().ConstruirMsgRetorno que retorna um objeto do tipo XmlDocument.​

| **Ambiente**                            | **URLs**                         |
|-----------------------------------------|----------------------------------|
| Homologação |https://wcfhml.nfe.intra.fazenda.sp.gov.br/services/ConsultaAN.svc​||
| Produção | ​https://wcf.nfe.intra.fazenda.sp.gov.br/services/ConsultaAN.svc|

_____________________________________________________

## ConsultaContribuinte (WCF)

 * ​Método _Consultar_ rec​ebe como parâmetro no request um CNPJ e o tipo de ambiente. Este por sua vez realiza a validação dos dados de entrada, tipo de ambiente e número de CNPJ. Uma vez validados são realizadas as seguintes consultas: ContribuinteDal. ConsultarObrigatoriedadeCTe que faz uma consulta no banco de dados _CTe_Out_, procedure _CTeAdmin.usp_ContribuinteSelectPorCnpjUf_ na qual realiza uma consulta nas tabelas [CTeAdmin].[Contribuinte], ContribuinteModal, ContribuinteVoluntario, ContribuinteLiminar, para verificar a obrigatoriedade da CTe; ContribuinteDal.ConsultarObrigatoriedadeNFe que consulta no banco de dados NFe_Out, procedure _ContribuintesHabilitadosSelect_ que retorna dados da tabela ContribuintesHabilitados. Essas informações são então inseridas em um objeto do tipo ContribuinteHabilitadoVo.​

| **Ambiente**                            | **URLs**                         |
|-----------------------------------------|----------------------------------|
| Homologação |https://wcfhml.nfe.intra.fazenda.sp.gov.br/services/ConsultaContribuinte.svc|
| Produção | https://wcf.nfe.intra.fazenda.sp.gov.br/services/ConsultaContribuinte.svc​|​

_____________________________________________________

## ConsultaCTe (WCF)
 * Método _Consultar_ recebe como parâmetro no request uma chave do CTe e o tipo de ambiente. Este por sua vez realiza a validação dos dados de entrada, tipo de ambiente e chave de acessoCTe. Estando validados os dados é feita uma consulta do número da chave CTe, CTeDal()). ConsultarCTe(), banco de dados _CTe_Out_, procedure _CTeOut.usp_CTeSelect_. O resultado dessa consulta contém um atributo Cstat que caso retorne os valores: 100 (Autorizado o uso da NF-e), 128 (verificar significado), 129 (verificar significado), 150 (verificar significado) o campo é atualizado para 129 (verificar significado). Se retornar 101 (Cancelamento de NF-e homologado) ou 151 (verificar significado) o campo é atualizado para 131 (verificar significado) em seguida é feita uma consulta na procedure _CTeOut.usp_ProtocoloLogSelectPorIdCTe_ que retorna informações sobre o evento de cancelamento. Retornando os valores: 301 (Uso Denegado: Irregularidade fiscal do emitente), 302 (Uso Denegado: Irregularidade fiscal do destinatário), 303 (verificar significado), 304 (verificar significado), 305 (verificar significado) e 306 (verificar significado) o campo é atualizado para 130 (verificar significado). Na sequência é feita a consulta sobre os eventos da CTe nas tabelas CTeEventos.Evento_Constraint, CTeEventos.Evento, CTeOut.Protocolo.​

| **Ambiente**                            | **URLs**                         |
|-----------------------------------------|----------------------------------|
| Homologação |​https://wcfhml.nfe.intra.fazenda.sp.gov.br/services/ConsultaCTe.svc​|
| Produção | ​https://wcf.nfe.intra.fazenda.sp.gov.br/services/ConsultaCTe.svc​|​

_____________________________________________________

## ConsultaNFe (WCF)
 * Método _Consultar_ recebe como parâmetro no request uma chave Da NFe mais o tipo de ambiente. Este por sua vez realiza a validação dos dados de entrada, tipo de ambiente e chave de acesso NFe. Estando validados os dados é feita uma consulta do número da chave NFe no banco _NFe_Out_, procedure _NFeOut.usp_ProtocoloXmlNFeSelectPorChNFe_ que faz uma consulta relacionada com as tabelas NFeOut.NFe_Constraint, NFeOut.Protocolo. O resultset contém um atributo Cstat que caso retorne os valores 100 (Autorizado o uso da NF-e) ou 150 (verificar significado) o campo é atualizado para 129 (verificar significado). Se retornar 101 (Cancelamento de NF-e homologado) ou 151 (verificar significado) o campo é atualizado para 131 (verificar significado).
 * Método _ConsultarLoteChaveNatural_ recebe o parâmetro request tipo ConsultaChaveNaturalRequest (chavesNFe e TbAmb). Esse método realiza a consulta na procedure _NFeOut.usp_ProtocoloXmlNFeSelectPorChaveNatural_ que retorna, limitado em até 50 registros, as informações das notas, a partir dos relacionamentos _NFeOut.NFe_Constraint E DFe_XML.NFe.NFeXml_.​​

| **Ambiente**                            | **URLs**                         |
|-----------------------------------------|----------------------------------|
| Homologação |​https://wcfhml.nfe.intra.fazenda.sp.gov.br/services/ConsultaNFe.svc|
| Produção | ​https://wcf.nfe.intra.fazenda.sp.gov.br/services/ConsultaNFe.svc|​

_____________________________________________________

## CreditoRural (WCF)

 * ​Método _ConsultarLoteUFs_ recebe uma lista de id de notas fiscais eletrônica a partir de uma consulta no banco NFe_Out_UFs procedure _NFeUFs.usp_CreditoAcumulado_ConsultaNFeEmLote_ que retorna, limitado em até 50 registros, as informações referente as notas fiscais, a partir dos relacionamentos das tabelas ProtocoloNFe, NFwUFs.DFeInfoCompl, ProtocoloNFeLog, DFe_XML e NFeEventos.Evento.
 * Método _ConsultarLote_ recebe uma lista de NFe a partir de uma consulta no banco de dados NFe_Out_UFsprocedure _NFeOut.usp_CreditoRural_ConsultaNFeEmLote_ que retorna, limitado em até 50 registros, as informações referente as notas fiscais, a partir dos relacionamentos das tabelas ProtocoloNFe, NFwUFs.DFeInfoCompl, ProtocoloNFeLog, DFe_XML e NFeEventos.Evento.​
 * Método _ConsultarAN_ recebe um número de nota fiscal. Faz a validação do certificado digital pelo método CarregarCertificado e após a validação caso o código cStat tenha valor igual a 129 ou diferente de null o retorno do método será um objeto do tipo NFeVO.​

| **Ambiente**                            | **URLs**                         |
|-----------------------------------------|----------------------------------|
| Homologação|https://wcfhml.nfe.intra.fazenda.sp.gov.br/services/CreditoRural.svc​|
| Produção | https://wcf.nfe.intra.fazenda.sp.gov.br/services/CreditoRural.svc​|​

_____________________________________________________

## DownloadEventoNFe (WCF)

* Método _DownloadEventos_ faz chamada para o método FilaEventosSistExtDal().ConsultarEventosCorindus. Este faz uma consulta no banco _NFe_Out_ na procedure _NFeEventos.usp_FilaEventosSistExtSelectCorindus_ retorna, limitado em até 50 registros, as informações sobre as tabelas NFeEventos.FilaEventosSistExt e [DFe_XML].NFe.EventoXml.​​

| **Ambiente**                            | **URLs**                         |
|-----------------------------------------|----------------------------------|
| Homologação|https://wcfhml.nfe.intra.fazenda.sp.gov.br/services/DownloadEventoNFe.svc|
| Produção|https://wcf.nfe.intra.fazenda.sp.gov.br/services/DownloadEventoNFe.svc|​

_____________________________________________________

## ConsultaContribuinte (WCF)​

Verifica se há dados do contribuinte nas bases do NFeOut.ContribuintesHabilitados e no CTeAdmin.Contribuinte pelo CNPJ do mesmo.​

​Método _Consultar_ um objeto ConsultaRequest com o CNPJ e o Tipo de ambiente efetuando consultas nas procedure _CTeAdmin.usp_ContribuinteSelectPorCnpjUf_ (base CTe_Out) e dbo.ContribuintesHabilitadosSelect (base NFe_Out) e verifica a data de credenciamento pela procedure _dbo.selCredenciamentoDataCredenciamento_.

Caso retorne algo, ele retornará a mensagem _"Consulta realizada com sucesso, foram encontrados registros"_. Caso contrário retorna _"Consulta realizada com sucesso, não foram encontrados registros"_.​

| **Ambiente**                            | **URLs**                         |
|-----------------------------------------|----------------------------------|
| Homologação|https://wcfhml.nfe.intra.fazenda.sp.gov.br/services/ConsultaContribuinte.svc​|
| Produção|https://wcf.nfe.intra.fazenda.sp.gov.br/services/ConsultaContribuinte.svc​|​

_____________________________________________________
​​
## Processo de autenticação e autorização para consultas na NF-e
Namespace: Sefaz.NFe.Lib.BLL.Validacao

A classe, BlocoA_BLL, possui método interno Validar que realiza o processo de validação e verificação descrito a seguir:

 * Verifica se o certificado raiz pertence à ICP-Brasil;​
 * Caso afirmativo recupera o número do CNPJ e o valida;
 * Após validação do CNPJ é realizada uma consulta na procedure [NFe_Out] .[dbo].[usp_OrgaoHabilitadoSelect] está retorna o órgão se o mesmo estiver ativo;
 * Se o retorno desta for diferente de null e feita mais uma consulta agora na procedures [NFeOut].[usp_ConfigSistemaSelect].​​

_____________________________________________________

## Endereço de outros serviços:

| **Ambiente**                            | **URLs**                         |
|-----------------------------------------|----------------------------------|
| Homologação|https://wcfhml.nfe.intra.fazenda.sp.gov.br/services/EventoFisco.svc​|
| Produção|https://wcf.nfe.intra.fazenda.sp.gov.br/services/EventoFisco.svc​|​

_____________________________________________________

# Configs do serviço WCF

Abaixo segue em anexo os configs dos serviços Corindus referente aos ambientes de Homologação e produção:

| **Ambiente**                            | **URLs**                         |
|-----------------------------------------|----------------------------------|
| Homologação|**Web_Config_corin​dus_hml.txt**|
| Produção|**Web_Config_corindus_prd.txt**|​
_____________________________________________________

Possíveis exceções nos serviços WCF

Segue em anexo arquivos com exceções que ocorreram e correções feitas:

[ExcessõesCorindus.docx](/.attachments/ExcessõesCorindus-41fad711-a299-48fe-b511-efef0d8f9569.docx)

_____________________________________________________

# Documentação para a facilitação de testes

Os documentos a baixo, possuem as referencias (endpoints) que devem sem inseridos na aplicação de testes conforme os ambientes desejados que podem ser o endereço do completo (balance) ou endereços indivíduais:

## Documentos Homologação:

[wcfhml - Balance.txt](http://etc.intra.fazenda.sp.gov.br/sites/cds_equipe/SiteAssets/Wiki%20CDS/DocumentacaoNFeCorindus/wcfhml%20-%20Balance.txt)
[wcfhml - SRV11199.txt](http://etc.intra.fazenda.sp.gov.br/sites/cds_equipe/SiteAssets/Wiki%20CDS/DocumentacaoNFeCorindus/wcfhml%20-%20SRV11199.txt)
[​wcfhml - SRV11200.txt](http://etc.intra.fazenda.sp.gov.br/sites/cds_equipe/SiteAssets/Wiki%20CDS/DocumentacaoNFeCorindus/wcfhml%20-%20SRV11200.txt)

## Documentos Produção

[wcf - Balance.txt](http://etc.intra.fazenda.sp.gov.br/sites/cds_equipe/SiteAssets/Wiki%20CDS/DocumentacaoNFeCorindus/wcf%20-%20Balance.txt)
[wcf - SRV11201.txt​](http://etc.intra.fazenda.sp.gov.br/sites/cds_equipe/SiteAssets/Wiki%20CDS/DocumentacaoNFeCorindus/wcf%20-%20SRV11201.txt)
[wcf - SRV11202.txt](http://etc.intra.fazenda.sp.gov.br/sites/cds_equipe/SiteAssets/Wiki%20CDS/DocumentacaoNFeCorindus/wcf%20-%20SRV11202.txt)