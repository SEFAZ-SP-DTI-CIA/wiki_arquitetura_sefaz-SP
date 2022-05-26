##   Propósito

   Foi criado um novo web-service de Diagnóstico sob "ws" e "EPECws", portanto paralelo aos web-services existentes:
 * ws/NfeDiagnosticoServico.asmx
 * EPECws/EPECDiagnosticoServico.asmx

O objetivo é medir o tempo de resposta de todos os bancos de dados acessíveis pelas respectivas máquinas da DMZ que vão processar o request, incluindo os bancos do CADESP de Campinas e São Paulo. Isso pode diagnosticar possíveis problemas com bancos específicos.
 
Foi adicionado ainda informações da Sonda, o ws/NfeDiagnosticoServico.asmx retorna também a última linha da tabela [NFCe_Out].[NFCeOut].[SondaMonitoracaoSefaz] .
 
O XML enviado contém informações sobre o que é requerido sob a tag <Config>, sob a qual podem existir as tags <Bancos> e <Sonda> (essa última aplicável somente à NFC-e).
&nbsp; - sob a tag <Banco> pode ser colocada a tag <Todas> , que vai retornar as informações de todos os bancos, ou a tag de cada banco se quiser acessar apenas bancos específicos.
&nbsp; - a tag <Sonda/> não tem filhos, retorna a informação da Sonda se a tag estiver presente.
 
 
##   Mensagens de envio
 
Assim como os outros web-services, é necessário preencher o cabeçalho (nfeCabecMsg), mas não é necessário preencher nenhum dado sob nfeDadosMsg:Assim como os outros web-services, é necessário preencher o cabeçalho (nfeCabecMsg), mas não é necessário preencher nenhum dado sob nfeDadosMsg:
 
### &nbsp; 1) EPEC

```
<soap:Envelope xmlns:soap="http://www.w3.org/2003/05/soap-envelope" xmlns:epec="http://www.portalfiscal.inf.br/nfe/wsdl/EPECDiagnosticoServico">
   <soap:Header>
      <epec:nfeCabecMsg>
         <!--Optional:-->
         <epec:cUF>35</epec:cUF>
         <!--Optional:-->
         <epec:versaoDados>3.10</epec:versaoDados>
      </epec:nfeCabecMsg>
   </soap:Header>
   <soap:Body>
      <epec:nfeDadosMsg>
         <Configuracao>
            <Bancos>
               <Todos/>
               <NFCe_EPEC_Dados/>
               <NFCe_EPEC_XML/>
               <NFCe_EPEC_Constraint/>
               <NFCe_Out_Constraint_Copia/>
               <CADESP_CAS/>
               <CADESP_SP/>
            </Bancos>
         </Configuracao>
      </epec:nfeDadosMsg>
   </soap:Body>
</soap:Envelope>
```

 
### &nbsp; 2) NFC-e

```
<soap:Envelope xmlns:soap="http://www.w3.org/2003/05/soap-envelope" xmlns:nfed="http://www.portalfiscal.inf.br/nfe/wsdl/NFeDiagnosticoServico">
   <soap:Header>
      <nfed:nfeCabecMsg>
         <!--Optional:-->
         <nfed:cUF>35</nfed:cUF>
         <!--Optional:-->
         <nfed:versaoDados>3.10</nfed:versaoDados>
      </nfed:nfeCabecMsg>
   </soap:Header>
   <soap:Body>
      <nfed:nfeDadosMsg>
         <Configuracao>
            <Bancos>
               <Todos/>
               <NFCe_In/>
               <NFCe_In_XML/>
               <NFCe_Out/>
               <NFCe_Out_XML/>
               <NFCe_Out_Constraint/>
               <NFCe_EPEC_Constraint_Copia/>
               <CADESP_CAS/>
               <CADESP_SP/>
            </Bancos>
            <Sonda/>
         </Configuracao>    
      </nfed:nfeDadosMsg>
   </soap:Body>
</soap:Envelope>
```

 
 
##    Mensagens de Retorno
 
### &nbsp; 1) EPEC

```
<retConsDiagServ versao="3.10" xmlns="http://www.portalfiscal.inf.br/nfe">
   <tpAmb>2</tpAmb>
   <Acesso_A_Banco>
      <NFCe_EPEC_Dados>2 ms</NFCe_EPEC_Dados>
      <NFCe_EPEC_XML>2 ms</NFCe_EPEC_XML>
      <NFCe_EPEC_Constraint>2 ms</NFCe_EPEC_Constraint>
      <NFCe_Out_Constraint_Copia>14 ms</NFCe_Out_Constraint_Copia>
      <CADESP_CAS>24 ms</CADESP_CAS>
      <CADESP_SP>8 ms</CADESP_SP>
   </Acesso_A_Banco>
</retConsDiagServ>
```

 
### &nbsp; 2) NFC-e

```
<retConsDiagServ versao="3.10" xmlns="http://www.portalfiscal.inf.br/nfe">
   <tpAmb>2</tpAmb>
   <Acesso_A_Banco>
      <NFCe_In>8 ms</NFCe_In>
      <NFCe_In_XML>2 ms</NFCe_In_XML>
      <NFCe_Out>1 ms</NFCe_Out>
      <NFCe_Out_XML>2 ms</NFCe_Out_XML>
      <NFCe_Out_Constraint>2 ms</NFCe_Out_Constraint>
      <NFCe_EPEC_Constraint_Copia>2 ms</NFCe_EPEC_Constraint_Copia>
      <CADESP_CAS>13 ms</CADESP_CAS>
      <CADESP_SP>5 ms</CADESP_SP>
   </Acesso_A_Banco>
   <Sonda>
      <pKey>10</pKey>
      <timestampReg>16/06/2015 09:21:06</timestampReg>
      <nroLotesFila>1</nroLotesFila>
      <nroLotesTotal>3</nroLotesTotal>
      <nroLotesSincronos>2</nroLotesSincronos>
      <nroLotesAssincronos>1</nroLotesAssincronos>
      <nroLotesAguardando>1</nroLotesAguardando>
      <nroLotesEmProcessamento>0</nroLotesEmProcessamento>
      <nroLotesProcessadosSucesso>2</nroLotesProcessadosSucesso>
      <nroLotesProcessadosFalhaSchema>0</nroLotesProcessadosFalhaSchema>
      <nroLotesProcessadosErro>0</nroLotesProcessadosErro>
      <dataRecepcaoUltimoLote>16/06/2015 09:20:05</dataRecepcaoUltimoLote>
      <tempoMedioAutorizacao>274</tempoMedioAutorizacao>
      <nroNFCesTotal>5</nroNFCesTotal>
      <nroNFCesAutorizadas>2</nroNFCesAutorizadas>
      <nroNFCesDenegadas>0</nroNFCesDenegadas>
      <nroNFCesInutilizadas>3</nroNFCesInutilizadas>
      <nroNFCesRejeitadas>0</nroNFCesRejeitadas>
      <dataUltimoProtocoloValido>16/06/2015 09:20:05</dataUltimoProtocoloValido>
      <dataUltimaEmissaoProcessada>16/06/2015 09:20:05</dataUltimaEmissaoProcessada>
      <dataUltimaInutilizacaoProcessada>16/06/2015 09:20:00</dataUltimaInutilizacaoProcessada>
      <nroEventosCancelamento>1</nroEventosCancelamento>
      <dataUltimoCancelamentoProcessado>16/06/2015 09:19:59</dataUltimoCancelamentoProcessado>
      <ultimaPKeyNFCe>1618905</ultimaPKeyNFCe>
      <nfceAtiva>False</nfceAtiva>
   </Sonda>
</retConsDiagServ>
```

