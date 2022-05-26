Release é o nome dado à uma execução de um pipeline de deploy.
Após ser criada, de acordo com a sua configuração, a release poderá iniciar um primeiro stage e aguardar a intervenção manual de outros.

A Sefaz estabelece algumas regras de procedimentos de execução e orientações de administração dessas releases.

[[_TOC_]]

#Criação de releases
Uma release pode ser criada em duas formas.

##Entrega contínua
A release é criada após uma execução bem sucedida do pipeline de build associado a esse pipeline de release, em um procedimento conhecido por Entrega Contínua ou [Continuous Delivery (CD)](https://pt.wikipedia.org/wiki/Entrega_cont%C3%ADnua).

Nessa estratégia, após a criação dos artefatos, pelo menos um stage do pipeline de release é executado (deploy), usualmente em um ambiente de testes ou desenvolvimento.

A Sefaz não adota a entrega contínua nos ambientes de homologação e de produção, que requerem a criação e aprovação prévia de [requisições de mudanças](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Operações/Solicitações-de-infraestrutura/Requisição-de-Mudanças).

##Manual
Uma release é iniciada clicando-se no botão "Create release".
![Release_Criar.png](/.attachments/Release_Criar-6ade583f-abf4-4a23-9a0e-f8bfdcd58271.png =500x)

Nesse caso, um painel se abre à esquerda da página solicitando o confirmação da criação.
![Release_Criar_Confirmar.png](/.attachments/Release_Criar_Confirmar-85c953f6-d51a-4b1c-8fd7-1128b2c31910.png =350x)

Antes de confirmar, é possível modificar algumas definições: 
- Execução de Stages - permite a mudança de execução de stages para execução manual (trigger After Release).
- Versão de build - permite a escolha de uma versão de build anterior à mais recente.
- Valores de variáveis - permite a alteração de valores das variáveis sinalizadas no pipeline.

A release será criada após se clicar no botão "Create".

#Exclusão de Releases de Deploy automatizado
As releases executadas em deploys automatizados pela equipe windows são registros das atividades efetuadas e **não podem ser excluídas**, mesmo em caso de falha, pois os seus logs podem orientar eventuais investigações futuras de possíveis problemas.

>Não foi encontrada alguma forma eficaz de evitar que um administrador de um projeto apague as releases, assim, só se pode orientar os usuários a não fazê-lo. Se o fizer, poderá ocorrer alguma investigação e o usuário estará sujeito à responsabilização por eventuais danos que essa ação causar ao serviço público.



