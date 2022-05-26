Para a criação de Deployment Pool em servidores de desenvolvimento e de testes, há templates específicos de RDM no Remedy:
- "Criação de Deployment Pool em servidor de desenvolvimento"
- "Criação de Deployment Pool em servidor de testes (QA)"

Nos servidores de homologação e de produção, os Deployment groups (deployment pool) são criados em [RDM de configuração de deploy automatizado](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Operações/Solicitações-de-infraestrutura/Requisição-de-Mudanças/RDM-para-configuração-de-Deploy-Automatizado).

[[_TOC_]]

#Levantamento de informações
Antes de iniciar uma RDM para a criação do deployment pool, cria-se um pipeline de release que irá utilizá-lo.

Além disso, a equipe de desenvolvimento em conjunto com as equipes de operações, através de emails, deverão buscar as informações sobre o destino do deploy, que pode ser um servidor ou um balance:
1. Link do host
2. Tipo de hospedagem: 
   - ( ) Balance NLB   ( ) Balance Big-Ip   ( ) Servidor

3. Endereço do balance BigIP, quando houver.
4. Relação dos servidores 
    - SRVnnnnn - IP: 10.216.nn.mmm
5. Nome sugerido para o Deployment Pool 
6. Link do pipeline de release
   
```
Exemplo: 
1. Link do host: webservicesdes4.intra.fazenda.sp.gov.br
2. Tipo de hospedagem: 
    ( ) Balance NLB   ( ) Balance Big-Ip   (X) Servidor
3. Relação dos servidores 
    - SRV72447 - IP: 10.217.13.71
4. Nome sugerido para o Deployment Pool: webservicesdes4.intra
5. Link do pipeline de release: 
https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Layout_Padrao/_releaseDefinition?definitionId=4&_a=definition-pipeline

```

#RDM
A partir disso, a criação do Deployment Pool deve ser solicitada por RDM no Remedy, com a utilização do template "Criação de Deployment Pool em servidor de desenvolvimento".

![image.png](/.attachments/image-9887ad9f-062d-4447-945b-a02160cc7198.png)

#Concordância
Para essa RDM consulte, por email, a equipe de operações, com antecedência, para obter informações para esse preenchimento e a concordância formal delas para a execução.
- Windows Web - dtiwinweb@fazenda.sp.gov.br - para verificação dos servidores, balance, deployment groups e firewall.
  >Sugestão de mensagem:
"Prezados colegas, solicitamos orientações e concordância sobre a criação de um Deployment Pool em um servidor de desenvolvimento.
A aplicação será acessível pelo endereço: [link previsto para a aplicação]
Já está iniciado o pipeline: [link para o pipeline de deploy automatizado]."

Anexe o email de concordância à RDM.

#Liberação
A RDM a ser criada exigirá uma Liberação no Remedy.

Se for necessário criar uma liberação, acesse o [BMC Remedy](https://sefaznetvirtual.intra.fazenda.sp.gov.br/arsys/forms/arsapp/SHR:LandingConsole), no menu lateral à esquerda (Aplicações), selecione "Gerenciamento de Liberação" e "Nova Liberação"

Preencha a liberação com informações de identificação do projeto e seu responsável, com algumas sugestões:
- Deixe em branco o campo "Template"
- Sumário: "Deployment Pool"
- Notas: "Criação de Deployment Pool em servidor de desenvolvimento/QA"
- Justificativa de negócio: "Estratégia Corporativa"
- Tipo de liberação: "Evolutiva"

Avance para o próximo estágio até chegar em "Implantação".
![Liberacao_Implantacao.png](/.attachments/Liberacao_Implantacao-6d6af060-ffc8-424d-9e84-f91b99cd2d49.png =600x)

#RDM
Para criar uma RDM, acesse o [BMC Remedy](https://sefaznetvirtual.intra.fazenda.sp.gov.br/arsys/forms/arsapp/SHR:LandingConsole).
Há duas formas de criar a RDM:
- no menu lateral à esquerda (Aplicações), selecione "Gerenciamento de Mudança" e "Nova Mudança"
- na liberação criada, selecione no menu "Criar Solicitação Rel.", no lado esquerdo da página, a opção "Mudança de Infraestrutura":
![Liberacao_Criar_RDM.png](/.attachments/Liberacao_Criar_RDM-0fc0807a-32b9-41b6-bdd8-db012ac06db3.png =600x)

##Identificação
Preencha a RDM com informações de identificação do projeto e seu responsável, com algumas recomendações:
- Serviço: procure o nome do projeto ou da aplicação
- Template: "Criação de Deployment Pool em servidor de desenvolvimento" ou "Criação de Deployment Pool em servidor de testes (QA)"
- Sumário: "Criação de Deployment Pool em servidor de desenvolvimento/testes (QA)"
- Notas: copie e cole as [informações levantadas acima](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/394/RDM-para-cria%C3%A7%C3%A3o-de-Deployment-Pool?anchor=levantamento-de-informa%C3%A7%C3%B5es)
- Classe: **Normal**
- Motivo da mudança: Nova Funcionalidade
- Gera indisponibilidade: Não
- Acompanhamento: Sim
- Ambiente: "Desenvolvimento"
- Grupo do gerente: nome do projeto no Remedy
- Gerente de Mudança: alguém, nessa relação de nomes, que pode autorizar a RDM

Salve a RDM, antes de continuar o preenchimento.

Se a RDM foi criada a partir da liberação, o Remedy retornará para ela.
Nesse caso, clique na página "Relacionamentos" da liberação e observe que já há uma ligação para a RDM, ainda em rascunho.
![Liberacao_Relacionamentos.png](/.attachments/Liberacao_Relacionamentos-569fd3b5-b983-4955-ac40-600c14e536c1.png =500x)
Com um duplo-clique do mouse sobre a mudança, a RDM se abre novamente.

##Análise de risco
Para preencher a análise de risco, clique no botão com um ícone de uma balança.
![Analise_de_Risco_botao.png](/.attachments/Analise_de_Risco_botao-326c4fe0-ab5c-4cb2-83fe-ebb69ca0d0a0.png =200x)
Preencha os campos em branco e salve.
![Analise_de_Risco.png](/.attachments/Analise_de_Risco-c7943077-9e7e-462b-b56a-ea3c80581e68.png =600x)

##Tarefas
As tarefas exigem o preenchimento do campo "Notas" com as informações levantadas ou a RDM será recusada na reunião de mudanças.
Anexe os emails de concordância à RDM.

### Abertura de firewall
- Notas
```
Abertura de firewall dos servidores [relação dos servidores da aplicação] à porta 443 do ads e à mesma porta dos servidores de balance (BigIP ou NLB).

  Regra A: Acesso ao ADS
 
  - Servidores de Origem: [relacionar os servidores]
  - Servidores de Destino: https://ads.intra.fazenda.sp.gov.br
  - Porta: 443

  Regra B: Acesso ao BIG-IP (se houver balance do tipo BIG-IP)
  - Servidores de Origem: [relacionar os servidores]
  - Servidores de Destino: Servidores do IP BIG-IP
  - Porta: 443
```

### Criação do Deployment Pool
- Notas
```
- Relação dos servidores 
    - SRVnnnnn - IP: 10.216.nn.mmm
- Nome sugerido para o Deployment Pool 
- Link do pipeline de release
```
### Validação do Deployment Pool
Preencha o campo "Notas" com as informações do pipeline de release. 

```
[link para o pipeline em modo de 'Edit']
```

##Data/Sistema
A data agendada deve ser, no mínimo, uma semana após a RDM ser preenchida, salva e avançada para o estágio de "Solicitação de Autorização".
Recomenda-se que a data final seja uma semana após o início, para que haja tempo para o atendimento de todas as tarefas e de eventuais testes.

##Aprovação
Após salvar a RDM, ao clicar no botão "Próximo Estágio", estando tudo em ordem, a RDM passa para o estágio de "Solicitação de Autorização".
Um email será enviado automaticamente pelo Remedy ao solicitante:
```
A Mudança de Infraestrutura CRQ000000999999 foi enviada. Criação do Deploy Automatizado HML
...
Confirmação de Recebimento:
A Mudança de Infraestrutura CRQ000000999999 foi enviada em seu nome por .... 
Status: Solicitação de Autorização
Impacto: 4-Secundário/Localizado
Urgência: 4-Baixa
Sumário: Criação do Deployment Pool

Não responda este e-mail. Se for preciso, entre em contato com o Suporte Técnico.
```

Com isso, ela estará na pauta da próxima reunião da equipe de mudanças, programada semanalmente às quintas-feiras, dez da manhã, se a solicitação for criada com pelo menos três dias de antecedência.

O demandante será convidado a participar da reunião por email. Se não puder participar, a mudança será transferida para a próxima reunião até que uma pessoa designada pela equipe de desenvolvimento compareça para eventuais esclarecimentos.

#Implantação
Aprovada a RDM, as tarefas serão executadas na ordem em que estão relacionadas.

- A equipe de segurança irá abrir a porta 443 do ads e do BigIP para os servidores onde serão implantados os Deployment Groups.

- A equipe de operações (Windows ou Linux, conforme o caso)
    - fará a [criação e configuração do Deployment Pool](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Operações/Agent-Job/Deployment-Group) para uso do projeto.
    - irá compartilhar o novo Deployment Pool com o projeto, atribuindo acesso ao Deployment Group à equipe de desenvolvimento
    - verificará se o [stage de Dev/QA](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/ADS/Deploy-automatizado/Release-pipeline) consegue acessar o novo Deployment Group;
    - se houver balance do tipo Big-IP, será necessário que se instale o PowerShell 7 nos servidores.

- A equipe de desenvolvimento:
    - fará [execuções do deploy](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/ADS/Deploy-automatizado/Release-pipeline/Execução-de-releases) e verificações até que tudo ocorra conforme o previsto.

Estando tudo em ordem, a RDM será finalizada.

 