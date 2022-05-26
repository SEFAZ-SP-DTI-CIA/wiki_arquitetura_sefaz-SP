Teste de software é qualquer procedimento que procura aferir a qualidade de uma aplicação, entendida como a sua capacidade de atender aos requisitos estabelecidos no seu projeto de desenvolvimento.

[[_TOC_]]

#Tipos de testes
Há diversas formas de se executar testes, que variam com o objetivo desejado. 

##Testes manuais
Executado e avaliado por ação humana.
- [caixa branca](https://blog.geekhunter.com.br/tecnicas-de-caixa-preta-e-branca-para-teste-de-software/) - Submetem-se dados à aplicação e a cada passo dentro de seu código vão se verificando suas transformações.
- caixa preta - Avalia-se as saídas de uma aplicação em função dos dados de entrada.
- [regressão](https://www.devmedia.com.br/teste-de-regressao/23038) - refazimento de testes a cada versão publicada de um software, para verificar se as alterações não afetaram lugares já testados anteriormente.

##Testes automatizados
Executado e avaliado por aplicativos de testes, usualmente antes de compartilhar o código, para verificar se as modificações não afetaram outros lugares, e também após o envio a um repositório compartilhado, para certificar de que essas alterações não prejudicam outras partes da aplicação.

Em sua preparação, uma ferramenta de testes envia dados específicos à aplicação e espera determinado resultado. Se houver discrepância, o próprio software de teste registra a falha.
- [Testes de unidade](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/312/Testes-unit%C3%A1rios) – testes aplicados em apenas um módulo. Submete-se a certos dados e avaliam-se as saídas, comparando com valores esperados. 
- [Testes de integração](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/310/Testes-de-integra%C3%A7%C3%A3o) - módulos são combinados e testados em grupo.
- [Teste de carga](http://www.linhadecodigo.com.br/artigo/3259/testes-de-performance-testes-de-carga-stress-e-virtualizacao-parte-3.aspx) - Verifica se o software funciona normalmente com uma quantidade real estimada de dados e interações.
- Teste de stress - Avalia o funcionamento da aplicação em cenários com quantidades anormais de dados ou interações.
- [Teste de interface de usuário](https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/314/Testes-de-interface) - Serve para garantir que todos os itens de uma tela funcionem adequadamente, analisando a interação de um aplicativo, site ou programa com o usuário final.

#Planejamento de testes
O planejamento dos testes pode ser dividido em categorias que auxiliem a definição de seus objetivos.
- Usabilidade
Verifica a relação do usuário com a interface e a relevância das informações exibidas.
  - Relevância
  - Adequação
  - Resposta esperada
  - Quantidade de interações para cada ação
  - Complexidade
  - Desempenho
  - Quantidade de erros
  - Eficácia

- Confiabilidade
Submete a aplicação a situações anormais e avalia sua resposta, dentro de alguma meta estabelecida.
  - Recuperação após falha
  - Tolerância a falhas
  - Conformidade

- Portabilidade
Verifica quais plataformas suportam a aplicação.

- Acessibilidade
Avalia se a aplicação pode ser utilizada por usuários com alguma limitação.

Veja mais:
- [Devmidia - Guia de testes de software](https://www.devmedia.com.br/guia/guia-de-testes-de-software/34403)
- [OneDayTesting - Tipos de testes](https://blog.onedaytesting.com.br/teste-de-software/) 

#Ferramentas de testes
Há uma infinidade de aplicações que auxiliam o planejamento, acompanhamento e execução de testes.

Para auxiliar a execução manual de testes, recomenda-se a instalação e utilização do [plugin "Test & Feedback"](https://marketplace.visualstudio.com/items?itemName=ms.vss-exploratorytesting-web), que facilita a captura de tela, gravação de vídeos e criação de workitens relacionados aos testes.

Para o gerenciamento de testes, a Sefaz utiliza o [Test Plan do ads](/Wiki-de-Arquitetura-e-Padrões-do-DTI/Biblioteca/ADS/Recursos-do-Azure-Devops/Test-Plan).
