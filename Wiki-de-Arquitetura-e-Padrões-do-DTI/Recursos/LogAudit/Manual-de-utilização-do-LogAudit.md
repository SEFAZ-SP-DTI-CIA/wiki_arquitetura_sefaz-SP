[[_TOC_]]
# 1. Sobre

O LogAudit (Figura 1) é um sistema que visa atender à demanda das aplicações da SEFAZ-SP em ter um repositório central para o cadastro de logs. 

No cenário atual cada aplicação fica encarregada de construir mecanismos próprios para o cadastro de seus logs, gerando desta forma consumo de recursos que poderiam ser utilizados em outras atividades além da desvantagem 

de se ter um ambiente descentralizado dificultando – por exemplo – trabalhos, como os de auditoria e análise de dados. 

O LogAudit é composto por um conjunto de aplicações que juntas fornecem os meios necessários para o funcionamento do sistema, sendo estas instaladas no servidor e em cada cliente que for utilizá-lo.

Com o LogAudit é possível enviar os logs mesmo que haja interrupção e falhas em recursos como banco de dados e rede; isto porque todo o trabalho é realizado e executado de forma assíncrona sob a estrutura do 

MSMQ (Microsoft Message Queuing) e com a interface do WCF (Windows Communication Foundation). Preferiu-se adotar esta estrutura devido ao fato de muitos aplicativos na SEFAZ já fazerem uso do WCF e portando o uso do MSMQ é

encapsulado sob o WCF facilitando o uso do serviço de logs como se estivesse consumindo um serviço como outro qualquer.

Existem duas interfaces – Log Simples e Log Auditado, para cada uma há componentes adequados para o uso.​​

# 2. Visão Geral do Sistema

Para cada mensagem de log há duas filas, uma local no Servidor da Aplicação Cliente e uma central no Servidor do LogAudit.

Quando a app envia o log, esse 1o vai para fila local e depois é tratada por módulos do LogAudit que endereçam o mensagem para fila central.

Mesmo que haja algum problema com a rede, a fila local sempre deve estar disponível. Assim que o problema de rede for resolvido, a mensagem continua seu trajeto até a fila central. 

Esse comportamento é tratado pelos módulos do LogAudit de modo transparente para as aplicações clientes.

![Figura1.jpg](/.attachments/Figura1-6295871a-d817-41a1-8346-cfc1eebc5747.jpg)

Figura 1

Os logs coletados podem ser de 2 tipos:
- **Log Simples** - Informações importantes sobre o funcionamento da aplicação. Auxiliam na análise da saúde das aplicações, como, por exemplo, indicando um eventual mau funcionamento da aplicação.
- **Log Auditado** - Informação de uma operação relevante para o negócio que a aplicação atende e que, portanto, tenha valor para efeitos de auditoria. Objetiva auxiliar no processo de análise/auditoria de aplicaçõe possibilitando responder: quais operações de negócio relevantes de uma determinada aplicação foram executadas, por quem, quando, onde, além de outras informações específicas de cada operação.
- Não é indicado registrar toda e qualquer ação do usuário na aplicação. Há necessidade de selecionar logs de auditoria que agreguem valor para o negócio ao serem registrados.

 O Log **Simples** pode ser categorizado em 3 tipos:

- **Error** -  Erro ou exceção na aplicação;
- **Warning** -  Alerta importante sobre o funcionamento da aplicação, que não necessariamente é um erro.
- **Information** - Informação sobre o funcionamento normal da aplicação. Pode ser usado para fins de debug, quando necessário.

O Log **Auditado** não possui categorização, ou seja, é apenas log auditado.

# 3. Pre-Requisitos

**Configuração do servidor de aplicação**

Toda aplicação que desejar fazer uso do LogAudit deverá ter o servidor onde a mesma está instalada devidamente configurado[1]. Esta configuração é por servidor e não por aplicação, ou seja, caso haja no servidor alguma aplicação que já faça uso do LogAudit, não haverá mais necessidade de configuração. Portanto, é importante verificar com a equipe do DTI-WINDOWS se o servidor da aplicação já está configurado para o LogAudit.

 >[Maiores detalhes sobre a configuração do LogAudit nos servidores de aplicação poderão ser encontrados no seu manual.](https://ads.intra.fazenda.sp.gov.br/tfs/PRODUTOS/Log_Audit/_wiki/wikis/Log_Audit.wiki/2798/Manual-de-instala%C3%A7%C3%A3o-e-configura%C3%A7%C3%A3o-do-LogAudit)


# 4. Componentes

* ## 4.1. Servidor  **Componentes do LogAudit disponibilizados via Nuget no servidor da SEFAZ**

   Para facilitar o processo de configuração e uso do LogAudit, são disponibilizados dois componentes (Sefaz.LogAudit.AuditClient, Sefaz.LogAudit.LogClient) (Figura 3) via Nuget[2] da Sefaz​.  Esses componentes já incluem ao projeto todas as dependências e configurações necessárias ao uso do LogAudit. Atentando para substituição dos parâmetros destacados na (Figura 4).  O valor da chave <font color=blue>**LogAuditClientCertificateCN**</font> deve ser o nome do certificado criado especificamente para a aplicação cliente que deseja utilizar o LogAudit. <br>
Para solicitar a criação do certificado usar o manual [Procedimento para solicitar Certificado de Grupo de Aplicação (CA INTERNA)V2](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/4d6df5f0-2d1f-4431-b769-f063824bd57e/_apis/git/repositories/1885edf5-80ac-4cbb-b3e3-bdf0ea291276/items?path=%2FSefaz%20Identity%2Fv003%2FLog%20Audit%2FProcedimento%20para%20solicitar%20Certificado%20de%20Grupo%20de%20Aplica%C3%A7%C3%A3o%20(CA%20INTERNA)V2.pdf&versionDescriptor%5BversionOptions%5D=0&versionDescriptor%5BversionType%5D=0&versionDescriptor%5Bversion%5D=master&resolveLfs=true&%24format=octetStream&api-version=5.0&download=true) 
<br>Por padrão já está configurado corretamente para o ambiente de homologação, caso seja produção bastará trocar "logaudit**hml**" por "logaudit**prd**" nos itens destacados; por exemplo: "dns value" em produção ficaria: "logauditprd.lbintra.fazenda.sp.gov.br.

   O parâmetro Ambiente.LogAudit poderá assumir três valores: **dev**, **hml** e **prd** indicando o ambiente alvo. Sendo que os acrônimos indicam respectivamente desenvolvimento, homologação e produção; deverão ser mudados dependendo do ambiente implantado. (Este parâmetro não interfere em nada no funcionamento do LogAudit, porém é bom preenchê-lo adequadamente pois poderá ter um uso válido no futuro).

   <font color= "red"> **Importante: Em produção o valor do parâmetro "LogAuditKeyPass" deverá ser substituído para: F4630CBE-4948-4C50-80A5-84579728149E**</font>
<br>
   >Estes componentes estão disponibilizados no servidor de Nuget da SEFAZ **(https://ads.intra.fazenda.sp.gov.br/tfs/PRODUTOS/_packaging/sefazFeed/nuget/v3/index.json)**, para maiores informações de como adicionar componentes via Nuget, acessar o link abaixo:

   [Como utilizar o Ruleset de Code Analysis da SEFAZ.pdf​](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/4d6df5f0-2d1f-4431-b769-f063824bd57e/_apis/git/repositories/1885edf5-80ac-4cbb-b3e3-bdf0ea291276/items?path=%2FSefaz%20Identity%2Fv003%2FLog%20Audit%2FComo%20utilizar%20o%20Ruleset%20de%20Code%20Analysis%20da%20SEFAZ.pdf&versionDescriptor%5BversionOptions%5D=0&versionDescriptor%5BversionType%5D=0&versionDescriptor%5Bversion%5D=master&resolveLfs=true&%24format=octetStream&api-version=5.0&download=true)

   _Sefaz.LogAudit.AuditClient   &    Sefaz.LogAudit.LogClient__ 

   Componentes que estão disponibilizados no Nuget e que deverão ser instalados conforme a necessidade para se fazer uso do LogAudit. 
O primeiro envia e trata logs auditaos e o segundo, logs simples, podendo ser usados individualmente ou juntos.

  ![figura3_1.jpg](/.attachments/figura3_1-f7df1ba6-6976-44f7-8deb-17c5812f6e4c.jpg)
   Figura 2

  ![PathNuget.png](/.attachments/PathNuget-a5173999-f35c-47ce-a29b-7e185306c0ca.png)

  ![figura3_3_1.jpg](/.attachments/figura3_3_1-7862b84a-d0fa-40a9-a488-9bdc07ad3703.jpg)
  Figura 3

  ![Configs.png](/.attachments/Configs-bd9968eb-d05b-4916-8fbb-485a47914981.png)
  Figura 4

* ##  4.2. **Bibliotecas adicionadas após a instalação**

   A (Figura 5) exemplifica as bibliotecas adicionadas após a instalação, lembrando que as bibliotecas Br.Gov.SP.Fazenda.LogAudit.AuditClient e Br.Gov.SP.Fazenda.LogAudit.LogClient podem ser instaladas isoladamente ou em conjunto.​

   A biblioteca **Sefaz.LogAudit.Client.Dependencies** é compartilhada tanto pelo componente **Br.Gov.SP.Fazenda.LogAudit.AuditClient**  como pelo **Br.Gov.SP.Fazenda.LogAudit.LogClient**. Sempre é bom manter esta dependência atualizada, ela é instalada de forma automática ao se optar por um dos componentes principais (LogClient, AuditClient). Ao ser instalado pela primeira vez é aconselhável marcar a opção <ins>__Dependency behavior como Highest__</ins>  como na figura abaixo (para qualquer um dos componentes): 
  ![NugetDependencyHighest - figura 5.png](/.attachments/NugetDependencyHighest%20-%20figura%205-fe478739-f9c0-4c1e-aa6d-848e0fd0a2c7.png)
![InstalledComponents - figura 5.png](/.attachments/InstalledComponents%20-%20figura%205-0240ec6a-f81e-4266-970f-5ed8bc2c54d6.png)
Figura 5
   <font color = "red">Caso ocorram erros durante o processo de instalação, uma das opções a ser executada é a limpeza de cache dos pacotes Nuget. Na figura logo abaixo está exemplificado este procedimento:</font>

  ![ClearNugetCache figura6.png](/.attachments/ClearNugetCache%20figura6-1afefeff-8d7c-4642-99a2-e390a9563217.png)

  O LogAudit faz uso do componente Log4net[3] para registro de possíveis erros que possam acontecer no envio dos logs. Há duas configurações que são acrescentadas no appSettings: log4net.Config e log4net.Config.Watch. 

  Recomendamos não alterar essa configuração.

* ## 4.3. **Nível de log do componente Sefaz.LogAudit.LogClient - Log Simples**<br>
  É possível configurar o nível de log que deseja ser registrado conforme a necessidade. 

    Este componente fornece os seguintes métodos de registro de logs em ordem crescente de criticidade:
   - WriteInformation
   - WriteWarning
   - WriteError (mais crítico)

   Os próprios nomes caracterizam o tipo de uso de registro. Cada aplicação é livre para decidir o que melhor se enquadra em cada contexto.

   Os níveis de logs são hierárquicos (Figura 6), isto significa que um nível inferior de criticidade não restringe um nível superior, porém o inferior deverá ser especificado explicitamente para seu registro. 

   Essa configuração é importante, pois mesmo que no código-fonte sejam invocados todos os níves de criticidade (Information, Warning e Erros), somente serão efetivamente gravados aqueles que se enquadrarem na configuração do logLevel do arquivo de configuração.

   Exemplo: Configurando o "logLevel" para "Information", todos os demais logs serão registrados, incluindo os logs de informação; configurando para "Warning"  todos os demais logs serão registrados, excluindo o "Information" que está num nível abaixo de criticidade e assim sucessivamente. <br>
![LogClientConfig figura 7.png](/.attachments/LogClientConfig%20figura%207-29391ac7-6eb9-41be-b1f4-b9729bd63fa3.png)
Figura 6

* ## 4.4. **Detalhes de uso do componente Sefaz.LogAudit.AuditClient - Log Auditado**

   Como já dito, este é o componente responsável pelo envio de informações que precisam ser auditadas. Importante fazer as seguintes considerações:
   * ### 4.4.1    **Auditoria** 
     Na (Figura 7) está um exemplo de código para o envio de mensagens auditadas;
        - No item **1)** é criado um DTO (Data Transfer Object – Importante ressaltar que as classes criadas não deverão ser complexas, isto é, com estruturas ou subclasses) simulando uma classe que conteria um conjunto de dados com informação que necessita ser  assinada digitalmente. É imprescindível (embora não obrigatório) que as declarações "AuditMetadata" sejam fornecidas documentando cada atributo da classe para efeitos de futura necessidade de análise e recuperação de dados.

        - No item **2)** podemos observar que a classe Audit herdou da classe LogAudit, isto por que a classe LogAudit é abstrata. Podemos também observar que os métodos protegidos da classe ancestral foram encapsulados de forma a prover valores "default", facilitando assim o uso. Observem que no item 3) o construtor da classe herdada recebe TRUE como parâmetro isto diz para o LogAudit repassar os erros internos (no envio de logs) para a aplicação, caso isto seja feito, toda chamada de métodos do LogAudit deverão possuir um tratamento de erro como no item 3), mas caso não não queiram que os erros (que talvez possam acontecer no envio de logs) sejam capturados pela aplicação basta passar FALSE para o construtor, desta forma caso haja algum erro no envio de logs a aplicação não será notificada. Porém os erros serão registrados no EvenViewer  em (Logs de Aplicativos e Serviços/LogAudit conforme item Tratamento de Erros deste manual).
     - No item **3)** é realizada a chamada de dois métodos: WriteRootEntry e WriteCorrelatedEntry. Na definição de ambos métodos podemos observar que o parâmetro "data" é opcional; neste parâmetro deverá ser fornecido um DTO com informações que precisam ser assinadas com o certificado de aplicação. Importante observar que a diferença deste parâmetro para os demais é que os dados contidos neste parâmetro serão assinados. O uso do parâmetro DTO dependerá da necessidade de negócio da aplicação e seu uso é opcional.

          Ao ser chamado o método WriteRootEntry será retornado um GUID que deverá ser armazenado pela aplicação. Como se trata de um procedimento assíncrono por mensageria, este GUID poderá ser utilizado, por exemplo, para pesquisar a informação no site do LogAudit.

          O uso do método WriteCorrelatedEntry é opcional e poderá ser utilizado no contexto em que haja necessidade de vincular informações. Podemos observar que o GUID que obtemos do WriteRootEntry foi passado como parâmetro, isto vinculará estas duas informações, este procedimento poderá ser realizado a qualquer tempo.

          Ao ser instalado, o LogAudit cria uma pasta na "Solution" do projeto com o nome **LogAuditExemplo** na qual está o exemplo que se segue abaixo - mostrado no item **Exemplo de Uso** deste manual.
![CodeExempleAudit_FIGURA 7.1.png](/.attachments/CodeExempleAudit_FIGURA%207.1-10cd42cc-3358-4565-9d10-1f1422dc6c53.png)
Figura 7

 * ## 4.5. **Observações importantes com relação ao tamanho dos dados enviados** 

     A mensagem de Log não poderá ultrapassar o limite de 4MB devido à estrutura do MSMQ. Para tanto foram impostos limites nos dados a serem enviados. 

     Abaixo segue uma relação destes valores. Caso exceda o limite, será gerada uma mensagem de erro alertando o problema e o log não será enviado.

     * ### 4.5.1 **Uso do componente Sefaz.LogAudit.LogClient**<br>
         Tabela 1

         |  Quantidade de Caracteres por campo |   |   |   |
         |---|---|---|---|
         | UserName  | UserIP  | Text  | LongText  |
          |255   | 50  |  1024 | 1048576  |
     * ### 4.5.2.  **Uso do componente Sefaz.LogAudit.AuditClient**<br>
         Tabela 2

         |  Quantidade de Caracteres por campo |   |   |   ||   |
         |--|---|---|---|--|---|
         | Category| Operation| UserName| UserIP| Message| Data (bytes)  |
          |100	| 150|  255| 50  | 4096[6] | 1048576[7]

      > 4096 é o tamanho final (já contando com os dados de criptografia). Um valor estipulado da quantidade máxima de caracteres (sem dados de criptografia) é 4070. Por exemplo, fornecendo um dado com 4070 caracteres no final ele ocupará 4096. Importante frisar que 4070 é um valor aproximado do limite máximo.

     >A informação armazenada neste campo é serializada, criptografada e assinada. O tamanho 4096 se refere à quantidade final de bytes ocupados. Para se ter uma ideia do que pode ser armazenado, considere que em média uma classe (DTO) com 28 campos e cada campo com 100 caracteres ocuparia 4096 bytes. Obviamente trata-se de uma estimativa para se ter uma ideia de tamanho. Geralmente um campo texto tem menos de 100 caracteres e, portanto,  a quantidade possível de campos poderia ser maior que 28. Aconselha-se a fazer um teste estimando antes o que será armazenado. Caso seja ultrapassado o limite, será gerado uma mensagem de erro e o log não será enviado.​​

# 5. Exemplo de Uso

   Ao ser instalado, o componente do LogAudit cria um diretório de exemplo de uso no arquivo de projeto (Figura 8). Este exemplo tem a finalidade apenas de esclarecer o uso básico das funcionalidades, podendo ser excluído. 

Para saber, por exemplo, se a instalação e configuração do componente do **LogAudit – Sefaz.LogAudit.LogClient (Log Simples)** – está funcionando, admitindo que seja um projeto em MVC, a (Figura 9)  exemplifica o teste do exemplo.

![Figura8.jpg](/.attachments/Figura8-536fa1af-f903-4f2f-8fec-f97a37987b6d.jpg)
Figura 8

Exemplo de Log Simples:

![Figura 9.jpg](/.attachments/Figura%209-7e375edb-4b84-4592-81bb-e941440488f3.jpg)

![figura9_2.jpg](/.attachments/figura9_2-cadec061-bfd5-4d41-8ef7-40145a0a395c.jpg)
Figura 9

# 6. Tratamento de Erros

O componente do LogAudit possui tratamento interno de erros (erros ocorridos por falha d​e envio). Todos os erros serão registrados na "Source" do LogAudit no EventViewer (Figura 10) – que deverá estar configurada no servidor onde a aplicação estiver instalada – além disto, possui registro de erros em arquivo.

No diretório da aplicação, deverá ser criado o diretório **Logs** e nas propriedades de segurança da pasta conceder acesso de leitura e escrita ao usuário da aplicação; por exemplo: Caso seja um site que use o ApplicationPoolIdentity com o Pool "APLICATIVO", deverá ser dado acesso de leitura e escrita no diretório ao usuário: "IIS APPPOOL\APLICATIVO". Caso haja algum erro, será criado o arquivo **LogAudit.Client.Log** com as informações sobre o erro.

Na Figura 10, por exemplo, houve um erro por que o usuário não forneceu o parâmetro "User Name" que é necessário para que a mensagem de log seja enviada.

Para problemas como indisponibilidade de recursos de rede, servidores inoperantes, etc. o LogAudit implementa tratamento de contingência, ou seja, serão realizadas novas tentativas e tratativas para que a mensagem possa ser enviada.  Para mais detalhes referentes ao fluxo de mensagens do LogAudit, consultar  o manual disponível em Log Audit (Manual de uso)​

Na (Figura 11) estão exemplificadas as filas do lado cliente do LogAudit e finalidade.  Caso alguma mensagem de Log não tenha sido gravada, é provável que a mensagem esteja em alguma dessas filas.​

![Figura10.jpg](/.attachments/Figura10-57d1412d-8af5-4b89-80a7-a65773eae8aa.jpg)

Figura 10

![Figura11.png](/.attachments/Figura11-016a7bc7-495f-42b9-bc95-1a100cc3b148.png)

Figura 11

# 7. Consulta de Logs

Para acesso aos logs registrados por cada aplicação foi disponibilizado um sistema de consulta​​ (Figura 12)  nos seguintes endereços de homologação e produção:

_Logs simples_

[https://webhomolog.intra.fazenda.sp.gov.br/Sefaz.LogAudit.QueryWeb.Log/](https://webhomolog.intra.fazenda.sp.gov.br/Sefaz.LogAudit.QueryWeb.Log/)
https://sefaznet11.intra.fazenda.sp.gov.br/Sefaz.LogAudit.QueryWeb.Log/

_Logs de auditoria_

https://webhomolog.intra.fazenda.sp.gov.br/Sefaz.LogAudit.QueryWeb.Audit/
https://sefaznet11.intra.fazenda.sp.gov.br/Sefaz.LogAudit.QueryWeb.Audit/

O acesso aos logs da aplicação cliente do LogAudit é automático para os administradores cadastrados no Controle de Acesso. Caso necessário, solicitar a inclusão do usuário como administrador.​

![SiteLogAudit.png](/.attachments/SiteLogAudit-a3088305-6606-4c3d-a014-037f472724f5.png)
Figura 17


