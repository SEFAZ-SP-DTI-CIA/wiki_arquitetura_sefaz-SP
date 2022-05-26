##(Usar o índice lateral para navegar nos tópicos)

## Requisitos
 * Ver Manual de Orientação do Contribuinte disponibilizado pela ENCAT (em [Links Úteis](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/336/Links-%C3%9Ateis)); atualmente apenas o manual contemplando a NF-e (versão 5) está disponível, sendo as alterações específicas à NFC-e disponibilizadas em Notas Técnicas (também em [Links Úteis](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/336/Links-%C3%9Ateis)).
 
## Serviços suportados e validações feitas em cada bloco


| Link detalhes de cada Serviço | Blocos de Validações de Lote | Blocos de Validações de NF |
|--|--|--|
| [Autorização](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/337/Servi%C3%A7o-Autoriza%C3%A7%C3%A3o-NFCe) (assíncrona) | A,B,GAP |  |
| [Autorização](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/337/Servi%C3%A7o-Autoriza%C3%A7%C3%A3o-NFCe) (síncrona) | A,B,GAP | ​​E,F,G​​ |
| [RetornoAutorização](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/338/Retorno-Autoriza%C3%A7%C3%A3o) | A,B,C,D,E2 |  |
| [Consulta](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/339/Servi%C3%A7o-Consulta)  | ​A,B,C,D  | J |
| [Inutilização](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/340/Servi%C3%A7o-Inutiliza%C3%A7%C3%A3o)  | A,B,C,D,E,F,I  |   |
| [StatusServiço](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/342/Status-Servi%C3%A7o)  | ​A,B,C,D,K  |   | 
| [RecepçãoEvento (Cancelamento)](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/344/Recep%C3%A7%C3%A3o-Evento-(Cancelamento))  | ​A,B,C,D1  | ​D2eventos,Eeventos,Feventos,Geventos,GAeventos  |
| [WindowsService (Consumidor)](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/345/WindowsService-(Consumidor))  | ​C,D,G_Lote  | ​E,F,G  |​
​
 
## Banco de Dados

 * [Stored Procedures NFC-e](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/346/Stored-Procedures-NFC-e)

 * http://portaltfs.intra.fazenda.sp.gov.br/sites/TPC-2010/NFC-e/Shared%20Documents/General/TabelasNFCe.docx

 * [Comandos SQL](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/347/Comandos-SQL)

## Cache de LCR em BD

 * [Documentação Do Cache LCR da NFC-e](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/348/Documenta%C3%A7%C3%A3o-Do-Cache-LCR-da-NFC-e)

## EPEC
 * [Modelo de Dados e Desenho EPEC](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/349/Modelo-de-Dados-e-Desenho-EPEC)
 

## Extrator DW
 * [​Documentação Do Extrator DW NFC-e](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/350/Documenta%C3%A7%C3%A3o-Do-Extrator-DW-NFC-e)

 * http://portaltfs.intra.fazenda.sp.gov.br/sites/TPC-2010/NFC-e/Shared%20Documents/General/DW/SEFAZ_NFCE_DW.docx
 

## Contadores de Performance

 * http://portaltfs.intra.fazenda.sp.gov.br/sites/TPC-2010/NFC-e/Shared%20Documents/General/ContadoresDePerformance.pptx
 

## Features
 * [Documentação da funcionalidade de Features NFC-e, incluindo o mapa de bits](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/352/Features-NFC-e)

## Listas
 * [Sobre as listas de códigos de municípios, países, NCM, ANP, etc](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/353/Sobre-Listas-NFCe)


Compartilhamento
* http://etc.intra.fazenda.sp.gov.br/sites/cds_equipe/Documentos%20Compartilhados/NFC-e/CompartilhamentoAN/Manual_Compartilhamento_NFe_v2.02j.pdf

* Clique no link ->  [PL_CompNFe_202.zip](/.attachments/PL_CompNFe_202-d36044f0-bbe0-4532-9ee2-355ea229fbd1.zip)​  para efetuar download do arquivo. Será necessário descompactar, para ter acesso aos arquivos de _schema._
​
## Jobs
 * [Descrição dos jobs da NFC-e](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/354/Jobs-NFCe)