# Cuidados para alterar a Consulta Pública

## Atualização de XSLT
Quando fazemos alteração na Consulta Pública, usamos sempre os *.xsl enviados pelo pessoal do Rio de Janeiro, substituindo cada um dos nossos *.xsl pelos equivalentes deles. Ao fazer uma alteração, tome cuidado especificamente com o arquivo NFe_Resumida.xsl. Neste arquivo, na versão do Rio de Janeiro, existe uma chamada para a função eEventoVersaoResumida. Esta função pode ser encontrada no arquivo Util.xsl. Essa função deveria ser analisada pelo xsl e usada para montar o xsl NFe_Resumida.xsl, contudo, isso não funciona na versão XSLT 1.0 do .net , por isso, precisamos alterar o NFe_Resumida.xsl substituindo a chamada da função por uma comparação que faz a mesma coisa que a função descrita em Util.xsl.

O .net apenas possui a versão 1.0 do XSLT, versões superiores apenas existem com dll de terceiro. O problema de fazer a conversão da versão 1.0 para 2.0 ou superior é que a própria linguagem de marcação xsl muda. Poderíamos fazer isso, mas nesse caso, teríamos que alterar também todos os *.xsl do projeto o que não é viável. 

Para fazer a alteração, geralmente recebemos um arquivo zipado que contém arquivos *.xsl, *.css e *.js. A estrutura de pastas que existe nesse arquivo zipado não corresponde a nenhuma estrutura do projeto Consulta Pública. A única correspondência que se deve procurar é pelo nome, assim, arquivos com determinado nome dentro do zip tem um correspondente com o mesmo nome dentro do projeto.

Verifique se novas versões dos arquivos .xls não modificam o caminho da pasta resources. Por exemplo os arquivos _Estilos_Geral.xsl e _Scripts_Geral.xsl fazem referência a "../resources" , isso tem que ser mantido. Uma maneira fácil de ver se tudo está funcionando com os caminhos é usar o debugger do navegador (p. ex. F12 do Chrome) para ver se tem algum erro ao carregar arquivos de estilo ou scripts (.css / .js) .

Por último mas também muito importante, qualquer alteração, salvo explicitado em contrário, se refere sempre à última versão do Consulta Pública. As versões do Consulta Pública da página resumida.aspx e da página completa.aspx estão nas pastas "consulta/v107", "consulta/v110" e "consulta/v200", repectivamente correspondendo a versão 1.07, versão 1.10 e versão 2.00 ou superior


## Configuração da página de Download do XML da NF-e
A página de download do XML da NF-e dentro da Consulta Pública demanda certificado digital para ver se o CPF/CNPJ desse certificado é de algum interessado na nota. Não sendo um interessado, o download não é permitido.

No IIS, o site da ConsultaPública como um todo está com a configuração de SSL "Exigir SSL" desmarcada e "Certificados do cliente" com "Ignorar" selecionado. Esa configuração vale para todas as sub-pastas desse site, exceto para sub-pasta 'xml' , que contém a página de download do XML da NF-e "DownloadXML.aspx" . Nessa sub-pasta 'xml', e somente nela, optou-se por alterar a configuração de SSL para "Exigir SSL" marcada e "Certificados do cliente" com "Aceitar" selecionado. Isso força com que abra a caixa de seleção de certificado digital quando se redireciona para essa página - que é a ação do usuário de selecionar o botão de Download XML , seja na consulta resumida, seja na consulta completa da NF-e.