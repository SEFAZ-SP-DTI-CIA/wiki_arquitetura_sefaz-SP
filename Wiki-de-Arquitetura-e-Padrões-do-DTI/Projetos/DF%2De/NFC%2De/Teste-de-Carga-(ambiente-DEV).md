[[_TOC_]]

## Máquinas envolvidas:

- Srv72049 – cockpit do teste, onde tem projeto do Visual Studio
- Srv72050 – controladora dos agentes
- Srv72051 a srv72060 – agentes
- Srv70033 – banco de dados (instância é srvdes01)
- Srv72067 – web-service da NFC-e
- Srv72068 – Windows service da NFC-e

##    Preparando os logs nas máquinas que rodam a NFC-e:

Nas máquinas 67 e 68, iniciar o perfmon. Nessas duas máquinas foram definidos um "User Defined" "Data Collector Set" chamado "NFCe". Basta iniciá-lo que ele vai gerar automaticamente logs em um diretório que dá para ver qual é no perfmon.
Na máquina do banco de dados, idem acima, só que o “Data Collector Set” se chama “NFC-e IndicesPerformance”.
Na máquina do banco de dados, iniciar o SQL Management Studio 2012 e rodar os scripts da Renata. Acho que aqui é necessário ter permissão de SA.

##   Preparando os agentes

Cada um dos agentes tem um arquivo Config.xml no diretório "d:\NFC-e\Teste de Carga". Nele temos valores para configurar:

autorização: número de série e número inicial da NF a ser autorizada. O número de série é constante, o número da NF é incremental.
inutilização: quantidade a ser inutilizada por requisição, série e numero inicial da NF. O número de série e a quantidade são constantes, e o número da NF é incremental.
    O algarismo da unidade da série (p. ex. "1" se série = 321) é o mesmo do último algarismo do servidor (p. ex. "1" no caso da srv72051). Portanto ele não deve ser modificado.

 

A cada teste você deve decidir se modifica o número de série de cada agente. Se não modificar, o que vai acontecer é que as primeiras notas vão coincidir com as primeiras notas do teste anterior; isso pode ser um problema ou não, na verdade o sistema tem que rejeitar as autorizações e inutilizações repetidas.

 

Se a decisão for que as notas devem ser diferentes do teste anterior, deve-se abrir o arquivo de configuração de cada agente e aumentar o algarismo das dezenas da série da autorização e da inutilização. Se chegar no final das dezenas, deve-se incrementar as centenas de 2 e reiniciar a dezena.

 

Um método fácil de fazer isso sem precisar logar em cada uma das máquinas é:

abrir o console ('cmd') em uma das máquinas srv720xx, em que vc está logado com a conta _dev
digitar "start \\srv72051\d$\" , que vai abrir um Windows explorer
navegar até o diretório d:\NFC-e\Teste de Carga e editar o Config.xml

##Rodando o teste:

Na máquina do cockpit, logar e abrir a solution que está no diretório D:\NFC-e\Teste de Carga\SolutionTesteCargaNFCe
No projeto "Teste de Carga", abrir o load-teste chamado "LoadTest1.loadtest"
Abrir "Scenarios", "Teste 1 da NFC-e", selecionar "Constant Load Pattern" -> Properties , e definir a variável "Constant User Count", que define quantos usuários simultâneos vão rodar. Um valor que exercitou bem o teste de carga foi 150.
Abrir "Run Settings", selecionar "Run Settings1" -> Properties, e definir o tempo do teste.
Clicar na setinha verde para iniciar o teste

##Acompanhando evolução do teste no banco

É possível acompanhar se os processos estão gerando os registros no banco conforme esperado. Para tanto, abrir na máquina local o MS SQL Server Management Studio e criar a query abaixo, que verifica se
a fila está sendo criada e consumida
a numeração e o tempo de processamento dos lotes processados

`select count(1)
from [NFCeIn].[FilaProcessamento] with (nolock)`

`select top 5 *
from [NFCeIn].[Lote] with (nolock)
where status = 104
order by pkey desc`


##Alterando os testes

 

Se houver necessidade de alterar algum teste de carga:

Fazer inicialmente a alteração no Visual Studio 2010 da máquina SRV72049
Copiar manualmente para o Visual Studio 2012 da máquina local (e fazer check-in)