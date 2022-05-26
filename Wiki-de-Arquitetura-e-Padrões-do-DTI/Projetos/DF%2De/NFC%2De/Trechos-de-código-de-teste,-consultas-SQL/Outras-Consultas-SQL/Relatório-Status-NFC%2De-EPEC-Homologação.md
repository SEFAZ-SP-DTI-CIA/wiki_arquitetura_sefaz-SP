# Consultas SQL, trechos de código de teste, etc

### --1.1) em LST_NFCE01HML


```
--1.1.1) status "últimos 7 dias"

SET NOCOUNT ON​

SELECT GETDATE() as "Dados NFCe dos últimos 7 dias:"

select cnpjEmit as CNPJ, CH.Empresa, count(cnpjEmit) as "Notas Autorizadas nos últimos 7 dias"
  from [NFCe_Out_Constraint].[NFCeOut].[NFCe_Constraint] CT with (nolock)
  inner join [NFCe_Out_Constraint].[NFCeOut].[ContribuintesHabilitados] CH with (nolock)
  on CT.CNPJEmit = CH.CNPJ
  where cStat = '100'
  and CT.cnpjEmit not in ('91964743000137','74554064000170','08173349000137','74554064000251','01215911000107','12345678901234​')
  and CT.timestampReg > DATEADD(DAY,-7,GETDATE())
  group by cnpjEmit, CH.Empresa
  order by 3 desc

SELECT SUBSTRING(nfce.chNFCe,7,14) as CNPJ, empresas.Empresa,
sum(nfce.indSinc) as "#Sinc",
sum(case when ISNULL(nfce.indSinc,0)=0 then 1 else 0 end) as "#Assinc",
(case when sum(nfce.indSinc)=count(1) then 100 else 100 * sum(nfce.indSinc)/count(1) end) as "% Síncronas nos últimos 7 dias"
  FROM [NFCe_Out].[NFCeOut].[NFCe] nfce
  JOIN [NFCe_Out_Constraint].[NFCeOut].[ContribuintesHabilitados] empresas
  ON empresas.CNPJ = SUBSTRING(nfce.chNFCe,7,14)
  WHERE nfce.timestampReg > DATEADD(DAY,-7,GETDATE())
  and SUBSTRING(nfce.chNFCe,7,14) not in ('91964743000137','74554064000170','08173349000137','74554064000251','01215911000107','12345678901234​','11111111111111')
  group by SUBSTRING(nfce.chNFCe,7,14), empresas.Empresa
  order by 3 desc
 
 

select cnpjEmit as CNPJ, CH.Empresa, count(cnpjEmit) as "Notas Inutilizadas nos últimos 7 dias"
  from [NFCe_Out_Constraint].[NFCeOut].[NFCe_Constraint] CT with (nolock)
  inner join [NFCe_Out_Constraint].[NFCeOut].[ContribuintesHabilitados] CH with (nolock)
  on CT.CNPJEmit = CH.CNPJ
  where cStat = '102'
  and ct.cnpjEmit not in ('91964743000137','74554064000170','08173349000137','74554064000251','01215911000107','12345678901234​')
  and CT.timestampReg > DATEADD(DAY,-7,GETDATE())
  group by cnpjEmit, CH.Empresa
  order by 3 desc
  
select SUBSTRING(ET.[chNFCe],7,14) AS CNPJ, CH.Empresa, count(SUBSTRING(ET.[chNFCe],7,14)) as "Notas canceladas nos últimos 7 dias"
  from [NFCe_Out_Constraint].[NFCeOut].[Evento_Constraint] ET with (nolock)
  inner join [NFCe_Out_Constraint].[NFCeOut].[ContribuintesHabilitados] CH with (nolock)
  on SUBSTRING(ET.[chNFCe],7,14) = CH.CNPJ
  where ET.tpEvento = '110111'
  and SUBSTRING(ET.[chNFCe],7,14) not in ('91964743000137','74554064000170','08173349000137','74554064000251','01215911000107','12345678901234​')
  and ET.timestampReg > DATEADD(DAY,-7,GETDATE())
  group by SUBSTRING(ET.[chNFCe],7,14), CH.Empresa
  order by 3 desc
  
SELECT contribuinte.Empresa,count(1) as "Tokens QRCode criados Nos últimos 7 Dias"
  FROM [NFCe_Out].[NFCeOut].[TokenContribuinte] token
  JOIN [NFCe_Out_Constraint].[NFCeOut].[ContribuintesHabilitados] contribuinte
  ON token.CNPJBase = SUBSTRING(contribuinte.CNPJ,1,8)
  WHERE contribuinte.CNPJ not in ('91964743000137','74554064000170','08173349000137','74554064000251','01215911000107','12345678901234​')
  AND token.timestampReg > DATEADD(DAY,-7,GETDATE())
  GROUP BY contribuinte.Empresa​

SELECT 
    AVG(l.[tempoRecebimento]+lp.[tempoProcessamento]) as "Tempo de autorização (média em ms)",
	MIN(l.[tempoRecebimento]+lp.[tempoProcessamento]) as "(Mínimo)",
	MAX(l.[tempoRecebimento]+lp.[tempoProcessamento]) as "(Máximo)" 
FROM [NFCe_In].[NFCeIn].[Lote] l with (nolock)
JOIN [NFCe_In].[NFCeIn].[LoteProcessamento] lp with (nolock)
ON l.pKey = lp.pKey
AND l.recebimento = lp.recebimento
and lp.recebimento > DATEADD(DAY,-7,GETDATE())
WHERE l.CNPJ not in ('91964743000137','74554064000170','08173349000137','74554064000251','01215911000107','12345678901234​')
 and l.tempoRecebimento <> GETANSINULL() and lp.tempoProcessamento <> GETANSINULL()
 
SELECT COUNT(1) as "#Lotes processados em mais de 3 segundos:"
FROM [NFCe_In].[NFCeIn].[Lote] l with (nolock)
JOIN [NFCe_In].[NFCeIn].[LoteProcessamento] lp with (nolock)
ON l.pKey = lp.pKey
AND l.recebimento = lp.recebimento
WHERE (l.[tempoRecebimento]+lp.[tempoProcessamento]) > 3000
and l.CNPJ not in ('91964743000137','74554064000170','08173349000137','74554064000251','01215911000107','12345678901234​')
and l.recebimento > DATEADD(DAY,-7,GETDATE())
 
SELECT COUNT(1) as "#Lotes processados em menos de 3 segundos:"
FROM [NFCe_In].[NFCeIn].[Lote] l with (nolock)
JOIN [NFCe_In].[NFCeIn].[LoteProcessamento] lp with (nolock)
ON l.pKey = lp.pKey
AND l.recebimento = lp.recebimento
WHERE (l.[tempoRecebimento]+lp.[tempoProcessamento]) <= 3000
and l.CNPJ not in ('91964743000137','74554064000170','08173349000137','74554064000251','01215911000107','12345678901234​')
and l.recebimento > DATEADD(DAY,-7,GETDATE())

--###############################################################################################
--EPEC

select nc.cnpjEmit as CNPJ, ch.Empresa, count(1) as "#Notas conciliando EPECs nos últimos 7 dias"
from [NFCe_Out_Constraint].[NFCeOut].[NFCe_Constraint] nc (nolock)
join [NFCe_Out_Constraint].[NFCeOut].[ContribuintesHabilitados] ch with (nolock)
  on nc.cnpjEmit = ch.CNPJ
where nc.tpEmis = 4
and nc.cStat = 100
and nc.cnpjEmit not in ('91964743000137','74554064000170','08173349000137','74554064000251','01215911000107','12345678901234​','11111111111111')
and nc.timestampReg > DATEADD(DAY,-7, GETDATE())
group by nc.cnpjEmit, ch.Empresa
order by 3 desc

--###############################################################################################​

--1.1.2) status "desde o início" 

select GETDATE() as "Dados NFCe desde o início da homologação"

select cnpjEmit as CNPJ, CH.Empresa, count(cnpjEmit) as "#Notas Autorizadas"
  from [NFCe_Out_Constraint].[NFCeOut].[NFCe_Constraint] CT with (nolock)
  inner join [NFCe_Out_Constraint].[NFCeOut].[ContribuintesHabilitados] CH with (nolock)
  on CT.CNPJEmit = CH.CNPJ
  where cStat = '100'
  and ct.cnpjEmit not in ('91964743000137','74554064000170','08173349000137','74554064000251','01215911000107','12345678901234​','11111111111111')
 
  group by cnpjEmit, CH.Empresa
  order by 3 desc

 
select DATEPART(MONTH,CT.timestampReg) as "Mês", DATEPART(YEAR,CT.timestampReg) as "Ano", count(CT.fkProtocolo) as "#Notas Autorizadas"
  from [NFCe_Out_Constraint].[NFCeOut].[NFCe_Constraint] CT with (nolock)
  where cStat = '100'
  and ct.cnpjEmit not in ('91964743000137','74554064000170','08173349000137','74554064000251','01215911000107','12345678901234​','11111111111111')
  group by DATEPART(MONTH,CT.timestampReg),DATEPART(YEAR,CT.timestampReg)
  order by 2,1

SELECT 
DATEPART(MONTH,nfce.timestampReg) as "Mês", 
DATEPART(YEAR,nfce.timestampReg) as "Ano", 
sum(ISNULL(nfce.indSinc,0)) as "#Sinc",
sum(case when ISNULL(nfce.indSinc,0)=0 then 1 else 0 end) as "#Assinc",
(case when sum(ISNULL(nfce.indSinc,0))=count(1) then 100 else 100 * sum(ISNULL(nfce.indSinc,0))/count(1) end) as "% Síncronas"
  FROM [NFCe_Out].[NFCeOut].[NFCe] nfce
  JOIN [NFCe_Out_Constraint].[NFCeOut].[ContribuintesHabilitados] empresas
  ON empresas.CNPJ = SUBSTRING(nfce.chNFCe,7,14)
  and SUBSTRING(nfce.chNFCe,7,14) not in ('91964743000137','74554064000170','08173349000137','74554064000251','01215911000107','12345678901234​','11111111111111')
  group by DATEPART(MONTH,nfce.timestampReg),DATEPART(YEAR,nfce.timestampReg)
  order by 2,1​​
 
  
select cnpjEmit as CNPJ, CH.Empresa, count(cnpjEmit) as "#Notas Inutilizadas"
  from [NFCe_Out_Constraint].[NFCeOut].[NFCe_Constraint] CT with (nolock)
  inner join [NFCe_Out_Constraint].[NFCeOut].[ContribuintesHabilitados] CH with (nolock)
  on CT.CNPJEmit = CH.CNPJ
  where cStat = '102'
  and ct.cnpjEmit not in ('91964743000137','74554064000170','08173349000137','74554064000251','01215911000107','12345678901234​','11111111111111')
 
  group by cnpjEmit, CH.Empresa
  order by 3 desc
  
select DATEPART(MONTH,CT.timestampReg) as "Mês", DATEPART(YEAR,CT.timestampReg) as "Ano", count(CT.fkProtocolo) as "#Notas Inutilizadas"
  from [NFCe_Out_Constraint].[NFCeOut].[NFCe_Constraint] CT with (nolock)
  inner join [NFCe_Out_Constraint].[NFCeOut].[ContribuintesHabilitados] CH with (nolock)
  on CT.CNPJEmit = CH.CNPJ
  where cStat = '102'
  and ct.cnpjEmit not in ('91964743000137','74554064000170','08173349000137','74554064000251','01215911000107','12345678901234​','11111111111111')
 
  group by DATEPART(MONTH,CT.timestampReg),DATEPART(YEAR,CT.timestampReg)
  order by 2,1


select SUBSTRING(ET.[chNFCe],7,14) AS CNPJ, CH.Empresa, count(SUBSTRING(ET.[chNFCe],7,14)) as "#Notas Canceladas"
  from [NFCe_Out_Constraint].[NFCeOut].[Evento_Constraint] ET with (nolock)
  inner join [NFCe_Out_Constraint].[NFCeOut].[ContribuintesHabilitados] CH with (nolock)
  on SUBSTRING(ET.[chNFCe],7,14) = CH.CNPJ
  where ET.tpEvento = '110111'
  and SUBSTRING(ET.[chNFCe],7,14) not in ('91964743000137','74554064000170','08173349000137','74554064000251','01215911000107','12345678901234​','11111111111111')
 
  group by SUBSTRING(ET.[chNFCe],7,14), CH.Empresa
  order by 3 desc

select DATEPART(MONTH,ET.timestampReg) as "Mês", DATEPART(YEAR,ET.timestampReg) as "Ano", count(ET.fkProtocolo) as "#Notas Canceladas"
  from [NFCe_Out_Constraint].[NFCeOut].[Evento_Constraint] ET with (nolock)
  inner join [NFCe_Out_Constraint].[NFCeOut].[ContribuintesHabilitados] CH with (nolock)
  on SUBSTRING(ET.[chNFCe],7,14) = CH.CNPJ
  where ET.tpEvento = '110111'
  and SUBSTRING(ET.[chNFCe],7,14) not in ('91964743000137','74554064000170','08173349000137','74554064000251','01215911000107','12345678901234​','11111111111111')
​  group by DATEPART(MONTH,ET.timestampReg),DATEPART(YEAR,ET.timestampReg)
  order by 2,1

 
 
SELECT contribuinte.Empresa,count(1) as "#Tokens QRCode Criados"
  FROM [NFCe_Out].[NFCeOut].[TokenContribuinte] token
  JOIN [NFCe_Out_Constraint].[NFCeOut].[ContribuintesHabilitados] contribuinte
  ON token.CNPJBase = SUBSTRING(contribuinte.CNPJ,1,8)
  WHERE contribuinte.CNPJ not in ('91964743000137','74554064000170','08173349000137','74554064000251','01215911000107','12345678901234​','11111111111111')
  GROUP BY contribuinte.Empresa​

SELECT DATEPART(MONTH,token.timestampReg) as "Mês", DATEPART(YEAR, token.timestampReg) as "Ano", count(1) as "#Tokens QRCode Criados"
  FROM [NFCe_Out].[NFCeOut].[TokenContribuinte] token
  JOIN [NFCe_Out_Constraint].[NFCeOut].[ContribuintesHabilitados] contribuinte
  ON token.CNPJBase = SUBSTRING(contribuinte.CNPJ,1,8)
  WHERE contribuinte.CNPJ not in ('91964743000137','74554064000170','08173349000137','74554064000251','01215911000107','12345678901234​','11111111111111')
  GROUP BY DATEPART(MONTH,token.timestampReg), DATEPART(YEAR, token.timestampReg)
  order by 2,1

select L.CNPJ as "Empresas NÃO habilitadas (CNPJ)", count(L.CNPJ) as "#Lotes Submetidos"
  from NFCe_In.NFCeIn.Lote L with (nolock)
  left join [NFCe_Out_Constraint].[NFCeOut].[ContribuintesHabilitados] CH with (nolock)
  on SUBSTRING(L.CNPJ,1,8) = SUBSTRING(CH.CNPJ,1,8)
  where CH.CNPJ is null
  and L.CNPJ not in ('91964743000137','74554064000170','08173349000137','74554064000251','01215911000107','12345678901234​','11111111111111')
  group by L.CNPJ, CH.Empresa
  order by 2 desc

select DATEPART(MONTH,L.recebimento) as "Mês", DATEPART(YEAR, L.recebimento) as "Ano", count(L.CNPJ) as "#Lotes de não cadastrados"
  from NFCe_In.NFCeIn.Lote L with (nolock)
  left join [NFCe_Out_Constraint].[NFCeOut].[ContribuintesHabilitados] CH with (nolock)
  on SUBSTRING(L.CNPJ,1,8) = SUBSTRING(CH.CNPJ,1,8)
  where CH.CNPJ is null
  and L.CNPJ not in ('91964743000137','74554064000170','08173349000137','74554064000251','01215911000107','12345678901234​','11111111111111')
  group by DATEPART(MONTH,L.recebimento), DATEPART(YEAR, L.recebimento)
  order by 2,1

select * from
(select CH.CNPJ,  CH.Empresa as 'Empresas credenciadas que nunca autorizaram nota'
 from [NFCe_Out_Constraint].[NFCeOut].[ContribuintesHabilitados] CH (nolock)
 where CH.CNPJ not in ('91964743000137','74554064000170','08173349000137','74554064000251','01215911000107','12345678901234?','11111111111111')
) as A
where A.CNPJ not in
(select N.cnpjEmit 
 from [NFCe_Out_Constraint].[NFCeOut].[NFCe_Constraint] N (NOLOCK) 
 where N.cnpjEmit not in ('91964743000137','74554064000170','08173349000137','74554064000251','01215911000107','12345678901234?','11111111111111')
 and N.cStat = 100
 group by N.cnpjEmit
 );​

select DATEPART(MONTH,c.dataDeHabilitacao) as "Mês", DATEPART(YEAR, c.dataDeHabilitacao) as "Ano", count(c.CNPJ) as "#Novos Contribuintes Cadastrados"
from NFCe_Out_Constraint.NFCeOut.ContribuintesHabilitados c
where c.CNPJ is not null
  and c.CNPJ not in ('91964743000137','74554064000170','08173349000137','74554064000251','01215911000107','12345678901234​','11111111111111')
   group by DATEPART(MONTH,c.dataDeHabilitacao), DATEPART(YEAR, c.dataDeHabilitacao)
  order by 2,1​

select c.CNPJ, c.Empresa as 'Empresa atualmente credenciada', c.dataCredenciamento as 'Data de credenciamento'
from [NFCe_Out_Constraint].[NFCeOut].[ContribuintesHabilitados] c (NOLOCK)
where tipoCredContribuinte in (1,2,6)
and c.CNPJ not in ('91964743000137','74554064000170','08173349000137','74554064000251','01215911000107','12345678901234​','11111111111111');

select c.CNPJ, c.Empresa as 'Empresa DE TESTE atualmente credenciada', c.dataCredenciamento as 'Data de credenciamento'
from [NFCe_Out_Constraint].[NFCeOut].[ContribuintesHabilitados] c (NOLOCK)
where tipoCredContribuinte in (1,2,6)
and c.CNPJ in ('91964743000137','74554064000170','08173349000137','74554064000251','01215911000107','12345678901234​','11111111111111');​

--#################################################################################################
--EPEC

select nc.cnpjEmit as CNPJ, ch.Empresa, count(1) as "#Notas conciliando EPECs (ambos autorizados)"
from [NFCe_Out_Constraint].[NFCeOut].[NFCe_Constraint] nc (nolock)
join [NFCe_Out_Constraint].[NFCeOut].[ContribuintesHabilitados] ch with (nolock)
  on nc.cnpjEmit = ch.CNPJ
where nc.tpEmis = 4
and nc.cStat = 100
and nc.cnpjEmit not in ('91964743000137','74554064000170','08173349000137','74554064000251','01215911000107','12345678901234​','11111111111111')
and nc.fkProtocolo <> 3242580
group by nc.cnpjEmit, ch.Empresa
order by 3 desc

select DATEPART(MONTH,CT.timestampReg) as "Mês", DATEPART(YEAR,CT.timestampReg) as "Ano", count(CT.fkProtocolo) as "#Notas Conciliando EPECs (ambos autorizados)"
  from [NFCe_Out_Constraint].[NFCeOut].[NFCe_Constraint] CT with (nolock)
  where tpEmis = 4
  and cStat = '100'
  and ct.cnpjEmit not in ('91964743000137','74554064000170','08173349000137','74554064000251','01215911000107','12345678901234​','11111111111111')
  and ct.fkProtocolo <> 3242580​
  group by DATEPART(MONTH,CT.timestampReg),DATEPART(YEAR,CT.timestampReg)
  order by 2,1
--#################################################################################################


SET NOCOUNT OFF






















--1.2) em LST_EPEC01HML
--1.2.1) status "desde o início" 

SET NOCOUNT ON​

SELECT GETDATE() as "Dados EPEC desde o inicio:"

CREATE TABLE #Result1(
    CNPJ char(14) NOT NULL,
    Empresa nvarchar(60) NULL
);

INSERT INTO #Result1 VALUES
 ('08173349000137',N'FastBooks'),
 ('74554064000170',N'EMPRESA COM REQUISICAO DE SUCESSO'),
 ('91964743000137',N'DEPOSITO EUCLIDES DA CUNHAA'),
 ('00623904000173',N'Apple Computer Brasil Ltda'),
 ('00608804000178',N'Lazarim & Travaglia Ltda'),
 ('68274596000151',N'PAMPULHA PÃES E DOCES LTDA'),
 ('45543915047145',N'Carrefour Comercio e Industria Ltda'),
 ('48769483000977',N'DL ILUMINACAO LT'),
 ('18587852000192',N'RK CAFETERIA EIRELI ME'),
 ('49732175004684',N'ESTOK COMERCIO E REPRESENTACOES S/A'),
 ('47427653001430',N'MAKRO ATACADISTA S.A.'),
 ('06158643000144',N'JOSE FERREIRA GANDRA DE CARVALHO - ME'),
 ('19221410000190',N'D&M Alimentos Rio Preto Ltda - ME'),
 ('03112800000192',N'Soares & Tenani Comércio e Serviços de Informática Ltda'),
 ('33014556086481',N'LOJAS AMERICANAS S.A.'),
 ('60957784000334',N'DELLA VIA PNEUS LTDA'),
 ('60957784002973',N'DELLA VIA PNEUS LTDA'),
 ('05437644000165',N'Aline do Valle Guimarães Barros-ME'),
 ('49732175000182',N'ESTOK COMERCIO E REPRESENTACOES S.A.'),
 ('57513434000102',N'A.A AFONSO E CIA LTDA'),
 ('61149506004220',N'TINTAS MC LTDA'),
 ('54517628001160',N'LINX SISTEMAS E CONSULTORIA LTDA'),
 ('93015006003481',N'COMPANHIA ZAFFARI COMERCIO E INDUSTRIA'),
 ('04176054000163',N'M. FERNANDO DA COSTA SJ DO RIO PRETO ME'),
 ('05116911000100',N'CENELLAR COM DE MAT MED HOSP LTDA ME'),
 ('11521659000100',N'Horti Frutti Corujas Ltda'),
 ('19374765000110',N'Tec Representações e Gestão de Varejo LTDA'),
 ('12536521000147',N'IT DECOR BRAZIL COMERCIO, IMPORTAÇÃO E EXPORTAÇÃO LTDA-ME'),
 ('47513387000143',N'Celso Coelho De Miranda - Me'),
 ('03343434000182',N'Fernando Barreira da S. Telemática'),
 ('69043156000237',N'DORIVAL JOSE FERREIRA – ME'),
 ('19833882000102',N'Ribeirão Vet'),
 ('05284442000120',N' MAURICIO MITSUO MIDORIKAWA INFORMATICA ME'),
 ('65698235000144',N' Vip Systems Informática & Consultoria Ltda'),
 ('06159294000185',N'D.O.Brasil Indústria e Comércio de Componentes Eletrônicos'),
 ('05800256000601',N'ROLDAO AUTO SERV COM DE ALIMENTOS LTDA'),
 ('09062049000143',N'GENESE TECNOLOGIA LTDA - ME'),
 ('04783180000186',N'M.G.DE O.LEDO ME'),
 ('14228622000186',N'Dani Banani LTDA ME'),
 ('01215911000107',N'SILVANA CASSIA M DE ASSUNCAO - ME'),
 ('08575309000111',N'DP MOLINA INFORMATICA LTDA - ME'),
 ('20990783000105',N'BELLA DONNA PERFUMARIA E COSMETICOS LTDA. - ME'),
 ('07587030000195',N'GUILHERME JUNIO VOLPI - ME'),
 ('54394630001350',N'SBS - SPECIAL BOOK SERVICES LIVRARIA E EDITORA LTDA'),
 ('11953617000148',N'SIN YOUNG CHO SISTEMAS'),
 ('15582319000140',N'VNX TECNOLOGIA EM INFORMATICA - EIRELI - EPP'),
 ('96418892000114',N'ZAP MATERIAIS PARA CONSTRUCAO LTDA - EPP'),
 ('18018441000186',N'CASA NOSSA INTERIORES LTDA-ME - ME'),
 ('18830742000100',N'PAULO FERNANDO DORING RODRIGUES'),
 ('02500762000182',N'MARIO CESAR FIOROT - ME'),
 ('10599315000150',N'ROGERIO BUENO MARTINS - ME'),
 ('20786867000121',N'MACEDO E BOSIO PRODUTOS NATURAIS LTDA - ME'),
 ('64826373000107',N'TASSARA INFORMATICA LTDA - ME'),
 ('15046321000102',N'MARCELO GUSTAVO FAZAN 22403734829'),
 ('39004353000103',N'PLUSTEC COMERCIO E SERVICOS TECNICOS LTDA - EPP'),
 ('56642960000100',N'LOJAS CEM S/A'),
 ('42274696007016',N'adidas do Brasil Ltda'),
 ('00623904000688',N'APPLE COMPUTER BRASIL LT'),
 ('56642960000371',N'LOJAS CEM S/A'),
 ('47960950000121',N'MAGAZINE LUIZA S/A'),
 ('46123758000118',N'AUTO SHOPP PAULISTA LT'),
 ('72980451000143',N'AUTO POSTO F-1000 LT'),
 ('18715523000180',N'ELLOCOM COMERCIO DE INFORMATICA LTDA - EPP'),
 ('02030078000184',N'EXPERT - ASSESSORIA E TREINAMENTO EM INFORMATICA LTDA - ME'),
 ('58170507000173',N'REGISTRADORAS BRASILEIRAS LTDA - EPP');
 
select cnpjEmit as CNPJ, CH.Empresa, count(cnpjEmit) as "#EPECs Autorizados"
  from [NFCe_EPEC_Constraint].[EPEC].[EPEC_Constraint] CT with (nolock)
  inner join #Result1 CH with (nolock)
  on CT.CNPJEmit = CH.CNPJ
  where cStat = '136'
  and ct.cnpjEmit not in ('91964743000137','74554064000170','08173349000137','74554064000251','01215911000107','12345678901234​','11111111111111')
  group by cnpjEmit, CH.Empresa
  order by 3 desc

select DATEPART(MONTH,CT.timestampReg) as "Mês", DATEPART(YEAR,CT.timestampReg) as "Ano", count(CT.pKey) as "#EPECs Autorizados"
  from [NFCe_EPEC_Constraint].[EPEC].[EPEC_Constraint] CT with (nolock)
  where cStat = '136'
  and ct.cnpjEmit not in ('91964743000137','74554064000170','08173349000137','74554064000251','01215911000107','12345678901234​','11111111111111')
  group by DATEPART(MONTH,CT.timestampReg),DATEPART(YEAR,CT.timestampReg)
  order by 2,1
​

SELECT r.Empresa as "Empresa Bloqueada", r.CNPJ, count(p.pKey) as "#EPECs Pendentes", FORMAT(DATEADD(DAY,8,MIN(c.timestampReg)),'d','pt-br') as "Bloqueada desde:"​
  FROM [NFCe_EPEC_Dados].[EPEC].[ConciliacaoPendente] p (NOLOCK)
  JOIN [NFCe_EPEC_Constraint].[EPEC].[EPEC_Constraint] c (NOLOCK)
  ON c.pKey = p.pKey
  JOIN [NFCe_EPEC_Constraint].[EPEC].[ContribuintesBloqueados] b
  ON c.cnpjEmit = b.cnpj
  AND c.cnpjEmit not in ('91964743000137','74554064000170','08173349000137','74554064000251','01215911000107','12345678901234​','11111111111111')
  JOIN #Result1 r
  ON r.CNPJ = c.cnpjEmit
  GROUP BY r.Empresa, r.CNPJ

​
--1.2.2) status "últimos 7 dias" 

SELECT GETDATE() as "Dados EPEC dos últimos 7 dias:"
 

select cnpjEmit as CNPJ, CH.Empresa, count(cnpjEmit) as "#EPECs Autorizados nos últimos 7 dias"
  from [NFCe_EPEC_Constraint].[EPEC].[EPEC_Constraint] CT with (nolock)
  inner join #Result1 CH with (nolock)
  on CT.CNPJEmit = CH.CNPJ
  where cStat = '136'
  and ct.cnpjEmit not in ('91964743000137','74554064000170','08173349000137','74554064000251','01215911000107','12345678901234​','11111111111111')
  and CT.timestampReg > DATEADD(DAY,-7,GETDATE())
  group by cnpjEmit, CH.Empresa
  order by 3 desc

DROP TABLE #Result1​

SET NOCOUNT OFF​
```

