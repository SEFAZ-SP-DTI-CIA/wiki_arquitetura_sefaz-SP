## Módulos do SVD
### 

## (Usar o índice lateral para navegar nas sub-páginas, pois atendem as opções de 1 a 5 abaixo)

###
[1- Recepção SVD](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/237/Recep%C3%A7%C3%A3o-SVD)

[2- Reprocessamento Recepção SVD](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/238/Reprocessamento-Recep%C3%A7%C3%A3o-SVD)

[3- NSU Faltantes Recepção SVD](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/243/NSU-Faltantes-Recep%C3%A7%C3%A3o-SVD)


[4- Distribuição CT-e / CT-e Envio](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/245/Distribui%C3%A7%C3%A3o-CT-e-CT-e-Envio)


[5- Gerar Eventos Fisco SVD](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/248/Gerar-Eventos-Fisco-SVD)

Modelo dos documentos de transporte (enum CTeModelo):

 * CT-e    57
 * GTV-e   64
 * CT-e OS 67

Tipos de registro de documentos de transporte para SVD (enum CTeSvdRegisterType):

 * CTE          = 1,
 * CTE_OS       = 2,
 * INUTILIZACAO = 3,
 * EVENTO       = 4,
 * GTVE         = 5

Tipos de serviço de documentos de transporte:

 * Normal                  = 0
 * Subcontratacao          = 1
 * Redespacho              = 2
 * RedespachoIntermediário = 3
 * VinculadoAMultimodal    = 4
 * TransportedePessoas     = 6
 * TransportedeValores     = 7
 * ExcessodeBagagem        = 8
 * GTV-e                   = 9

Tipo de Emissão (tpEmis) - presente na chave

 * Normal                  = 1
 * EPEC pela SVC           = 4
 * Contingência FSDA       = 5
 * Autorização pela SVC-RS = 7
 * Autorização pela SVC-SP = 8

Tipo de Autorizador - presente no protocolo

 * SEFAZ normal     = 1
 * SEFAZ VIRTUAL-RS = 3
 * SEFAZ VIRTUAL-SP = 5  (quando SP autoriza normalmente para PE, RR, AP)
 * SVC-RS           = 7
 * SVC-SP           = 8