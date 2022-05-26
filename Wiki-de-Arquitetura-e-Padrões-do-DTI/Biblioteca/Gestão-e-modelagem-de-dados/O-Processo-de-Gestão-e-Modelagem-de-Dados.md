[[_TOC_]]

#Descrição do Processo
O Processo de Gestão e Modelagem de Dados deve contemplar tantos os projetos de produtos, sejam para o desenvolvimento de sistemas novos ou apenas para manutenção evolutiva, como também aquelas solicitações de suporte e consultoria à equipe do CCD.

##Processo de Desenvolvimento de Projetos
O processo que suporta os projetos de produtos possui uma formalidade maior quando comparado com o processo de suporte, e deve ser iniciado logo após a conclusão do [Desenho de Solução](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/69/Desenho-de-Soluc%C3%A3o), tendo em vista que nesta etapa preliminar já devem existir informações técnicas suficientes para que se possa ter seu início. 

O Processo de Gestão e Modelagem de Dados aplicável aos projetos de produtos possui três fases mandatórias: 
- Reunião Inicial: antes de se começar o sistema/projeto e imediatamente após a reunião de desenho de solução; 
- Reunião de Homologação: anteriormente à promoção do sistema/projeto para o ambiente de homologação;
- Reunião de Produção: anteriormente à promoção do sistema/projeto para o ambiente de produção.

Outras reuniões entre as equipes do CCD e de projeto, tendo em vista a necessidade de algum suporte técnico relacionado aos dados ou de alguma revisão específica de problemas encontrados, devem ser solicitadas por estas últimas ao CCD.

##Processo de Suporte
O processo que contempla as solicitações  ao CCD de suporte, fora do contexto de projetos, envolverão quaisquer temas relacionados à ciência de dados, tais como: 
- Avaliação da arquitetura de dados atual e de suas bases de dados; 
- Propostas de novas arquiteturas de dados, levando-se em consideração eventuais bases de dados já existentes;
- Planejamento de modelos de consumo; 
- Métodos de aquisição e extração de dados via ETL (extração, transformação, carregamento), SSIS, outros; 
- Análise de processamento de grande volume de dados; 
- Aplicação de Inteligência artificial; 
- Busca em textos; 
- Análise de processamento de dados em tempo real; 
- Mascaramento de dados em ambientes que não sejam de produção.

O processo aplicado ao suporte à consultoria em geral não deve possuir nenhuma formalidade em particular, a não ser a obrigatoriedade de que as suas reuniões e atas sejam devidamente documentadas no ETC, de modo que seus resultados possam ser reutilizados em outras requisições semelhantes.

##Distribuição das Requisições pelo CCD
A distribuição das requisições submetidas ao CCD será realizada levando-se em consideração a qual produto se relaciona o projeto ou a consultoria pedida. Em caso de haver mais de um produto envolvido no projeto, deverá ser feita a distribuição levando-se em consideração aquele produto considerado mais predominante. Para cada produto suportado pelos processos da SEFAZ, haverá um grupo de arquiteto de dados previamente designado para o mesmo.
 
A distribuição das requisições ao CCD será feita, preferencialmente, dentro deste grupo, contudo, é possível que por necessidade operacional (férias, licença etc.) alguma requisição possa ser direcionada a um arquiteto que não seja do grupo do seu produto.

Buscar-se-á uma distribuição equitativa das requisições dentro de cada grupo de arquitetos, assim como também obedecer-se-á, sempre que possível, a uma sequência temporal, na qual a data de criação da requisição determinará a sua ordem de atendimento.

##Reunião Inicial Obrigatória
A primeira reunião obrigatória do Processo de Gestão e Modelagem de Dados deve ocorrer o mais cedo possível e após a finalização da reunião inicial de solução de desenho. A solução de desenho possui objeto e abordagem distintos daquela da gestão e modelagem de dados. Primeiramente, o desenho de solução envolve uma análise da arquitetura do sistema como um todo, contemplando a infraestrutura de hardware, software, segurança, comunicação, camadas de dados, aplicação, apresentação etc. O escopo da primeira reunião de desenho é amplo e envolve equipes de várias áreas, possuindo como objeto uma análise preliminar da arquitetura completa do projeto. Com relação à Reunião Inicial Obrigatória de gestão e modelagem de dados, o objeto da análise arquitetural é bem mais específica, quando comparada com aquela da reunião de desenho: ela deve se ater tão somente às estruturas e aos processos aplicáveis aos dados do projeto e aos seus produtos.
	
Para que a Reunião Inicial Obrigatória possa ocorrer, são exigidas das equipes de desenvolvimento as seguintes informações e documentações preliminares: 
- Definição de qual Sistema de Gerenciamento de Banco de Dados (SGBD) será utilizado pelo projeto. 
- Diagrama(s) de modelagem de dados preliminar. Essa modelagem preliminar pode ser na forma de um modelo conceitual, lógico ou físico, a depender da fase de amadurecimento na qual o projeto esteja.
- Dicionário de dados preliminar. O dicionário de dados não precisa descrever as entidades / atributos de conhecimento comum dentro da SEFAZ, contudo, deve necessariamente definir as entidades específicas do projeto.

A definição de qual SGBD é o mais adequado para cada projeto é de responsabilidade da sua equipe de desenvolvimento, uma vez que é ela que detém os conhecimentos técnicos específicos de cada sistema. Naturalmente, a escolha, de uma forma geral, deverá recair sobre um dos principais SGBD suportados pela DTI (Oracle, Sybase, MSSQL Server). A equipe CCD poderá auxiliar nessa escolha quando identificar, pela natureza do projeto, que um determinado SGBD seja mais adequado do que outro.

Foi escolhido pela DTI/CDS para aquisição a ferramenta de modelagem de dados PowerDesigner, da SAP. Ela será utilizada, num futuro breve, pelo CDS para confecção dos diagramas de modelagem de dados (conceitual, lógico e físico). O objetivo é que cada time de projeto tenha pelo menos uma licença de acesso nesta ferramenta, de modo que um de seus integrantes possa confeccionar os diagramas de modelagem de dados requeridos pelo Processo de Gestão e Modelagem de Dados. 

Será obrigatório apresentar na Reunião Inicial de Gestão e Modelagem de Dados o modelo de dados do sistema. Contudo, se o projeto for considerado ainda imaturo, poderá ser apresentado um modelo parcial ou o modelo conceitual. O dicionário de dados do projeto também deve ser apresentado neste momento, especificando e esclarecendo aquelas entidades e atributos específicos do sistema em estudo, uma vez que que essas entidades não serão de conhecimento do CCD e das demais equipes.

Nesta primeira reunião serão definidos os ajustes de arquitetura que eventualmente se apliquem ao projeto. Caso sejam consideradas necessárias, reuniões adicionais podem ser definidas para endereçar dúvidas ou ajustes específicos. É também nesta reunião que as equipes estabelecem a frequência e o cronograma de futuras reuniões. É importante se re-enfatizar que somente a reunião inicial, a reunião de homologação e a reunião de produção são obrigatórias. Qualquer reunião adicional deve ser requerida pela equipe de desenvolvimento ou requerida pela equipe do CCD.

Se for identificado pelo responsável do sistema/projeto a necessidade de [Mascaramento ou Redução de Dados](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/190/Mascaramento-e-Redu%C3%A7%C3%A3o-de-Dados) será agendado uma reunião específica para atender a essa necessidade. Cabe ao responsável do sistema/projeto junto ao CCD a identificação desta necessidade, tendo em vista que é a equipe do sistema que conhece a semântica do conteúdo dos seus dados e a sua necessidade ou não de mascaramento e/ou redução. 

Recomenda-se que sejam planejadas algumas reuniões intermediárias – entre a reunião inicial e a reunião de homologação – envolvendo o CCD, de modo a se prevenir as ocorrências de erros de arquitetura de dados. A quantidade e a frequência dessas reuniões devem ser definidas tendo por base a complexidade de cada projeto em particular.

Se a equipe desenvolvimento optar por agendar com o CCD somente as reuniões obrigatórias, abdicando de outras reuniões intermediárias durante o ciclo de desenvolvimento do projeto, entende-se que esta equipe assume a responsabilidade individual de aplicar ao seu projeto todas as orientações de melhores práticas estabelecidas e especificadas pelo CCD.

##Reunião de Homologação Obrigatória

Na reunião obrigatória de homologação deverá ser aferida a completa adequação do projeto a todas as padronizações previstas pelo DTI, assim como também aquelas definidas nos manuais de melhores práticas de modelagem de dados estabelecidas pela CCD. 

Será verificada a adequação dos seguintes temas: 
- Padronização de nomenclatura de todos os objetos de banco de dados; 
- Guia de boas práticas de programação em Banco de dados, envolvendo tópicos tais como: 
  - SSIS;
  - Desenvolvimento em banco de dados
    - Programação em Transact-SQL (triggers, funções, cursor, SQL dinâmico etc.); 
    - Programação em Oracle SQL (triggers, funções, cursor, SQL dinâmico etc.); 
    - performance em geral; 
    - Reporting Services;
 - Atendimento dos padrões estabelecidas pelo COI para os bancos de dados em produção: 
   - Estrutura interna dos file systems; 
   - Particionamento de tabelas;
   - Nomenclatura específicas de objetos em produção;
   - Segurança, usuários, acessos definidos em ambiente de produção.
 - Tópicos de arquitetura de dados, tais como: 
   - Normalização de tabelas; 
   - Redundância envolvendo entidades, atributos ou mesmo banco de dados; 
   - Outros temas identificados somente em tempo de homologação, uma vez que as equipes percebam a sua importância e pertinência no contexto de promoção do projeto para o ambiente de produção.

###Preparação de scripts e checklist de Homologação
Na reunião de homologação é mandatório que a equipe do projeto apresente o checklist dos scripts e documentação de todos os passos a serem executados em homologação (com relação ao processamento de dados) para preparar e modificar este ambiente para receber o novo sistema. A importância da preparação dos scripts e do checklist em ambiente de homologação se fundamenta no fato que as mesmas operações serão refeitas no ambiente de produção, com exceção apenas dos ajustes referentes ao ambiente em si mesmo.

##Reunião de Produção Obrigatória
Na reunião de produção serão verificados que todos os problemas previamente identificados em homologação foram resolvidos, acrescidos de qualquer pendência das etapas anteriores. Esta reunião precede a passagem de produção propriamente dita e tem por objeto garantir que não ocorra nenhuma inconsistência de arquitetura ou procedimental com relação àqueles processos estabelecidos pelas equipes do COI, especificamente a equipe DBA.

Todas as eventuais ocorrências, durante o processo de homologação do projeto, que demandarem modificação na estrutura de dados precisam ser revistas antes de se proceder a passagem para produção.

###Preparação de scripts e checklist de Produção
Na reunião de produção também é mandatório que a equipe do projeto apresente os scripts e a documentação de todos os passos a serem executados (na forma de checklist) no âmbito da gestão dos dados. A importância da preparação dos scripts e do checklist em ambiente de produção reside no fato que os mesmos serão executados pela equipe de dbas de produção sem, necessariamente, haver o envolvimento técnico da equipe do projeto. A disponibilidade prévia dos checklist e dos scripts a serem executados em produção é mandatória! Sem a apresentação desses artefatos a Reunião de Produção será cancelada.

##Reuniões Técnicas

As reuniões técnicas entre as equipes do CCD e dos projetos de cada um dos sistemas ocorrerão quando forem mandatórias (após o desenho de solução, antes da passagem para o ambiente de homologação e antes da para passagem para o ambiente de produção) ou quando forem solicitadas revisões pela equipe do projeto. Os escopos das reuniões técnicas mandatórias já foram definidos em tópicos anteriores. Com relação às reuniões técnicas facultativas, as mesmas devem ser solicitadas pelas equipes de projeto, ficando a cargo dessas equipes a definição de suas pautas. Estas pautas devem ser orientadas a sanar as dúvidas específicas que surgirem dentro do processo de gestão e modelagem de dados.
	
As reuniões solicitadas ao CCD deverão ser conduzidas por um analista de dados previamente designado para o projeto. Este analista fará o acompanhamento das reuniões do projeto até a sua conclusão, uma vez que não é produtiva a troca de analista de dados no meio do processo, tendo em vista o acúmulo natural de conhecimentos técnicos que os partícipes adquirem ao longo do desenvolvimento do projeto. Naturalmente, pode haver trocas de analistas em caso de necessidades diversas, contudo é premente que as equipes façam a transferência de conhecimentos aos novos integrantes.

As reuniões técnicas focarão na documentação produzida pelas equipes dos projetos:
- Diagramas de modelagem de dados; 
- Dicionário de dados; 
- Arquitetura de distribuição de camadas de processamento; 
- Arquitetura física de servidores; 
- Arquivos de sistema de bancos; 
- Particionamento de tabelas etc.
- Acesso e análise direta dos bancos de dados se os mesmos já estiverem disponíveis.

Durante a análise direta das bases dos projetos – se já existirem – e da sua documentação produzida, a equipe do CCD analisará a adequação dos diversos elementos de sua arquitetura de dados e do(s) produto(s) aos quais esses projetos fazem parte. Essa revisão contemplará tópicos tais como: 
- Avaliação da arquitetura de dados atual e de suas bases de dados; 
- Propostas de novas arquiteturas de dados, levando-se em consideração eventuais bases de dados já existentes; 
- Planejamento de modelos de consumo; 
- Métodos de aquisição e extração de dados via ETL (extração, transformação, carregamento), SSIS, etc.; 
- Análise de processamento de grande volume de dados; 
- Aplicação de Inteligência artificial;
- Busca em textos; 
- Análise de processamento de dados em tempo real; 
- Mascaramento de dados em ambientes de homologação e desenvolvimento.

Adicionalmente aos tópicos arquiteturais acima mencionados, as reuniões técnicas também terão foco nos elementos específicos de modelagem de dados e nas boas práticas definidas pelo DTI. Os seguintes tópicos serão verificados quanto à padronização adotada pelo CCD: 
- Adequação de Nomenclatura 
- Adequação dos Tipos de Dados
- Normalização da Base de Dados
- Identificadores de Entidades (Chaves Primárias)
- Campos nulos
- Adequação de Índices nas tabelas
- Adequação das Chaves Estrangeiras
- Uso de Views
- Codificação em Bancos de Dados:
  - Stored Procedures;
  - Uso de Stored Procedures; 
  - Uso de Triggers; 
  - Controle de Transações; 
  - Uso de Cursor;
  - Processamento de Arquivos;
  - Uso do SQL Server Integration Services (SSIS)
  - Uso do Reporting Services
- Particionamento de Tabelas
- Adequação de File Groups e File System

##Atores do Processo de Gestão e Modelagem de Dados

Os atores no Processo de Gestão e Modelagem de Dados são aqueles profissionais que têm um papel específico de atuação dentro do processo. Um ator representa um conjunto coerente de papéis que podem ser comparados com aqueles que os usuários de Casos de Uso desempenham quando interagem com os mesmos. Tipicamente, um ator representa um papel que um ser humano ou uma funcionalidade desempenha no sistema. No Processo de Gestão e Modelagem de Dados são identificados os seguintes atores atuantes:

###Equipe CCD

É a equipe responsável pela implantação das políticas de dados de todos os sistemas sob responsabilidade da DTI/CDS. A equipe CCD tem a responsabilidade de conduzir todas as reuniões técnicas de revisão junto às equipes de desenvolvimento e aos seus projetos, orientando e exigindo a adequada arquitetura nas estruturas de dados dos sistemas sob sua revisão.

###Equipe de Projeto

São as equipes que executarão o desenvolvimento de projetos junto ao CDS. As equipes de projeto são responsáveis por requisitar junto à equipe do CCD a abertura do projeto no Processo de Gestão e Modelagem de Dados. Essas equipes são responsáveis pela produção e correção da documentação técnica de dados de cada um de seus projetos.

###Analista de Dados Responsável

É o membro da equipe CCD designado como responsável pelo acompanhamento do projeto junto à sua equipe. Este analista de dados é o responsável pela qualidade e arquitetura do projeto com relação aos seus dados. Ele fará acompanhamento do projeto junto à equipe de projeto, agendando as reuniões de revisão, identificando as eventuais pendências que haja e as ações a serem tomadas.

###Líder do Projeto

É o membro da equipe de desenvolvimento designado como responsável pelo acompanhamento do projeto do junto ao CCD. Este analista  é o responsável pela qualidade e arquitetura do projeto com relação aos seus dados. Ele fará acompanhamento do projeto junto à equipe de projeto, agendando as reuniões de revisão, identificando as eventuais pendências que haja e as ações a serem tomadas.

##Premissas e Restrições 
###Premissas
A mais importante premissa do Processo de Gestão e Modelagem de Dados é a estrita observação das boas práticas de arquitetura de dados definidas em seus manuais.  Não há como implantar um processo como esse sem que ele não possua uma natureza de obrigatoriedade, tanto com relação àquelas regras definidas como sendo de boas práticas, como também as recomendações consideradas mandatórias e a participação nas reuniões inicial, de homologação e de produção.

Certamente, cabem revisões periódicas às boas práticas de arquitetura de modelagem de dados, de modo a aferir a sua pertinência ao contexto da realidade do CDS. Nenhuma recomendação técnica possui caráter permanente, podendo ser ajustada às mudanças tecnológicas e as de processo. No entanto, as reuniões entre as equipes do CCD e as equipes de projetos não têm o objetivo de alterar recomendações de arquiteturas abstratas e gerais, mas, sim, de efetivá-las para o caso concreto de um determinado projeto em particular. Ajustes de metodologia e alterações de recomendações de mudança de arquitetura abstratas devem ser objeto de reuniões entre a CCD e outras áreas do DTI. Portanto, pode-se concluir que a premissa mais importante do Processo de Gestão e Modelagem de Dados é a obrigatoriedade de suas recomendações. Qualquer exceção a essa premissa deve ser devidamente fundamentada.

###Restrições
O Processo de Gestão e Modelagem de Dados deve ser aplicado a projetos formais do DTI. Pequenas alterações ou ajustes que não se caracterizem pela formalidade de abertura de um projeto não deve ser objeto deste processo. A razão para isto é bem clara: se a mudança não justificou a abertura de um projeto, seja por ser muito pequena ou por ser emergencial, não se justifica também passá-la pelo controle formal do Processo de Gestão e Modelagem de Dados. Portanto, não se devem submeter ao Processo de Gestão e Modelagem de Dados aquelas alterações que não justifiquem a existência de um projeto.

Naturalmente, ainda que as alterações do projeto não seja elegíveis ao controle de Processo de Gestão e Modelagem de Dados, as equipes de desenvolvimento podem solicitar ao CCD reuniões técnicas "ad hoc" de orientações em tópicos de modelagem e gestão de dados que achem pertinente. A diferença residirá no formalismo do processo.


    


 

