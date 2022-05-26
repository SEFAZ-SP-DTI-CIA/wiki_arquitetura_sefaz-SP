A Sefaz-SP não adota uma estratégia de configuração de branches, deixando para a equipe de desenvolvimento a escolha da forma que seja mais adequada à sua necessidade.

Porém, observam-se diversas aplicações que utilizam _branch_:
- Master - (trunk ou main) com o código estável
- Feature - para a organização do desenvolvimento em partes 
- Release - para geração de artefatos destinadas aos deploys.

![estrategia_branch.png](/.attachments/estrategia_branch-44f88e8c-3451-4cf9-8da2-bdd74462a13b.png)

## Features
Uma branch de feature pode ser criada, de acordo com as estratégias da equipe de desenvolvimento, por:
- funcionalidade
- sprint
- desenvolvedor
- uma mistura de critérios

Qualquer que seja, espera-se que haja uma adição (merge) das alterações dessa branch à master, encerrando-se a branch.

## Releases
Cria-se uma branch para cada versão (release) candidata a ser promovida para produção.

Havendo a identificação de falhas (bugs) na aplicação em produção, pode-se criar uma nova branch sob a release para aplicar uma correção (hotfix). Após testes locais, a correção é adicionada à branch release para sua homologação e promoção à produção.

Essa correção pode ser feita diretamente na branch release, para simplificar. Porém, essa ramificação fica visualmente evidente no painel de gerenciamento ou no de histórico de branches no Visual Studio, facilitando o entendimento.

Não se prevê merges de branches de release à master, que são bloqueadas após a criação de nova release, mantidas apenas para efeito de documentação.

> As vezes, se deseja que alguma correção na release seja adicionada à master, que pode ser feita pelo merge, mas também pelo recurso de seleção de changeset ([cherry pick](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Desenvolvimento/Git/Git-no-Visual-Studio)).

#Pipelines no ads
No ads, pode ser interessante a criação de dois (ou mais) pipelines sobre a:
- Branch master
Com integração contínua para a verificação da estabilidade do código como um todo.
- Branch de release 
 Destinado ao deploy em produção, configurado para disparo manual.


