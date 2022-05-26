# ​NT 2018.001 - Emitente Pessoa Física (CPF) com Inscrição Estadual​

## Requisitos

### Ocorrência

_Ocorrência 437115:_ 

### Link Nota Técnica

​https://www.nfe.fazenda.gov.br/portal/exibirArquivo.aspx?conteudo=9OYnBfONR2s=

## Alterações em Regras de Validação

### Regras implementadas diferente da NT:

- Eventos RVs G04e, G05e, G06: essas validações aparecem separadamente para eventos de Cancelamento e Carta de Correção, mas a implementação foi feita no BlocoG de eventos, que é comum para todos os evento

  **Obs:** a RV G08 foi feita também no BLocoG, mas dentro de switch por tipo de evento, deixando desse modo código duplicado nesse switch. Avaliar futuramente unir os códigos no mesmo "case" para evitar a duplicação.

### Regras não implementadas:

- Alterações em RV que tratam de destinatário (p.ex: cStat 232) foram ignoradas (será necessário rever, pois essas validações usam regras do Alfonso sobre tratamento de IE)
- C02a-08, A03-10 e C02a-14 não serão implementadas, conforme e-mails do Wagner anexados à Ocorrência
- J03 (cStat 217): a consulta atualmente é feita por idNFe ( a proc busca o campo "chNFe" da [Evento_Constraint]), não importando se o emitente foi CNPJ ou CPF.
- 5E17-50: será feita no escopo da Ocorrência 438377 (NT 2019.001)
- RVs de Carta de Correção que já implementadas na NFe por RVs da parte genérica de eventos (normalmente a numeração está como aparece para o evento Cancelamento): 
---- G05e (cStat 617): a validação dessa RV é feita no código pela RV G04e (já alterada para o evento de Cancelamento)
---- G06 (cStat 494):  a validação dessa RV é feita no código pela RV G04e (já alterada para o evento de Cancelamento)
---- G05h (cStat 249): essa RV já existe na forma de G04h (na NT aparece como se as validações de Cancelamento e Carta de Correção fossem distintas, mas no código da NFe acaba sendo tudo junto no BlocoG_Eventos)

### Regras removidas:

- C02a-10: a versão atual dessa RV (cStat 407) será renomeada para C02a-14 e removida do código

### Regras alteradas pela NT, mas não encontradas no código da NFe:

- 2AB08-40 (cStat 468) que trata de EPEC, não existia na NFe (somente na NFC-e) e por isso está sendo ignorada (Wagner ficou de enviar e-mail para grupo XML confirmar,pois parece não ter relação com o restante da NT)
- I09 (cStat 241) mesmo  comentpario da regra acima.
- GA02 (cStat 503): o cStat existe, mas a RV não existe
- P12-32 (cStat 266): RV do evento EPEC, não existe com esse nome, e a mesma regra foi removida com o nome GA01 (como se fosse regra de Evento de Cancelamento)

## Alterações em Banco​
### Base NFe_In
- SP NFeIn.usp_LoteStatusSelect​ : alterar para retornar CPF
Obs:
  - no momento, estou assumindo que o campo se chamará CNPJ_CPF e terá prefixo "CPF"  quando necessário
  - _após conversa com o Sávio e o Marcos de Paula (ex-NFP), decidimos seguir o exemplo da NFP, que nos casos de varchar(14) usa o tamanho do texto, 11 ou 14, para decidir se é CPF ou CNPJ. Adicionalmente, Sávio sugeriu manmter o campo com o"CNPJ" para não correr o risco de quebrar outros sistemas (fora de nossas solutions) que porventura usarem a tabela Lote (eu acho improvável, mas é algo a ser considerado - vou verificar se o Leka pode ajudar a descobrir se tem outro sistema que acesse essa tabela)._ 

### Base NFe_Out

- Tabela [NFeEventos].[DadosApoio] : criar campo [CPFEmit] VARCHAR(11) nullable (_avisar time da NFP que o campo [CNPJBaseEmit] poderá vir nulo e haverá um novo campo_); ​
- SP [NFeOut].[usp_NFeInsert]​ : no insert da NFeEventos.DadosApoio, caso a série seja [910-969] inserir nulo no campo [CNPJBaseEmit] e inserir SUBSTRING(@idNFe, 10, 11)

### Base CCC
- SP [NFe].[usp_ConsultaCNPJ] : precisa alterar para receber também um CPF (hoje recebe um CNPJ)
- Tabela(s) X, Y, Z : verificar alterações necessárias

### Base CADESP
- SP [pr_Deca_ConsCadPorCnpj2] : precisa verificar com o time do CADESP como será tratado o caso de contribuinte CPF (se usará a mesma SP ou se haverá outra)