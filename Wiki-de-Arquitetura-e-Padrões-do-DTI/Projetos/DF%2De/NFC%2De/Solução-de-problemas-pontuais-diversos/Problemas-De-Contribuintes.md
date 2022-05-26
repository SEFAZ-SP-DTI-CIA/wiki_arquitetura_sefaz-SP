## Problemas de Contribuintes

-- Caso o contribuinte alegue não conseguir acessar os WebServices ou enviar NFC-e/EPEC:

1) Solicitar ao contribuinte:
1.1) que ele acesse o site do webservice (https://nfce.fazenda.sp.gov.br/ws/NfeAutorizacao.asmx) via browser, usando a _mesma máquina_ e o _mesmo certificado_ usado na transmissão do XML
1.2) se possível, que ele envie o printscreen da tela
(caso o acesso seja feito com sucesso, sabemos que não é problema no acesso/certificado.)

2) Caso o teste 1 indique problema no certificado:
2.1) solicitar ao contribuinte o certificado usado para transmissão
2.2) investigar possíveis problemas no certificado (raiz, LCR, etc) e nossos servers (acesso ao link da LCR, firewall, proxy, certificado raiz não instalado, etc)
2.3) adicionalmente, solicitar ao contribuinte que envie uma NFC-e de teste ao webservice NF-e: (https://nfe.fazenda.sp.gov.br/ws/NfeAutorizacao.asmx) e reporte o resultado
       O contribuinte não precisa se preocupar em autorizar porque a NF-e vai rejeitar o envio por ser uma NFC-e.
2.4) alternativamente, solicitar ao contribuinte que teste o certificado que está usando no envio (o certificado dele, do contribuinte) no site:
       https://www.identityprd.fazenda.sp.gov.br/v001/Sefaz.Identity.STS.Exemplo/Account/Login.aspx​
       (manter selecionado como "Bancário")

3) Se o erro no sistema continuar, mesmo após o certificado estar OK:
3.1) solicitar o XML de request que ele está enviando e a resposta que está tendo (ou print screen da resposta caso não seja XML)
3.2) investigar o problema a partir do cStat ou da mensagem de Exception ou do erro http (dependendo do que o contribuinte enviar)