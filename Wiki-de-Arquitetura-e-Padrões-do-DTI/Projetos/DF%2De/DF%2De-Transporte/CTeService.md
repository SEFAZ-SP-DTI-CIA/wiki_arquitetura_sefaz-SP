##CTeService
###
O CTeService é um serviço windows que executa vários serviços em um só, conforme aparece descrito abaixo.
###
Homologação: Sefaz.CTeService (SRV11489 e SRV11490)
Produção: Sefaz.CTeService (SRV11544, SRV11545, SRV11546, SRV11547, SRV11548, SRV11549, SRV11550 e SRV11551)
###
O CTeService executa quatro processamentos:

* Autorização de CTe

  Basicamente recebe xml's gravados na base CTe_In pelo serviço
  que emite o CTe, processa e armazena os documentos autorizados.

* Autorização de evento de CTe
  
  Faz um processo análogo a Autorização de CTe mas para autorização
  de eventos.

* Gravar na Sonda de tempos em tempos, enquanto o serviço estiver
ativo, um registro de processamento sem problema. 

* Executa o batimento entre a base de contribuintes do Cadesp e do CTe

  Elabora a partir do CTe, uma lista contendo cnaes obrigatórios, 
  contribuintes obrigatórios SINIEF, contribuintes voluntários e de   
  ofício.
  Submete a lista assim obtida ao Cadesp que gera uma lista de 
  contribuintes com cnae obrigatório a partir da lista enviada pelo
  CTe. Depois disso, o Cadesp usa a lista de contribuintes com
  cnae obrigatório e as outras duas listas passadas pelo CTe,
  contribuintes obrigatórios SINIEF e contribuintes voluntários ou de 
  ofício. Dessa lista geral de contribuintes, o Cadesp gerar uma 
  nova lista com os dados dos contrintes no Cadesp e devolve para o
  CTe.
  Finalmente, o CTe usa a lista devolvida pelo Cadesp para incluir,
  alterar, credenciar ou descredenciar contribuintes. Lembrando que
  o credenciamento é feito se o contribuinte mudou status de inativo
  para ativo ou se foi incluído no Cadesp com status de ativo.
  Da mesma forma, o descredenciamento é feito se o status mudou
  de ativo para inativo ou se o contribuinte do CTe não está na
  lista do Cadesp. Nesse último caso, o contribuinte é inativado
  no CTe.  



 