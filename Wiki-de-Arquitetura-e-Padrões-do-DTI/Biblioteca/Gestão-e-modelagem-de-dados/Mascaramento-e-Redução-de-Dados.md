[[_TOC_]]

# Mascaramento de dados
Mascarar os dados tem como principal objeto a segurança dos dados sensíveis contra acessos não autorizados. As ferramentas de mascaramento de dados criam dados semelhantes aos originais, com mesma estrutura, no entanto, sem mostrar o seu verdadeiro conteúdo. O formato original é mantido inalterado, mas o conteúdo dos dados acessíveis não correspondem aos originais. Os dados mascarados podem ser utilizados em ambientes de desenvolvimento, teste e homologação não comprometendo o resultado da análise, mas garantindo sempre a confidencialidade da informação sensível.

##Visão Geral sobre Mascaramento de Dados
Use o mascaramento de dados para substituir os dados de origem em colunas confidencias com dados de teste realísticos em ambientes que não sejam de produção. Ao criar regras de mascaramento de dados, você define a lógica para substituir dados confidenciais. Para configurar as colunas confidenciais que você deseja mascarar, atribua regras de mascaramento de dados às colunas de origem, aos domínios de dados e às diretivas.

Uma diretiva define as regras de mascaramento de dados, os dados a serem mascarados e os parâmetros de mascaramento para uma origem. Ao atribuir regras de mascaramento de dados para diretivas, você pode atribuir várias colunas de origem às regras de mascaramento de dados. Você também pode atribuir uma regra diretamente a uma coluna de origem. Você pode atribuir regras de mascaramento de dados com base nos tipos de dados das colunas de origem.

Para implementar o mascaramento de dados, crie um plano de mascaramento de dados e gere um fluxo de trabalho a partir do plano. Um plano de mascaramento de dados pode conter diretivas e regras. Ao selecionar uma regra em um plano de mascaramento de dados, você atribui uma coluna à regra. Diretivas e regras definem como mascarar dados reservados e confidenciais em um banco de dados de destino. Um plano de mascaramento de dados contém pelo menos uma regra ou uma diretiva.

##Definições
- Algoritmo de mascaramento: Método utilizado para descaracterizar o conteúdo de um campo de uma tabela de banco de dados de modo a diferi-lo do valor do campo original.
- Catálogo de dados: Documento que descreve a semântica dos campos das tabelas de banco de dados e contém informações como classificação e restrição de acesso. 
- Dados de Produção: São dados coletados ou gerados pela SEFAZ e armazenados em ambiente tecnológico, os quais possuem validade e/ou refletem uma situação jurídica real. 
- Informação: Entende-se por informação tanto o dado unitário em uma base de dados, como o conjunto de dados unitários que tenham significado quando relacionados. 
- Gestor da Informação: Entende-se por gestor da informação o servidor fazendário designado pelo respectivo setor de trabalho que tem a incumbência legal ou normativa para produzir, coletar e manipular a informação, sendo – portanto – capaz de estimar em que nível de criticidade a informação se enquadra. 
- Dados sensíveis: São subconjuntos das informações armazenadas nos sistemas da SEFAZ, protegidas por sigilo fiscal ou não, cujo conteúdo pode causar prejuízos ou responsabilização legal para SEFAZ ou a terceiros no caso de divulgação, alteração ou utilização de forma inadequada ou indevida. 
- Mascaramento de dados: Processo de ofuscação de dados informações reais com o uso de técnicas e algoritmos, porém mantendo seu formato original e suas propriedades para que as aplicações continuem a funcionar adequadamente. 
- Redução de dados: Consiste em criar um subconjunto de dados a partir da base de dados original;
- Workflow: Procedimento criado na ferramenta de gerenciamento de dados de testes – TDM que armazena as regras de mascaramento e realiza o processo quando executado; 

## Componentes de Mascaramento de Dados
Para executar as operações de mascaramento de dados, atribua regras aos domínios de dados, diretivas e colunas. Use os domínios de dados e a descoberta de dados para localizar as colunas que deseja mascarar. Crie cascatas para mascarar colunas semelhantes.

###Atribuições: 
A alocação de regras a uma coluna para mascarar os dados de coluna. Você atribui uma regra para uma coluna seja por meio de uma atribuição de coluna ou de uma atribuição de domínio de dados. Uma atribuição de coluna atribui uma regra diretamente a uma coluna em uma origem. A atribuição de um domínio de dados designa uma ou mais regras em um domínio de dados para as colunas em uma origem.

###Coluna com dados reservados:
Uma coluna com dados reservados contém dados confidenciais. Configure a sensibilidade da coluna para marcar as colunas que deseja mascarar.

###Domínio de dados:
Um objeto que representa o significado funcional de uma coluna com base nos dados da coluna ou no nome da coluna. Use um domínio de dados para filtrar as portas que você deseja mascarar ao atribuir uma regra a colunas. Defina padrões nos dados ou padrões nos nomes de coluna ao configurar um domínio de dados.

###Plano:
Define as operações de mascaramento de dados. Um plano de mascaramento de dados indica se os dados são mascarados no lugar no banco de dados de origem ou no fluxo em um banco de dados de destino.

###Diretiva:
Define as regras de mascaramento de dados, os dados a serem mascarados e os parâmetros de mascaramento para uma origem.

###Regra:
Define a técnica de mascaramento de dados, um qualificador de regra opcional e os parâmetros de mascaramento.
Uma técnica de mascaramento define a lógica usada para mascarar os dados. Os parâmetros de mascaramento definem como uma técnica de mascaramento em uma regra mascara os dados de origem. É possível definir uma opção de substituição em uma regra que define se os usuários podem modificar os parâmetros de mascaramento da regra quando eles atribuírem a regra às colunas em uma origem.

###Cascata de valor:
Mascara colunas semelhantes entre tabelas. Você pode identificar colunas semelhantes em um projeto e configurá-las para regras de mascaramento de cascata. Use cascatas quando alguns campos não estiverem normalizados entre várias tabelas.

##Fluxo de Tarefa de Mascaramento de Dados
Para implementar as operações de mascaramento de dados, atribua regras de mascaramento às colunas em uma origem. Crie um plano e adicione diretivas e regras ao plano. Gere um fluxo de trabalho a partir do plano para mascarar dados em um banco de dados de destino.

Conclua as seguintes etapas de alto nível para criar os componentes que precisará em um plano de mascaramento de dados:
- Crie regras, domínios de dados e diretivas para definir as técnicas de mascaramento e parâmetros de mascaramento.
- Crie um projeto que contenha uma ou mais fontes de dados.
- Adicione diretivas ao projeto. Quando você adiciona uma diretiva a um projeto, o projeto recebe os domínios de dados e as regras que a diretiva contém.
- Adicione mais regras ao projeto. Quando você adiciona mais regras a um projeto, o projeto recebe as regras de mascaramento.
- Atribua regras às colunas na origem. Você pode atribuir regras de uma diretiva ou um domínio de dados. Você pode atribuir as regras padrão dos domínios de dados a várias colunas de uma vez. Atribua manualmente regras avançadas e regras de mapplet a colunas.
- Crie um plano e adicione componentes de mascaramento de dados ao plano. Gere um fluxo de trabalho por meio do plano. Monitore o andamento do fluxo de trabalho na exibição Monitor ou na exibição Plano.

# Processo de Mascaramento
As etapas do processo de mascaramento de dados devem abranger, no mínimo, as seguintes atividades:
 - Identificação dos dados - para cada aplicação devem-se identificar quais dados devem ser protegidos. Nessa etapa deve ser elaborado um catálogo de dados sensíveis, os quais devem ser obrigatoriamente mascarados na transição entre bases de produção e homologação/desenvolvimento;
- Mapeamento do algoritmo de mascaramento adequado a cada tipo de dados;
- Configuração e elaboração de rotinas (workflow) de mascaramento de dados;
- Execução manual ou agendada do workflow de redução e mascaramento de dados; 
- Teste de efetividade da descaracterização.
