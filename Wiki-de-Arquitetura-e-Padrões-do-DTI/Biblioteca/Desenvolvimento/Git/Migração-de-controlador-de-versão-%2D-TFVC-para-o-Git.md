A Sefaz, por orientação do própria Microsoft, adotou o Git como ferramenta de versionamento de código de desenvolvimento de sistemas.
Portanto, TODOS os projetos deverão converter o código do TFVC (Team Foundation Version Control) para o Git.

#Git-TF
Dentre alguns aplicativos que auxiliam a conversão de código entre o TFVC e o Git , o DTI têm utilizado o Git-TF com sucesso.

O Git-TF pode ser obtido em:
https://www.microsoft.com/en-us/download/details.aspx?id=30474
(selecione o arquivo zip mais recente)

>Há, também, uma versão disponível com instruções de uso em:
https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_git/Git-TF

##Instalação do Git-TF
- Instale o Java em seu computador, se não houver. (talvez tenha que reiniciar o computador)

- Copie ou baixe* a pasta aplicativo git-tf para seu computador, por exemplo:
`c:\Git-TF`

- Acesse "Editar as Variáveis de ambiente do sistema" no Windows. 
    - Clique em "Variáveis de Ambiente".
      - Adicione à variável do sistema PATH o caminho do Git-TF.
      - Crie uma nova variável de sistema: TF_ACCEPT_UNTRUSTED_CERTIFICATES=true

##Conversão de repositório
A conversão do repositório TFVC em Git é feito em duas etapas: clonagem e envio (push)
Para clonar o repositório TFVC, execute a linha de comando**:
  `git-tf clone https://ads.intra.fazenda.sp.gov.br/tfs/TPC-2010 $/[nome do projeto]/[nome da branch] --deep`
>O argumento --deep traz todo o histórico do repositório. Com isso, a conversão se torna muito mais demorada.
Sem o histórico, a conversão se torna similar a fazer download do repositório TFVC, tornando o uso do Git-TF desnecessário.

Após a clonagem, utilize comandos Git*** para enviar o código ao novo repositório, por exemplo:
  `git remote add origin [endereço do repositorio]`  ****
  `git push -u origin --all`
    
---
Observações
>A conversão pode ser feita em computadores da Sefaz na intranet ou em teletrabalho pela VPN.

>\* Para baixar o git-tf, acesse:
https://ads.intra.fazenda.sp.gov.br/tfs/ADMIN/Wiki_Arquitetura/_git/Git-TF
. Clique no ícone com três pontos na vertical no canto superior direito do painel.
. Selecione "Download as a zip".
. Salve em alguma pasta e extraia seu conteúdo para a pasta em que será usado o Git-TF.
	  
>** Essa conversão pode demorar de minutos a dias, dependendo do tamanho do repositório.
Recomenda-se que se converta uma branch apenas.
Havendo a necessidade de migrar todas as branches, clona-se uma por vez, gerando repositórios diferentes para cada branch.

>*** Para execução dos comandos, o usuário deve ter acesso como Contributor (ou maior) no Team Project.

##Configuração do PowerShell
Para habilitar a execução de scripts PowerShell, se necessário, execute pelo PS como administrador uma vez no computador:
`Set-ExecutionPolicy -ExecutionPolicy Unrestricted`