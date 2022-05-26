Código atual: 3894 – Agente Fiscal de Rendas
Código novo: 5770 – Auditor Fiscal da Receita Estadual

Fazendários ativos: 2.643, sendo que:
- grande parte terá atualização automática do cadastro no SIAP;
- o restante será via atualização manual, pelos usuários das regionais.

Fazendários inativos: serão alterados apenas os inativados após a lei.

**Importante**: durante um curto período de tempo teremos os 2 códigos sendo utilizados (em virtude das atualizações manuais). 

Aplicações que necessitarão alteração:
-As que fazem algum tratamento com o código e/ou nome do cargo atual.

Aplicações com possibilidade de impacto são as que utilizam:
-Sefaz Identity
-SIAP
-LDAP  não é possível identificar todas as aplicações que autenticam usuários
-AD
-IDM
-Cópia da base Oracle do SIAP, em SQL Server (DRH)

Arquitetos de Sistemas: criar Enabler em todos os produtos, referente ao levantamento de impacto e eventuais ajustes necessários.
Equipe das aplicações que tiverem impacto: criar PBI´s abaixo do Enabler acima.

Exemplo do padrão de Enablers a serem criados nos Produtos (usar tag ENABLER_CIA_2022_ALTERACAO_NOME_CARGO_AFR):
[Query com todos os enablers](https://ads.intra.fazenda.sp.gov.br/tfs/produtos/SAFe_Iniciativas_Corporativas_de_Tecnologia/_queries/query/c4fe0b78-d658-4dc4-ad8a-e8d6447104e3/)

![image.png](/.attachments/image-60af9389-65b9-404f-8af2-f5c3a1746b4f.png)



**IMPORTANTE**:
- "a data de implantação será **15/04/2021**, este prazo agora está atrelado também ao processo de alteração da Hierarquia Sefaz, sem possibilidades de prorrogação."
- "Durante um tempo, os dois códigos funcionarão ao mesmo tempo"

<br>
Planejamento original, porém desatualizado:
![image.png](/.attachments/image-7dac4162-6185-4a54-9753-62e1c82b771b.png)