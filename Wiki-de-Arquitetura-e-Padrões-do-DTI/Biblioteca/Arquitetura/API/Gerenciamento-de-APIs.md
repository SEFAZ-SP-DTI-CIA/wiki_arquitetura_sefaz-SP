**Gerenciamento de API's** 

O objetivo da gestão de API's é permitir o controle, identificação e monitoramento das requisições de provenientes de fora da Sefaz.

Um exemplo bem visual de uma API pode ser visualizado na figura abaixo.
![Items.png](/.attachments/Items-6d317c50-a542-4e0d-8a01-2699ef02e4f9.png)

Neste cenário, questões de : " Quem Utiliza nossos serviços ?, Quantas requisições chamam nossas aplicações por minuto ?, De onde vem estas requisições ?, Estamos tendo um Ataque Hacker por (Brute Force) ? ", necessitam de uma métrica para respostas aos Gestores que só é obtida mediante processos de governança nas Api's existentes.

Desta forma a ideia é que este cenário... 

![cenarios.png](/.attachments/cenarios-e3c34fe1-c7a1-403c-a460-e757e576ae84.png)

Seja migrado para este.

![migrado.png](/.attachments/migrado-d134d0f5-6ce0-492d-84f5-aa49e3adec90.png)

**Ferramentas de Api Gateway sendo consideradas no ambiente Sefaz.**

   As ferramentas disponíveis para controle das API's são várias no mercado, todas tem seus pontos fortes e fracos.

Inicialmente está em fase de validação a opção do KONG Api Gateway, uma solução tida no mercado como robusta e capaz de controlar o fluxo de requisições de forma estável e confiável.

Sua colocação no ‘Brand Market’ do setor pode ser visualizada no gráfico abaixo , onde seu posicionamento está próximo aos dos lideres de mercado.
![quadrantes.png](/.attachments/quadrantes-a18d1a50-fc80-4c43-9eaf-1acf3c6c214e.png)

**_Kong Overview:_**

![fluxo-kong.png](/.attachments/fluxo-kong-86247a6a-7f3b-40bf-84e9-6fd31ce42f4f.png)

**_Kong Características:_**

Como pode ser visualizado no gráfico acima, o Kong possui várias características:
-	Centralização do acesso aos serviços das API's para o mesmo DNS.
-	Controle de acesso fornecendo uma camada segura de autenticação. 
-	Monitoramento do tráfego que chega na API .
-	Transformação de requisições e respostas para diversos fins como, por exemplo, mudar a requisição e resposta para manter compatibilidade com sistemas legados, ex (entrada em JSON e saída em XML).
-	Governança das aplicações
O Kong permite por meio de um Dashboard Administrativo organizar e agrupar as API's por domínios de negócio, com isso fornece um controle centralizado de permissões para os usuários, definindo papéis sobre o que cada um pode fazer (visualizar, alterar, deletar, criar, publicar, etc..).

**_Kong Ambiente de Administração_**

O Kong é uma solução baseada em Plugins , através de um Dashboard Administrativo que permite a gestão das rotas e consumers que fazem requisições através dele, expondo serviços atrelados as rotas internas de tal forma que a aplicação não irá saber qual endereço a Api está sendo consumida, o que potencializa a característica de segurança e governança do gateway.

![ambiente-kong.png](/.attachments/ambiente-kong-b6b62fb1-45a4-431c-a9e8-77cb43129295.png)

Como pode ser visto na ilustração, o Kong disponibiliza um ambiente de controle e monitoramento das Apis, conforme discutido anteriormente.
Uma das características mais interessantes do Kong é a facilidade com que ele expõe objetos para chamadas externas protegendo objetos dentro da rede, desta forma ele expõe Serviços acoplados à rotas, e estas mapeiam as Apis propriamente ditas dentro da rede Sefaz. 

![objetos.png](/.attachments/objetos-c36f8549-c99c-4587-b287-2ce81d9776e8.png)

Estes serviços tem um componente principal, os plugins utilizados pelo Kong que executam os processos de controle de tráfego conforme a ilustração abaixo. 

![fluxo-kong.png](/.attachments/fluxo-kong-0d5cc228-32d4-48da-a793-20b401a42e99.png)

Estes plugins são organizados em diversas categorias com alguns de seus componentes:
-	Autenticação
	Plugins de Autenticação (Básica, Criptografada, HMAC, Tokem (JWT))
-	Segurança
	Plugins de controle das requisições ( Deteção de BOTS (Robôs), CORS (política de CORS na requisição), Restrição de IP's)
-	Controle de Tráfego
	Plugins de controle de Tráfego ( ACL (Permissão de Consumers ao acesso de serviços), Limite de Taxa (Load Balance),Limite de taxa de resposta (Answer Balance) )
-	Serverless
	Funções Lambda
-	Análise e Monitoramento
	Plugins para geração de Dashboards de Verificação
-	Transformação
	Plugins de transformação de dados das requisições (XML para JSON)
-	Análise de Log
o	Plugins para geração e análise de log das requisições

**Considerações Finais**

O intuito deste conteúdo não é gerar uma documentação pormenorizada do Kong e sim dar um ‘OverView’ deste API Gateway entendendo como esta ferramenta pode implementar uma melhor governança no gerenciamento de API's.

Como esta ferramenta ainda está sob análise, conforme outras opções de Mercado tais como: ( AMAZON Api Gateway, WSO2 Api Manager, Desenvolvimento Próprio (uma questão de custo/benefício) ) , que vierem a ser ponderadas, serão devidamente explanadas neste documento.
