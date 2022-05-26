<u>18/10/2019</u>

Enviamos a seguir informações importantes relativas à migração para o Azure DevOps Server 2019. Observamos que houve mudança no nome, mas trata-se de produto similar ao anterior, Team Foundation Server (TFS):

- **Mudança do endereço:** O endereço antigo do TFS era apenas http. Para evitar conflitos na infraestrutura da SEFAZ, a nova versão do TFS está em outro endereço:

    <a href="https://ads.intra.fazenda.sp.gov.br/tfs" target="_blank">https://ads.intra.fazenda.sp.gov.br/tfs</a>

     O endereço antigo está apenas funcionando como um redirecionamento para o servidor novo. A recomendação do time da Microsoft é que a configuração do Visual Studio seja alterada para apontar para o novo endereço (detalhes adicionais podem ser encontrados no: [Guia "Configuracao servidor Azure DevOps"](http://etc.intra.fazenda.sp.gov.br/sites/suporte_desenv/_layouts/15/WopiFrame.aspx?sourcedoc=/sites/suporte_desenv/Documentos%20Compartilhados/Guias%20e%20Manuais/Configuracao%20servidor%20Azure%20DevOps.docx&action=default)).

- **Mudança do _nuget_ da SEFAZ:** A nova versão do TFS permite, além de baixar os pacotes nuget da internet (funcionalidade que não estava ativa no TFS antigo), configurar o build automatizado para utilizar outros gerenciadores de pacote (maven, npm, ...). Para isso, devem ser seguidos os procedimentos indicados no guia: ["Configuração do Nuget da SEFAZ"](http://etc.intra.fazenda.sp.gov.br/sites/suporte_desenv/_layouts/15/WopiFrame.aspx?sourcedoc=/sites/suporte_desenv/Documentos%20Compartilhados/Guias%20e%20Manuais/Configura%C3%A7%C3%A3o%20do%20Nuget%20da%20SEFAZ.docx&action=default).

- **Mudança da _url_ do Sharepoint do TFS:** : A partir da versão 2017 do TFS, o Sharepoint não possui mais a vinculação com o TFS. Por esse motivo, ele passou a ter um endereço separado.

   Em relação ao endereço antigo, o que muda é só a url base, 
de: http://tfs.intra.fazenda.sp.gov.br
para: http://portaltfs.intra.fazenda.sp.gov.br

  Por exemplo, o endereço antigo:
http://tfs.intra.fazenda.sp.gov.br/sites/TPC-2010/RECOPI%20Nacional/Shared%20Documents/Forms/AllItems.aspx

  Passa a ser:
http://portaltfs.intra.fazenda.sp.gov.br/sites/TPC-2010/RECOPI%20Nacional/Shared%20Documents/Forms/AllItems.aspx


  Caso sejam necessários esclarecimentos adicionais, entre em contato através do e-mail dti_cia_nsd@fazenda.sp.gov.br.



