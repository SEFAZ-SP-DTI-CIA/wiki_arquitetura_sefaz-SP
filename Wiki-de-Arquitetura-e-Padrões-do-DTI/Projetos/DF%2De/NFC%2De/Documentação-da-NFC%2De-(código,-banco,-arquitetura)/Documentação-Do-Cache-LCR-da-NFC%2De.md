## O que é:

Um serviço que mantém em banco todas as LCR dos certificados na tabela de certificados.

## Requisitos:

A máquina onde roda o serviço precisa do pacote "Microsoft Visual C++ 2005 Redistributable Package".

A versão atualmente usada nos servidores dá suporte a VS 2015,2017,2019 e foi obtida no link : https://support.microsoft.com/en-us/help/2977003/the-latest-supported-visual-c-downloads

### Lib msvcr120.dll / msvcr120d.dll

Normalmente os servidores possuem a lib C:\Windows\System32\msvcr120.dll , necessária para a CryptlibHelper.dll . Porém, quando se compila em modo debug, a lib exigida é a msvcr120d.dll ; essa normalmente não existe nos servidores e deve ser copiada manualmente caso haja necessidade de rodar versão de debug em servidores.

## Banco:

As LCR são mantidas em 5 tabelas em um schema próprio dentro do mesmo banco onde fica a tabela [Certificado] (no nosso caso, seria [NFCe_Out]):

[LCR].[Certificado] – cópia da [NFCe_Out].[Certificado], inclui um campo "PkeyLCR" (refere-se a pKey de [LCR].[ListaRevogados]) e uma pKey sem uso externo

[LCR].[ListaRevogados] – lista das LCR, inclui data de expiração e outras informações de controle (essa lista que define a pKey)

[LCR].[LCRQueue] – lista de LCR ainda não processadas (por pKey)

[LCR].[PontosDistribuição] – lista das URLs que fornecem as LCR (por pKey)

[LCR].[Revogados] – certificados revogados  (por pKey)

Além disso, a tabela [NFCeOut].[Certificado] tem um novo campo “pKeyLCR” que é NULL (se o certificado ainda não foi processado pelo serviço) ou 1 (se já foi processado).

![BancosLCRchaves_V2.png](/.attachments/BancosLCRchaves_V2-3712ff2b-73a8-4071-9013-acb9cfc6592e.png)

### Serviço:

É um WindowsService (LCRService) que roda em uma máquina separada dos demais serviços (no caso do CT-e, roda junto com Integra).

O serviço trabalha da seguinte forma:

# 

_Thread 1_: VerificarCertificadosNaoMarcadosNFCeOut (roda a cada _3 segs_)

1) Lê todos os certificados da tabela [NFCeOut].[Certificado]

2) Para cada certificado, verifica pKeyLCR

3) Caso pKeyLCR == NULL, copia o certificado para [LCR].[Certificado], e define pKeyLCR = 1 em [NFCeOut].[Certificado]

&nbsp;

4) _Thread 2_: ​​VerificarCertificadosLCRqueNaoTemLCRsalvaEmBD (roda a cada _1 seg_)

Apaga os certificados vencidos da tabela [LCR].[Certificado], bem como os registros relacionados a esses certificados nas tabelas [LCR].[ListaRevogados], [LCR].[LCRQueue], [LCR].[PontosDistribuicao] e [LCR].[Revogados]​

Para cada certificado de [LCR].[Certificado], verifica se tem LCR

Se tem, atualizar pKeyLCR

Se não tem, tenta extrair o LCR e inclui em [LCR].[ListaRevogados]

&nbsp;

2) _Thread 3_: VerificarListaRevogadosParaAdicionarEmQueue (roda a cada _3 segs_)

1) Lê todas as LCR em [LCR].[ListaRevogados]

2) Se LCR expirada ou não baixada, insere pKey em [LCR].[LCRQueue]

&nbsp;

_Thread 4_: EnfileirarLCRsDaQueue (roda a cada _1 seg_)

1) Lê todas as pKeys em [LCR].[LCRQueue]

2) Cria um objeto com a URL (disponível em [LCR].[ListaRevogados]) de cada pKey lida e enfileira em memória

&nbsp;

_Thread 5_: AtualizarLCRs (usa semáforo, processamento contínuo)

1) Para cada objeto em memória:

2) Incrementa o número de tentativas de download em [LCR].[ListaRevogados]

3) apaga a entrada de [LCR].[LCRQueue] da pKey do objeto

4) busca a LCR na URL do objeto

5) atualiza [LCR].[ListaRevogados] com as informações retornadas na consulta à URL

&nbsp;

Obs: as exceções são logadas em BD e event viewer e adicionadas a um dicionário em memória, juntamente com o minuto em que foram geradas.

Caso as exceções ocorram repetidamente dentro de um intervalo de 20 minutos, serão logadas pela Thread 6 (abaixo).

&nbsp;

_Thread 6_: LimparLogsExcecoesRepetidas (roda a cada 20 minutos e uma vez caso o serviço seja interrompido)

1) Para cada mensagem de exceção repetida (guardada em um dicionário):

2) Caso a mensagem tenha sido gerada há mais de 20 minutos, apaga a mensagem do dicionário.

3) Caso a mensagem apagada tenha mais de 1 ocorrência, loga a mensagem com o número de ocorrências

4) Caso o serviço seja interrompido, todas as mensagens com mais de 1 ocorrência são logadas com o número de ocorrências, independente de quanto tempo atrás tenham sido geradas.

&nbsp;

**Como é usado o “Cache” de LCR em Banco:**

Na validação BlocoE (WindowsService Autorizador), antes de rodar o X509Chain.Build, a aplicação verifica

&nbsp; - se já existe a LCR em banco, E

&nbsp; - se a flag “CERTIFICADO_VALIDAR_LCR_OFFLINE” está true na tabela ConfigSistema, E

&nbsp; - se a cadeia completa está disponível no certificado

Caso tudo afirmativo, a aplicação verifica, para cada item da cadeia, se não existe revogação no banco.

Caso contrário, faz verificação ONLINE normal.

&nbsp; 

Isso é feito em uma classe “Certificado” que encapsula várias chamadas a X509Chain.

&nbsp;  

**Vantangens:**

1) _menor tempo de acesso_: assumindo que a LCR está no banco, acessar o banco na intranet é mais rápido que acessar o servidor pela internet.

2) _resistência a problemas no link das ACs_: Segundo o Vicentini, “(...)uma certificadora que ficou fora por 3 horas, a certisign. A NF-e chegou a levar 90 segundos de média pra processar os lotes. No CT-e não passou de 400ms” mas ele também não tem certeza de quanto desse ganho foi devido apenas ao cache em banco, já que o IIS continuou tendo que acessar a LCR pela internet.

**Desvantagens**:

1) _custo de esforço_: além das alterações no código do BlocoE, é preciso criar novos elementos na BLL, DAL, Entity e Facade, 4 novas tabelas e alteração em tabelas já existentes (no mínimo, alterar a tabela NFCeOut.Certificado para incluir a pKeyLCR)

2) _custo de infra_:  para seguir o modelo do CT-e, precisamos de uma máquina separada para rodar esse serviço. Além disso, _talvez_ precisemos de um banco separado para evitar conflito com o serviço principal da NFC-e.

3) _incerteza_: após implementado, não sabemos ainda qual seria o ganho a longo prazo, já que nossa tabela terá cerca de 1 milhão de certificados, pode acontecer do serviço não atualizar as LCR a tempo e acontecer do serviço principal fazer acessos inúteis a tabela de LCR.  Outra incógnita é se o tempo adicional de verificar o banco será realmente melhor que o cache em memória já existente para LCR (um revés do cache em memória é que é duplicado em cada máquina que rode o serviço NFC-e, enquanto o BD é centralizado para todas as instâncias do serviço, mas se o banco for muito grande e ficar frequentemente desatualizado, acaba compensando menos).

&nbsp; 

**Outras observações**:

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; O Savio disse que eles pretendem analisar implementar esse serviço de Cache LCR em BD, mas somente no futuro, pois eles tem problemas mais urgentes no momento.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; O Vicentini acha vantajoso manter esse cache para CT-e, mas ele também não sabe exatamente quantificar o ganho.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Todas as consultas são feitas com índice, portanto levam um tempo mínimo. Talvez tenhamos que modificar o tipo do thumbprint no certificado. No nosso caso, é char(40) enquanto que no CT-e usa-se varbinary(64) , mas não sei se faz alguma diferença..
