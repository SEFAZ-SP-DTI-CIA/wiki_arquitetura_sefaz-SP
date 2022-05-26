A grande quantidade de tipos de certificados digitais pode gerar confusão. Alguns tipos se referem ao local de armazenamento (ex: A3, A1, em nuvem) e outros ao conteúdo (ex: e-CPF, e-CNPJ). Sendo assim, neste artigo descrevemos os principais tipos.

# Certificado A3
Certificado com chaves criptográficas armazenadas em hardware criptográfico homologado junto à ICP-Brasil ou com certificação INMETRO e com validade máxima de 5 anos.

Os certificados em cartão (com validade máxima de 3 anos) utilizados aqui na SEFAZ são deste tipo.

# Certificado A1
Certificado com chaves criptográficas armazenadas em repositório protegido por senha e/ou identificação biométrica e com validade máxima de 1 ano (se for ICP-Brasil).

Arquivos .pfx ou .p12, que podem ser instalados em computadores, contêm certificados deste tipo.

# Certificado em Nuvem
Certificado com chaves criptográficas armazenadas por um Prestador de Serviço de Confiança (PSC) em dispositivo criptográfico Hardware Security Module (HSM).

O acesso a este tipo de certificado é feito por meio de interface disponibilizada pelo PSC conforme requisitos definidos pela ICP-Brasil. Essa interface não é compatível com os métodos de acesso aos certificados A1 e A3 tradicionais.

Alguns PSC também disponibilizam drivers e software para permitir a utilização de certificados em nuvem em aplicações que ainda não os suportam diretamente.

# e-CPF e e-CNPJ
Um e-CPF é simplesmente um certificado de pessoa física, assim como um e-CNPJ é um certificado de pessoa jurídica. Em ambos os casos isso somente indica qual o conteúdo do certificado, e não o local de armazenamento das chaves criptográficas.

Os certificados em cartão utilizados aqui na SEFAZ são e-CPFs, ou seja, certificados de pessoa física.

# Mais Informações
Há diversos outros tipos de certificados, tanto em relação ao conteúdo quanto em relação ao local de armazenamento das chaves criptográficas. Para obter as listas completas, ou para mais informações sobre os tipos acima, consulte a documentação disponibilizada pelo Instituto Nacional de Tecnologia da Informação (ITI) em:

https://www.gov.br/iti/pt-br/assuntos/legislacao/documentos-principais

A lista completa de tipos de certificados (ex: A3, A1) e os conteúdos dos mesmos (ex: e-CPF, e-CNPJ) estão no DOC-ICP-04.

A interface padrão para acesso aos certificados em nuvem está descrita no DOC-ICP-17.01.

Obs: Arquivos .cer ou .crt contêm a parte pública de qualquer tipo de certificado, mas não contêm chaves privadas e não têm senha.

<br/>

[Sefaz Identity](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/91/Sefaz-Identity)
