##    Propósito

   A Quase-Autorização é o processo de se enviar uma NFC-e para o ambiente de modo que ela vai passar com sucesso por todas as validações (exceto as relacionadas com CADESP e relativas a certificados) mas será rejeitada no final com código 999 e com texto complementar "Rejeição: Erro não catalogado (Quase-autorização Empresa Fake DTI)"

   Isso permite que sejam enviadas notas para o ambiente de produção tendo a certeza de que ela não será autorizada. Se o retorno for o mencionado acima, temos certeza que ela passou por todas as autorizações (exceto CADESP e certificados). Se for outro retorno, deve-se analisar o resultado.

   A Quase-Autorização foi implementada tanto na autorização síncrona quanto na assíncrona.

 

##    Validações puladas

   A funcionalidade é restrita a um certificado específico, o "Empresa Fake Teste DTI 99171171171115". Esse certificado não é ICP Brasil por ter sido gerado aqui no DTI. Portanto as validações de verificação de certificado ICP Brasil são ignoradas para esse certificado, o código verifica o thumbprint desse certificado e pula a validação se for o certificado em questão (ponto válido tanto para o certificado de transmissão quanto para o de assinatura da nota).

   A validação de CADESP também é pulada pelo fato de esse CNPJ 99171171171115 não estar no CADESP, é um CNPJ de teste.

   A verificação se o certificado é o da Quase-Autorização é feita pelo batimento do thumbprint e também do CNPJ - todos colocados no web.config dos web-services e no app.config dos serviços de autorização assíncrona da NFC-e.

 

##    SOAP-UI

    Para fazer uma Quase-Autorização no SOAP-UI, deve-se:

- usar o certificado "Empresa Fake Teste DTI 99171171171115" como certficado de transmissão para o envio das requisições ao sistema NFC-e.

- usar a tag <usarCNPJFake>true<usarCNPJFake/> no assinador de notas para forçar a utilização desse certificado na assinatura da NFC-e

 

    No corpo da NFC-e, deve-se:

- usar o CNPJ 99171171171115 na chave de acesso,

- usar o mesmo CNPJ na tag <emit> (onde convém colocar <xNome>Empresa Fake DTI</xNome>)

- usar IE emitente <IE>000000000012</IE> (isso porque o contribuinte armazenado na tabela ContribuintesHabilitados tem esse valor de IE)

  

##    Extensões

&nbsp; O conceito de Quase-Autorização foi estendido para outros requests:

&nbsp; 1) Autorização de EPEC: para Quase-Autorizar um EPEC, deve-se utilizar procedimentos semelhantes ao da NFC-e quanto à assinatura (usar a tag usarCNPJFake), à transmissão (usar o certificado mencionado) e no corpo do EPEC acertar o CNPJ e IE com mesmos valores da NFC-e.

&nbsp; 2) Inutilização: para quase-autorizar uma inutilização deve-se utilizar procedimentos semelhantes ao EPEC, exceto quanto ao IE que não é usado.

&nbsp; 3) Consulta Protocolo: não existe 'autorização' de consulta, pois nenhum documento é armazenado nesse serviço. nesse caso o que foi feito foi somente permitir chamar esse serviço utilizando-se como certificado de transmissão o "Empresa Fake Teste DTI 99171171171115".

&nbsp; 4) Cancelamento: o código de Quase-Autorização foi implementado para cancelamento (na verdade é comum para Cancelamento e EPEC), mas não faz sentido usar; um cancelamento somente cancela nota de uma determinada chave, que não poderia ter sido autorizada.