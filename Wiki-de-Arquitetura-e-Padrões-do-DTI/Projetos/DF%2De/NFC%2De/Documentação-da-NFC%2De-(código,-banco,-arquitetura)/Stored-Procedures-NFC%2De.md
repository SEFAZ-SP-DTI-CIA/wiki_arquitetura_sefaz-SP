# NFCeIn


**usp_FilaProcessamentoSelect** (maxRow = 18)

1. Apaga FilaProcessamento (TOP maxRows) salvando (pKey, recebimento, id_part = pKey%4) em **Tabela temporária**
2. **Tabela temporária**(recebimento, cabecalho, mensagem) = **LoteStaging** join por pKey, recebimento, ip_part
3. Retorna **Tabela temporária**(pKey, cabecalho, mensagem) + **Lote**(CNPJ, status) + **LoteCompl**(ipOrigem, certificado)
    1. Joins acima por **Lote**(recebimento) e **LoteCompl**(recebimento)

**usp_LoteInsert** (pKey, tmStamp, CNPJ, cabecalho, mensagem, status, ipOrigem, certificado)

1. Se count(**FilaProcessamento**) > 30.000, throw DivideByZero
2. **Lote**(CNPJ, recebimento, processamento, status) = (parâmetro, GETDATE(), GETDATE(), parâmetro)
3. pKey = SCOPE_IDENTITY()
4. **LoteStaging**(pKey, recebimento, cabecalho, mensagem, id_part) = parâmetros(id_part = pKey%4)
5. **FilaProcessamento**(pKey, recebimento) = parâmetros
6. **Se** ipOrigem fornecido> **LoteCompl**(pKey, ipOrigem, certificado, recebimento) = parâmetros

**usp_LoteStatusSelect** (int pKey)

1. **Retorna Lote**(pKey, CNPJ, status) para pKey=parâmetro e recebimento entre GETDATE() e -1 mês

**usp_LoteUpdate** (int pKey, int status)

1. Se status = 103 ou 105 (sucesso ou em processamento) >
   1. **Lote**(Processamento,TempoProcessamento,status) = (GETDATE(), miliss recebimento - processamento, parâmetro)
   2. **ProcessamentoLog**(pKey,recebimento,host) = (pKey, recebimento, HOST_NAME())
   3. para pKey=param e recebimento entre GETDATE() e -48 horas
2. **Senão Lote**(Processamento, status) = (GETDATE(), parâmetro)
   1. para pKey=param e recebimento entre GETDATE() e -48 horas

**usp_TempoMedioProcessamentoSelect** ()

1. Retorna **TempoMedioProcessamento**(tempoProcessamento)
 

# NFCeOut

**usp_CadespLogConsultaInsert** (metodo, CNPJ, IE)

1. **CadespLogConsulta**(metodo, CNPJ, IE) = parâmetros

**usp_CancelamentoInsert** (nroProtocolo(OUTPUT), timestampOffsetReg(OUTPUT), idEvento, verAplic, tpAmb, versao, cOrgao, idReceptor, cStat, chNFCe, tpEvento, nSeqEvento, evento, dhEvento, ipOrigem, CNPJcertificado, certificado, CPFcertificado, issuerName, thumbprint, dataExpCertificado, ano(OUTPUT))

1. **Protocolo**(idReceptor,ufProtocolo,anoProtocolo,nroProtocolo,tpDocumento,timestampOffsetReg,timestampReg) = (parâmetro, 35, parâmetro(timeStampReg), parâmetro, 2, parâmetro, parâmetro)
2. pKey = SCOPE_IDENTITY()

3. **NFCe_Out_Constraint_DB.Evento_Constraint**(pKey,chNFCe,tpEvento,nSeqEvento,timestampReg) = parâmetros

4. **Evento**(pKey,idEvento,CNPJ,tpAmb,versao,verAplic,cOrgao,cStat,dhEvento,timestampReg) = parâmetros [ CNPJ = SUBSTRING(@chNFCe, 7, 14) ]

5. **EventoXml**(pKey, evento, timestampReg) = parâmetros

6. **Executa usp_DadosTransmissaoInsert**

**usp_CancExtemporaneoPermitido**(chNFe)

1. **Retorna** TOP 1 **CancExtemporaneo**(timestampValidade) para chNFe == parâmetro ordenado por timestampReg

**usp_CodigoMunicipioSelectPorMunicipioUF**(municipio, UF)

1. **Retorna CondMunIBGE**(CodMun) para parâmetros

**usp_DadosTransmissaoInsert**(pKey, ipOrigem, CNPJcertificado, certificado, thumbprint, dataExpCertificado, CPFcertificado, issuerName, timestampReg)

1. **Se Certificado**(pKey) é NULL para thumbprint == parâmetro
    1. **Certificado**(CNPJcertificado, CPFcertificado, issuerName, thumbprint, dataExpiracao) = parâmetros
    2. pKeyCertificado = SCOPE_IDENTITY()
    3. **XML.Certificado**(pKey, certificado) = (pKeyCertificado, parâmetro)
2. **Senão** pKeyCertificado = **Certificado**(pKey) para thumbprint == parâmetro
3. **DadosTransmissao**(pKey, ipOrigem, pKeyCertificado, timestampReg) = parâmetros [pKeyCertificado = obtido acima]

**usp_DateTimeUTCSelect**()

1. Retorna SYSDATETIMEOFFSET()

**usp_EventoSelect**(idEvento)

1. chNFCe = SUBSTRING(idEvento, 7, 44)
2. TabelaTemporária = **EventoContraint** para chNFCe acima
3. **Se** Tabela temporária não vazia
   1. Tabela temporária 2 =
     * Evento(pKey, idEvento, verAplic, cOrgao, cStat,timestampReg) +
     * TabelaTemporária(chNFCe,tpEvento,nSeqEvento) +
     * **Protocolo**(nroProtocolo)
     1. Joins por pKey ; timestampReg iguais entre Evento e TabelaTemporária
   2. **Retorna** Tabela temporária 2 (pKey,idEvento,tpAmb,verAplic,cOrgao,cStat,chNFCe,tpEvento,nSeqEvento,timestampReg,nroProtocolo) para idEvento == parâmetro

**usp_GetAutonumber**(id(OUTPUT), **_timestampReg - não é usado(?))_**

1. **Se** Ano ímpar, AutonumberProtocolo(nulo) = 1
2. **Senão**  AutonumberProtocolo2(nulo) = 1
3. **Retorna** id = SCOPE_IDENTITY()

**usp_InutNFCeCheck2**(UF, CNPJ, serie, notaInicial, notaFinal)

1. **TabelaDeRetorno**(NFeExistenteNaFaixa, FaixaParcialmInutilizada, MesmaFaixaInutilizada) = (0,0,0)
2. numero = cada um dos numeros na faixa [notaInicial ; notaFinal]
3. **Tabela temporária** = **NFCe_Constraint**(pKey, cStat) + numero + **Protocolo**(idReceptor, ufProtocolo, anoProtocolo, nroProtocolo, timestampReg)
   * NFCe_Constraint selecionado por (UF,CNPJ,serie) == parâmetros
   * Join com **Protocolo** por **NFCe_Constraint**(pKey , timestampReg)
4. **Se Tabela temporária** não vazia, **TabelaDeRetorno**(NFeExistenteNaFaixa) = 1
5. **Se Tabela temporária** tem pelo menos uma nota já inutilizada (cStat == 102)
   1. **TabelaDeRetorno**(FaixaParcialmInutilizada) = 1
   2. minPkeyCancelada = **MIN**(**Protocolo**(pKey)) e **Protocolo** JOIN **Tabela temporária** por (nroProtocolo, anoProtocolo, idReceptor, ufProtocolo)
   3. **Se** notaInicial == **InutNFCe**(nNFIni) **E** notaFinal == **InutNFCe**(nNFFim), para **InutNFCe**(pKey) == minPkeyCancelada
      1. **TabelaDeRetorno**(MesmaFaixaInutilizada, idReceptor, UF, anoProtocolo, nroProtocolo) = (1) + **Tabela temporária**(idReceptor, ufProtocolo, anoProtocolo, nroProtocolo) para pKey == minPkeyCancelada
6. **Retorna TabelaDeRetorno**()

**usp_InutNFCeInsert2** (idReceptor, UF, ano, CNPJ, modelo, serie, nNFIni, nNFFim, versao, inutNFCe, **_chsNFCe AS TABLE_**, digVal, status, tpAmb, verAplic, justificativa, ipOrigem, CNPJcertificado, certificado, CPFcertificado, issuerName, thumbprint, dataExpCertificado)

1. **Tabela temporária**(tpEmiss, uf, ano, cnpj, modelo, serie, numero) = SUBSTRINGS(chsNFCe)
2. **usp_GetAutonumber**(nroProtocolo, SYSDATETIMEOFFSET())
3. **ForEach** chNFCe in **Tabela temporária**
   1. **Tabela temporária2**(cnpjEmit, numero, serie, tpEmis, ano, UF, cStat, idReceptor, ufProtocolo, anoProtocolo, nroProtocolo, tpDocumento) = **Tabela temporária**(cnpjEmit, numero, serie, tpEmis, ano, UF) + (102, idReceptor parâmetro, UF parâmetro, Ano com 2 digitos SYSDATETIMEOFFSET(), nroProtocolo, 3)
