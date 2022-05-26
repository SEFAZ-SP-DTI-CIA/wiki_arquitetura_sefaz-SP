# Análise Estática de Codigo (camada de segurança)

O teste de segurança de aplicativos estáticos (SAST - Static Application Security Testing) é um conjunto de técnicas projetadas para analisar o código-fonte do aplicativo para descobrir vulnerabilidades de segurança durante seu processo inicial de desenvolvimento. Essas técnicas são frequentemente usadas para identificar falhas antes do produto ir para produção. A ferramenta SAST fornece informações sobre vulnerabilidades no código e sugestões de correção para as equipes de desenvolvimento. 

O ideal é que o processo de análise estática seja incluído durante a própria fase de desenvolvimento de software, esta é uma fase poderosa no ciclo de vida do desenvolvimento para empregar esse processo, pois fornece feedback imediato ao desenvolvedor sobre os problemas que eles podem estar introduzindo no código durante o próprio desenvolvimento.

### Fortify

Fortify Static Code Analyzer (SCA), é uma solução de SAST que ajuda a proteger as organizações do maior risco de segurança atual, as aplicações que giram seus negócios, ajudando na revisão do código e ao desenvolvedor a identificar, priorizar, e resolver os problemas com menos esforço em menos tempo.

Para a utilização do fortify é necessário seguir aos passos informados nos procedimentos abaixo, primeiro deve-se criar um projeto na console de gerenciamento SSC, esse passo é importante para que depois de iniciar a análise do projeto seja possível o envio para a centrar de gerenciamento, depois é só seguir o passo de configuração do plugin no pipeline no ADS e atribuir as devidas configurações descritas no procedimento do ADS para que seja possível rodar a análise diretamente do agente build.

- [ ] [Criação de Aplicação na console SSC (Software Security Center)](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Arquitetura/Maturidade-de-Sistemas/Nível-1/Análise-estática-de-código-\(camada-de-segurança\)/Fortify) 

- [ ] [Configurando integração Continua no ADS (Azure Devops Server):](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/39/Fortify?anchor=integrando-o-fortify-ao-ads) 

- [ ] [Classificação de vulnerabilidades:](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/39/Fortify?anchor=integrando-o-fortify-ao-ads)

### Critério de aceitação
Ter configurado uma ferramenta de análise estática de segurança, como o Fortify, fazendo alguma análise estática no código dentre as tasks de Integração Contínua, ou, caso não haja possibilidade de Integração Contínua, haver uma garantia (via procedimento acordado) de que a análise seja realizada antes de cada deploy em produção.

