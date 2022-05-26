[[_TOC_]]

#Saneamento das Bases de Dados no Ambiente de Desenvolvimento

O ambiente de desenvolvimento possui maiores limitações com relação à estrutura física do que aquelas disponíveis nos ambientes de produção e homologação. Adicionalmente, alguns processos administrativos de manutenção de bancos de dados são muito sensíveis ao tamanho e à quantidade dos mesmos, muitas vezes tornando a janela noturna de manutenção extremamente curta. Processos tais como:
- Backup full
- Restore
- Mascaramento
- Uso de espaço nos file systems
- Outros

Crescem de forma linear - em alguns casos de forma exponencial - com o aumento do tamanho e da quantidade de bancos de dados a serem verificados, ainda que sejam automatizadas a maioria de suas atividades administrativas. Por isto, é mandatória a implantação de uma política eficaz de saneamento de dados, tanto sob  aspecto da quantidade de bases de dados envolvidas como também os seus tamanhos. A estratégia de apenas aumentar os recursos físicos de infraestrutura das estruturas de dados, sem a implantação simultânea de uma política de saneamento, está fadada ao fracasso; uma vez que a demanda por dados não saneados é sempre crescente.

A manutenção das estruturas de dados do DTI/CDS saneadas é responsabilidade do CCD, que tem uma visão completa de todo o CDS no que diz respeitos aos seus bancos de dados, assim como também é responsabilidade do CCD a implantação de uma política de saneamento de dados no ambiente de desenvolvimento.

#Definições
1. É considerado banco de dados **oficial** em desenvolvimento de um determinado sistema/projeto aquele banco de dados que possui correspondente banco de dados no ambiente de produção com idêntico nome.
2. É considerado banco de dados **não oficial** no ambiente de desenvolvimento todas aquelas cópias de bancos de dados que não possuam correspondente versão em produção com idêntico nome.
3. Todos os bancos de dados em desenvolvimento que não sejam considerados **oficiais** de algum sistema/projeto será classificado como **cópia temporária**.

#Política de Saneamento de Dados
De maneira a manter o ambiente de desenvolvimento de dados saudável, a seguinte política foi estabelecida para o DTI/CDS:  

I. O ambiente de dados em desenvolvimento  está sofrendo um processo de saneamento que objetiva eliminar todas as **cópias temporárias** de base de dados sem uso.

II. Toda e qualquer criação nova de base de dados no ambiente de desenvolvimento requer a abertura de um chamado no remedy e o agendamento junto ao CCD (equipedba) de uma reunião para revisão da solicitação. Se a base de dados a ser criada for um banco de dados **oficial** de um novo sistema/projeto esse requerimento ocorrerá dentro do contexto de uma [Reunião Inicial Obrigatória](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/186/O-Processo-de-Gest%C3%A3o-e-Modelagem-de-Dados?anchor=reuni%C3%A3o-inicial-obrigat%C3%B3ria) do projeto; por outro lado, se a nova base de dados for uma  **cópia temporária**, os seguintes requisitos deverão ser observados:
- Existir justificativa para a criação da cópia do banco de dados;
- Definição de responsável pela criação da cópia do banco de dados;
- Definição do sistema/projeto vinculado à cópia do banco de dados;
- Estabelecimento da Data limite para remoção da cópia do banco de dados do ambiente de desenvolvimento;

Eventuais prorrogações da data limite para a remoção da cópia do banco de dados podem ser solicitadas, com a devida fundamentação. Contudo, não serão aceitas solicitações de criações ou prorrogações de **cópia temporária** sem estar vinculada a uma data de remoção.


