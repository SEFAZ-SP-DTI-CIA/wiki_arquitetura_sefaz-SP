# Extrator DW NFC-e

## Definição

O Extrator DW da NFC-e é uma aplicação que tem como finalidade:

 * ler informações da base da NFC-e;
 * tratar as informações lidas de modo a individualizá-las (como por exemplo quebrar um xml de uma nota armazenada em banco);
 * disponibilizar essas informações já tratadas em uma base dados (Sybase) que será posteriormente utilizada como insumo no processo ETL DW da DI.

## Layout

O layout do extrator DW define todas as informações que devem ser recuperadas da base da NFC-e e disponibilizadas no Sybase. Há um "de para" com as tabelas e colunas da NFC-e e as tabelas e colunas do Sybase.

É um documento que deve ser fornecido pela DI. A versão atual encontra-se em:

http://etc.intra.fazenda.sp.gov.br/sites/docdig_sp/nfce/_layouts/15/start.aspx#/default.aspx?RootFolder=%2Fsites%2Fdocdig%5Fsp%2Fnfce%2FDocumentos%20Compartilhados%2FSistema%2FAnalise%20e%20Design%2FEspecif%20Tecnica&FolderCTID=0x012000138E4D6A350A02489CAF4D4D137AC4BD&View=%7B44E8387C%2D8E9F%2D4520%2D9C04%2DCC085B89AF95%7D

## Estrutura da Aplicação

O Extrator DW está estruturado em cinco grandes processos que se referem ao tipo de informação extraida:

 * **Emissão** (autorização de NFC-e) 
 * **Evento** (atualmente reflete-se apenas ao Cancelamento)
 * **Evento EPEC** (apesar de também ser um evento, decidiu-se manter o processo do EPEC em separado, pois se trata da contingência da NFC-e)
 * **Inutilização**
 * **Credenciamento** (informações sobre contribuintes credenciados até a data da extração)

Cada um desses processos é dividido nas seguintes etapas:

 * **Extração**
Leitura da base da NFC-e, tratamento das informações e disposição em formato txt para posterior escrita no Sybase. A Extração é subdividida em threads:
   * **Readers** - fazem a leitura da base da NFC-e conforme os parametros especificados para a execução.
   * **Workers** - fazem o intermédio entre a leitura, envio para o parsing das informações e posterior disposição para a escrita em arquivos.
   * **Parsers** - fazem a quebra de informações contidas em XML.
   * **Writers** - escrevem os dados já tratados em arquivos txt conforme definido em layout.
 * **Importação**
Escrita no Sybase das informações armazenadas em arquivos txt. A escrita é feita através do comando Load Table.
 

## Parâmetros de Execução

Os parâmetros de execução do Extrator DW encontram-se na tabela **ETL_NFCe.Monitoracao.ConfigSistema**.

**EXTRAI_DATA_ESPECIFICA** Indica se deve ser efetuada extração de uma data específica. Utilizada em casos de reprocessamento. Por padrão tem valor **false**
**DATA_INICIAL_ESPECIFICA** Data de início da extração caso o parâmetro EXTRAI_DATA_ESPECIFICA seja true (inclui este dia), será extraído a hora também caso o parâmetro EXTRAI_DATA_E_HORA seja true
**DATA_FINAL_ESPECIFICA** Data de fim da extração caso o parâmetro EXTRAI_DATA_ESPECIFICA seja true (inclui este dia), será extraído a hora também caso o parâmetro EXTRAI_DATA_E_HORA seja true
**VERSOES_A_SEREM_EXTRAIDAS**  versões de autorização e inutilização a serem extraídas
**VERSOES_A_SEREM_EXTRAIDAS_EVENTOS** versões do processo Evento a serem extraídas
**BASEFILENAME** Diretório onde vão ser gravados os arquivos
**EMISSAO_NR_READERS** Quantidade de readers que devem ser executados simultaneamente
**EMISSAO_NR_WORKERS** Quantidade de workers (e parsers) que devem ser executados simultaneamente
**EMISSAO_ROWS_BATCH** Quantidade de registros a serem recuperados por chamada da procedure de leitura de NFC-e
**INUT_ROWS_BATCH** Quantidade de registros a serem recuperados por chamada da procedure de leitura da tabela Inutilização
**EVENTOS_ROWS_BATCH** Quantidade de registros a serem recuperados por chamada da procedure de leitura da tabela Evento
**TIPOPROCESSOS_A_EXTRAIR** Lista os tipos de processo que devem ser extraidos (Emissão/Cancelamento/Inutilização/EPEC/Credenciamento)
**ARQUIVOSAIDAZIP** Indica se o arquivo gerado deva estar comprimido em um arquivo Zipado
**EXECUTASOMENTEIMPORTACAO** Indica se será executada somente a etapa importação dos dados dos arquivos txt para o Sybase. Utilizado em caso de falha na etapa de escrita no Sybase para não necessitar repetir a Extração dos dados.
**EPEC_ROWS_BATCH** Quantidade de registros a serem recuperados por chamada da procedure que faz a leitura da tabela EPEC
**VERSOES_A_SEREM_EXTRAIDAS_EPEC** versões de EPEC a serem extraídas
**MANTER_DIAS_ARQUIVOS_BACKUP** A quantidade de dias que os txt gerados na etapa de Extração ficarão disponíveis no servidor.
**DIVIDE_ARQUIVO** Indica se os arquivos gerados na extração serão divididos ao atingir seu limite de tamanho.
**TAMANHO_MAXIMO_ARQUIVO** Indica o tamanho máximo do arquivo gerado pela extração, o valor do campo é interpretado como MegaBytes(MB) pelo sistema.
**EMISSAO_NR_WRITERS** Quantidade de writers do processo Emissão que devem ser executados simultaneamente.
**EVENTOS_NR_WRITERS** Quantidade de writers do processo Eventos que devem ser executados simultaneamente. (obs: os demais estão com valores fixos).
**MULTIPLE_WORKER_QUEUES** Indica se o extrator usará uma ou várias filas em memória para os Workers.
**MULTIPLE_WRITER_QUEUES** Indica se o extrator usará uma ou várias filas em memória para os Writers.
**DIVIDE_ARQUIVO_POR_WRITER** Indica se os arquivos de saída serão divididos pela quantidade de Writers para evitar concorrência de escrita.
**EXTRAI_DATA_E_HORA** Indica se o sistema irá considerar horas e minutos nos parâmetros de data.
**IMPORTAR_ARQUIVOS_PENDENTES** Indica se a aplicação fará importação de arquivos pendentes para o Sybase antes do processamento comum agendado. è uma função de recuperação automática caso a importação tenha falhado no(s) dia(s) anterior(es).
**VERSOES_A_SEREM_EXTRAIDAS_CRED** versões de Credenciamento a serem extraídas (versão ficta criada para não tirar o padrão dos demais processos que sempre possuem versões).
**CRED_ROWS_BATCH** Quantidade de registros a serem recuperados por chamada da procedure que faz a leitura da tabela de Credenciamento
**PROCESSAR_DESDE_ULTIMA_EXTRACAO_COM_SUCESSO** Indica se o processamento será realizado desde a última data de extração de sucesso ou se seguirá a regra simples do D-1.
**DATA_REFERENCIA_ULTIMA_EXTRACAO_COM_SUCESSO** Data de referência dos dados extraídos pela última vez com sucesso

## Agendamento de Execução

A execução do Extrator DW da NFC-e é feita através da ferramenta **Control-M**, cuja manutenção é dada pela equipe **DTI-PCP**.

Dentro do Control-M Foi criado um Job chamado NFC-**E_EXTRATOR_DW** que dispara a aplicação

`"D:\Program Files (x86)\SEFAZ_SP\NFCeExtratorDW\Br.Gov.Sp.Fazenda.DFeDW.NFCeOutConsole.exe"`
 
diariamente as 2h da madrugada (ciclo estipulado no formulário de criação do respectivo do Job no Control-M).


## Logs de Execução

A execução do Extrator DW pode ser monitorada através dos logs de execução existentes no banco **ETL_NFCe**.

Basicamente são divididas em três níveis de detalhamento:

**Tabela Processamento** - Nível menos detalhado, compreende apenas o processo principal (no caso da NFC-e não há extrações específicas como na NF-e, então o único processo macro registrado é o ConsoleNfce).

![TabelaProcessamento.jpg](/.attachments/TabelaProcessamento-72df54e3-2124-4ec9-969b-6517b280bb1e.jpg)

**Tabela Execucao_Processo** - Nível de detalhe intermediário, mostra as etapas de Extração, Importação, Importação Pendentes (recuperação de txt que não foram escritos no Sybase anteriormente), Mover para Pendentes (separação dos arquivos que não tiveram êxito na escrita no Sybase) por Data de Referência e Processo (Emissão/Inutilização/Cancelamento/EPEC/Credenciamento).

![TabelaExecucaoProcesso.jpg](/.attachments/TabelaExecucaoProcesso-f75b9519-093e-44ca-bd3d-935f848c333a.jpg)

**Tabela Log_Execucao_Processo** - Nível mais detalhado de execução. Traz informações por item processado (reader, parser, writer, importação arquivo a arquivo, por exemplo).
A coluna LEXP_DS_TIPO denota o tipo de registro: **Information, Warning, Error** (aqui há detalhamento do problema na coluna LEXP_DS_DESCRICAO respectiva).

![TabelaLogExecucaoProcesso.jpg](/.attachments/TabelaLogExecucaoProcesso-3e14dff8-2fcb-4296-b8a9-faa8ff5e6550.jpg)

## Sybase

Após o processo de extração e geração dos arquivos txt, esses arquivos devem ser carregados para o Sybase via Load Table.
As tabelas do Sybase para as quais serão carregadas as informações extraídas estão elencadas no documento de Layout do Extrator DW da NFC-e.

O acesso ao Sybase pode ser feito pelo software **Interactive SQL - SybaseIQ** (_http://sybase-iq.software.informer.com/download/_)
Os ambientes e suas configurações de acesso estão listados abaixo:

## Desenvolvimento DTI

  User ID: ods
  Password: secfaz#di
  Host: SRVIQDES01
  Port: 2640

## Testes DI

  User ID: di_qualidade
  Password: sefaz123
  Host: SRVIQDES01
  Port: 2640

**Homologação** (atualmente não estamos utilizando esse ambiente, mas ele existe)

  User ID: ods_app
  Password: ods_app
  Host: SRVIQHML01
  Port: 2642

## Produção

  User ID: nfce_extrator
  Password: nfce_extrator_5102
  Host: srv15434 (10.216.27.11) (era srv15405 10.215.0.33 , alterado por ocorrencia 376111)
  Port: 2640

Deve-se colocar a opção Action em "**Connect to a running database on another computer**" conforme se ve abaixo:

![ConexaoSybase.jpg](/.attachments/ConexaoSybase-0b0979f2-7a97-4372-9ae2-1a76e49905ef.jpg)

Uma observação importante é que a manipulação das tabelas no Sybase sempre devem ser precedidas do Owner, pois no caso do Sybase de desenvolvimento (DTI) e o de testes (DI) foi feita uma separação de perfil num mesmo ambiente, quebrando-o apenas pelo Owner.

DTI / Homologãção / Produção -> Owner = ODS
                                         DI -> Owner = DI_Qualidade

Além disso em produção a base está configurada para não aceitar comandos com o Owner implícito. Logo deve-se sempre colocar os comandos no formato "Owner.Tabela".
Exemplo:

    select * from ods.tb_dsod_nfce_tmp_doc_fiscal


Conforme se vê no exemplo acima as tabelas seguem um padrão de nomenclatura "**tb_dsod_nfce_...**" e no caso das que a aplicação DW manipula quase sempre há um "**tmp**" antes do nome que especifica a tabela.
Isso se deve ao fato de que a aplicação Extrator DW apenas alimenta tabelas temporárias, que serão consumidas numa etapa posterior em um processo ETL de responsabilidade da DI. Após a execução desse ETL, os dados dessas tabelas temporárias são apagados a fim de que fiquem prontas para uma próxima carga.

A única exceção de tabela que a aplicação Extrator DW manipula (leitura e escrita) e que não é uma tabela temporária é a **tb_dsod_nfce_controle_execucao**.

`select * from ods.tb_dsod_nfce_controle_execucao​`

Trata-se de uma tabela composta por um **cod_status** que se comporta como uma flag de controle para evitar que o DTI carregue novas informações ao mesmo tempo em que a DI processa os dados de uma carga anterior ou mesmo evitar que seja feita uma carga quando houve alguma falha anterior.

Os códigos que esse campo assumem são:

**0** - o Sybase pode receber uma nova carga do Extrator DW normalmente.

**1** - o Extrator DW está no meio de uma carga para o Sybase. (em caso de falha na carga, o status permanecerá em 1).

**2** - o Extrator DW finalizou a carga para o Sybase com sucesso e a DI pode iniciar a sua etapa de processamento.

**3** - a DI está executando sua etapa de processamento. (em caso de falha no processamento da DI, o status permanecerá em 3).

 

## Tabelas NFC-e Sybase

Abaixo segue a lista atual das tabelas existentes no modelo NFC-e no Sybase:

tb_dsod_nfce_tmp_autor_xml
tb_dsod_nfce_tmp_cred
tb_dsod_nfce_tmp_doc_fisc_inf_adic
tb_dsod_nfce_tmp_doc_fisc_inf_adic_contrib
tb_dsod_nfce_tmp_doc_fisc_inf_adic_fisco
tb_dsod_nfce_tmp_doc_fisc_inf_adic_proces
tb_dsod_nfce_tmp_doc_fiscal
tb_dsod_nfce_tmp_doc_fiscal_dest_loc_entr
tb_dsod_nfce_tmp_doc_fiscal_emit_loc_ret
tb_dsod_nfce_tmp_doc_fiscal_evt_cancel
tb_dsod_nfce_tmp_doc_fiscal_evt_epec
tb_dsod_nfce_tmp_doc_fiscal_evt_gen
tb_dsod_nfce_tmp_doc_fiscal_inutilizacao
tb_dsod_nfce_tmp_doc_fiscal_prod
tb_dsod_nfce_tmp_doc_fiscal_prod_comb
tb_dsod_nfce_tmp_doc_fiscal_prod_di
tb_dsod_nfce_tmp_doc_fiscal_prod_dia
tb_dsod_nfce_tmp_doc_fiscal_prot_rec
tb_dsod_nfce_tmp_doc_fiscal_ref_nfe
tb_dsod_nfce_tmp_doc_fiscal_total
tb_dsod_nfce_tmp_doc_fiscal_transp
tb_dsod_nfce_tmp_doc_fiscal_transp_lac
tb_dsod_nfce_tmp_doc_fiscal_transp_vol
tb_dsod_nfce_tmp_forma_pagto
tb_dsod_nfce_tmp_inf_supl
tb_dsod_nfce_tmp_nve
tb_dsod_nfce_tmp_transmissor

Deve-se tomar muito cuidado para não confundir com as tabelas do sistema NFCE, pois infelizmente na modelagem do Sybase o início do nome das tabelas de ambos os sistemas (NFC-e e NFCE) são idênticos ("tb_dsod_nfce").
Para se certificar de que alguma tabela realmente pertence à NFC-e o ideal é sempre consultar o arquivo de Layout NFC-e Extrator mais atual (já citado no subtópico Layout).

## Comandos Sybase (Interactive SQL)

Os comandos aceitos pelo Sybase estão listados no seguinte endereço:

http://infocenter.sybase.com/archive/index.jsp?topic=/com.sybase.help.ase_15.0.commands/html/commands/commands3.htm

Uma dica importante de uso cotidiano do Interactive SQL é habilitar a opção **Commit After Every Statement**, pois sem isso é comum executar alguns comandos na interface Interactive SQL e o resultado não aparecer atualizado.

Para habilitar a opção deve-se dentro do Interactive SQL ir ao menu Tools >> Options >> Sybase IQ >> Commands e marcar a checkbox correspondente:

![ToolsOptionsSybase.jpg](/.attachments/ToolsOptionsSybase-8f096076-c152-44d5-8908-83e9367e943f.jpg)

Outra dica bastante utilizada é a de executar somente a parte selecionada na janela de comandos:

F9 - Executa somente a seleção;
F5 - Executa todos os comandos presentes na janela de edição de query.

 

## Falhas na Execução e Modos de Reprocessamento

Há alguns casos em que o processo de carga do DW poderá sofrer algum tipo de falha e necessitará de um reprocessamento a ser configurado e realizado com intervenção da equipe de desenvolvimento.

Os tipos de falhas possíveis podem ser:

### 1) Falha na Extração
Quando a aplicação tem algum tipo de erro durante a etapa de Extração, os arquivos txt que deveriam ser gerados podem estar incompletos ou até mesmo corrompidos. Portanto nesse caso o ideal é fazer o reprocessamento desde o início da data referência que sofreu alguma falha, avaliando o log de execução do DW para saber com que parâmetros se deve preencher a configuração.

- Os parâmetros a seguir devem ser modificados de acordo com a data e tipos de processos a serem reprocessados:

**EXTRAI_DATA_ESPECIFICA** deve ser marcada como Ativar (true)
**DATA_INICIAL_ESPECIFICA** deve ser preenchido com a data no formato AAAA-MM-DD relativa ao ínicio do período a ser reprocessado.
**DATA_FINAL_ESPECIFICA** deve ser preenchido com a data no formato AAAA-MM-DD relativa ao fim do período a ser reprocessado.
**TIPOPROCESSOS_A_EXTRAIR** selecionar os tipos de processo que precisam do reprocessamento
**EXECUTASOMENTEIMPORTACAO** certificar-se que esta opção está marcada como Desativar (false)

&nbsp; - Após essa configuração de parâmetros deve-se entrar em contato com o pessoal da DI responsável pelo DW (normalmente o Felipe Augusto Rodrigues da Silva) para alinhar o reprocessamento, solicitando ao mesmo que limpe as tabelas temporárias do Sybase relativo a NFC-e (caso haja algum dado lá) e que troque a flag de controle de execução para o valor de cod_status = 0.

&nbsp; - Depois disso deve-se solicitar ao pessoal do Control-M (via incidente) para que executem manualmente o job NFCeExtratorDW uma vez.

&nbsp; - Confirmando que dessa vez o processamento ocorreu com sucesso, avisar a DI (Felipe) que a carga já foi realizada para que ele dê andamento na fase ETL da DI.

&nbsp; - Com o processamento da DI realizado, retornar a configuração de parâmetros do DW para a situação anterior (rotina diária de extração).

 

### 2) Falha na Importação 

Em caso de falha somente na segunda etapa da carga diária (escrita no Sybase, ou Importação), os arquivos txt gerados são movidos para um diretório específico chamado **Pendentes**, dentro de cada diretório raiz relativo ao processo extraido.
Por padrão esses arquivos serão processados automaticamente no dia seguinte (quando a flag **IMPORTAR_ARQUIVOS_PENDENTES** estiver setada como True).
O caso mais comum de falha nessa etapa é ocasionado pela flag de controle de execução existente no Sybase contar com um valor diferente de 0 ou 1. Verificado que este seja o problema, é recomendado avisar a DI para que analise se há algum motivo importante para que a flag não tenha voltado automaticamente para o seu estado esperado, e assim que possível trocar a flag para o valor correto para que a carga do dia seguinte ocorra sem problemas.

Caso o reprocessamento desses arquivos pendentes necessite ser realizado antes da próxima carga agendada, deve-se seguir os seguintes passos:

&nbsp; - Configurar os seguintes parâmetros:Configurar os seguintes parâmetros:

**EXTRAI_DATA_ESPECIFICA** deve ser marcada como Ativar (true)
**DATA_INICIAL_ESPECIFICA** deve ser preenchido com a data no formato AAAA-MM-DD relativa ao ínicio do período a ser reprocessado.
**DATA_FINAL_ESPECIFICA** deve ser preenchido com a data no formato AAAA-MM-DD relativa ao fim do período a ser reprocessado.
**TIPOPROCESSOS_A_EXTRAIR** selecionar os tipos de processo que precisam do reprocessamento
**EXECUTASOMENTEIMPORTACAO** certificar-se que esta opção está marcada como Ativar (true)

&nbsp; - Após essa configuração de parâmetros deve-se entrar em contato com o pessoal da DI responsável pelo DW (normalmente o Felipe Augusto Rodrigues da Silva) para alinhar o reprocessamento, solicitando ao mesmo que limpe as tabelas temporárias do Sybase relativo a NFC-e (caso haja algum dado lá) e que troque a flag de controle de execução para o valor de cod_status = 0.

&nbsp; - Depois disso deve-se solicitar ao pessoal do Control-M (via incidente) para que executem manualmente o job NFCeExtratorDW uma vez.Depois disso deve-se solicitar ao pessoal do Control-M (via incidente) para que executem manualmente o job NFCeExtratorDW uma vez.

&nbsp; - Confirmando que dessa vez o processamento ocorreu com sucesso, avisar a DI (Felipe) que a carga já foi realizada para que ele dê andamento na fase ETL da DI.

&nbsp; - Com o processamento da DI realizado, retornar a configuração de parâmetros do DW para a situação anterior (rotina diária de extração).
 

Após o reprocessamento com sucesso, os arquivos que estavam no diretório de Pendentes serão movidos para o diretório **Processados**

 

### 3) Falha no Processamento ETL (essa parte é de responsabilidade da DI)

Este último caso foge do escopo do Extrator DW da NFC-e, mas pode ocorrer como uma solicitação excepcional da DI. Trata-se de um pedido para reprocessar a carga devido a falha no processamento ETL de responsabilidade da DI.

Nessa situação, como o processamento do NFCeExtratorDW aconteceu com sucesso, os arquivos extraidos estarão no diretório **Processados**, dentro do diretório raiz onde ficam os arquivos txt gerados durante a Extração.

Para evitar que se faça novamente a leitura das bases da NFC-e e extração de informações, o ideal é reaproveitar os arquivos relativos à data solicitada para reprocessamento (desde que ainda estejam lá, visto que há uma configuração de quantidade de dias que tais arquivos devem ser armazenados - **MANTER_DIAS_ARQUIVOS_BACKUP**).

Caso os arquivos necessários ainda estejam no servidor, deve-se abrir um incidente para DTI-Windows solicitando que estes arquivos sejam movidos para o diretório **Pendentes** e então seguir os passos listados no reprocessamento **"2) Falha na Importação"**.

Caso os arquvios já não se encontrem mais no diretório **Processados** (por terem diferença de data maior que a quantidade de dias configurados no parametro MANTER_DIAS_ARQUIVOS_BACKUP), deve-se realizar o reprocessamento completo, conforme descrito nos passos do item **"1) Falha na Extração"**.