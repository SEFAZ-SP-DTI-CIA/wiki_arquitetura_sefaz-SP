# Análise Estática de Codigo (CD)

## Introdução

Este tipo de análise valida o código fonte (sintaxe, estrutura e bibliotecas). Existem diversas ferramentas que realizam esse tipo de análise. No caso de soluções .NET, é possível realizar a Análise Estática pelo próprio Visual Studio (menu _Analyze_). Além dos critérios de segurança (exigidos no nível 1), é possível avaliar o código quanto a sua estutura, bibliotecas utilizadas, lógica de código, resiliência da aplicação, globalização, nomenclatura etc. A escolha de quais itens serão analisados e da criticidade dos problemas encontrados é definido em um arquivo de regras (o Visual Studio tem instalado nativamente vários arquivos de regra).  

Além disso, o processo de entrega contínua do Azure DevOps Server também permite a ativação dessa análise durante o _build_ da solução.  

---

## Regra
A análise estática de código avalia o código fonte em busca de problemas na modelagem do código e nas escolhas de codificação. Este processo visa reforçar o uso de boas práticas de desenvolvimento e garantir o mínimo de integridade do código que está sendo versionado.

## Critério de aceitação
O projeto em questão deve ter a análise de código estático ativada no Pipeline de Integração Contínua do Projeto. O conjunto de regras mínimo aceitável é o "Managed Recommended Rules". Além disso, a equipe deve explicar o processo de atendimento dos apontamentos realizados na análise de código. 

Para completude deste item, a equipe deve garantir que erros na análise de código são tratados antes que o código seja instalado no ambiente de produção.
