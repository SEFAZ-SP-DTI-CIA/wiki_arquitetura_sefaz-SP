São denominados artefatos de uma aplicação o conjunto de arquivos necessários para sua execução em algum servidor de hospedagem, seja uma máquina local, um servidor em uma rede privada (intranet) ou na internet.

Alguns desses artefatos, denominados de arquivos de configuração, contém informações de relacionamentos (links) com outros sistemas ou bancos de dados, como, por exemplo, o web.config ou appsettings.json.

Em um processo de criação de artefatos (build) e sua instalação em servidores (deploy), denomina-se "promoção de artefatos" o processo de alteração dos arquivos de configuração antes do deploy, para o devido ajuste das suas referências, que devem ser distintas de acordo com o [ambiente de hospedagem da aplicação](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/Operações/Ambientes-de-hospedagem-de-aplicações). 

Há diversas estratégias de promoção de artefatos que podem ser usadas em um processo de deploy.

# Promoção de binários
Estratégia adotada como padrão pela Secretaria da Fazenda de São Paulo, os artefatos são gerados uma vez apenas, para cada versão da aplicação, e são utilizados para deploy em todos os ambientes, com alteração dos arquivos de configuração através de [técnicas de transformação de variáveis](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/ADS/Deploy-automatizado/Release-pipeline/Definição-de-variáveis-de-release).

![image.png](/.attachments/image-6280fecb-5999-4db3-9e76-07a569f77f7d.png)

Nessa estratégia, se houver falha na execução da aplicação em homologação após uma execução bem sucedida em desenvolvimento, o problema deverá estar na **infraestrutura** ou na **transformação das variáveis de configuração**.

Veja um exemplo de deploy automatizado usando a estratégia de promoção de binário na [aplicação modelo](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_release).
#Promoção por código
Estratégia em desuso, na promoção por código ou branch, a mudança nos arquivos de configuração é feita pela equipe de desenvolvimento diretamente no código-fonte da aplicação. 

No versionador utilizado, criam-se réplicas (branches) do código para cada ambiente de deploy:
- Uma branch de DEV contém tudo o que está sendo alterado. 
- Para a branch MAIN, promovem-se somente as alterações que podem ser homologadas pela área de negócios. 
- A branch PROD recebe o código aprovado na homologação, sendo usado para instalação em produção para os usuários finais.
![image.png](/.attachments/image-3f8ac7c6-cc27-4947-8fcf-eac238f32c8f.png =600x)

Aqui, pode ser criado um pipeline build para cada branch de cada aplicação, que pode disparar (trigger) um pipeline de release para deploy no respectivo ambiente em que se fará seu uso. 
O binário utilizado no deploy em produção será aquele gerado na build de PROD, não tendo relação necessária com DEV ou MAIN.

Nesse modelo, após o sucesso de execução da aplicação no ambiente de testes, falhas em homologação ou produção podem ser oriundas de problemas de **infraestrutura**, de **transformação de arquivos de configuração** ou do **código-fonte**. Devido à possibilidade de falha também oriunda do código-fonte, recomenda-se o uso da promoção por binário, estratégia que evita este tipo de falha.
