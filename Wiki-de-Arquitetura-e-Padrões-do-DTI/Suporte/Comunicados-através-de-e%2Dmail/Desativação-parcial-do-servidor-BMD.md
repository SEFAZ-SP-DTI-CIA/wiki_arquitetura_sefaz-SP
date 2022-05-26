   Informamos que o servidor BMD será parcialmente desativado em breve. Assim, solicitamos a todos os projetos que utilizem a BMD para armazenamento de artefatos de build que alterem seus pipelines para utilização de “Azure Pipelines”, de acordo com as orientações em nossa Wiki de Arquitetura e Padrões do DTI: 

https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/12/Configura%C3%A7%C3%A3o-das-tasks?anchor=publish-artifact
 
 
  Se precisar de auxílio para essa mudança, abra um chamado no Suporte Devops:

 

https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Suporte_DevOps            

 

 

_**Contexto:**_

 
  _O file share \\BMD\CDS tem sido utilizado para armazenamento dos arquivos (artefatos), necessários ao deploy das aplicações, gerados no ADS. O DTI planeja a desativação parcial desse share._

   _No ADS, ao se construir um pipeline de build, na task "Publish Artifacts", há duas opções para o campo "Artifact publish location":_

 

- Azure Pipelines (opção mais recomendada)
- File Share
 

 _Apesar de a primeira opção ser a mais recomendada, diversos projetos têm utilizado a segunda e apontado para \\BMD\CDS. Assim, como a geração de builds é muito grande, a ocupação da BMD ficou preocupante._
 

  _Conforme mencionado, as orientações para configuração de pipeline de build podem ser consultadas na Wiki de Arquitetura e Padrões do DTI:_


https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/12/Configura%C3%A7%C3%A3o-das-tasks?anchor=publish-artifact
 


 