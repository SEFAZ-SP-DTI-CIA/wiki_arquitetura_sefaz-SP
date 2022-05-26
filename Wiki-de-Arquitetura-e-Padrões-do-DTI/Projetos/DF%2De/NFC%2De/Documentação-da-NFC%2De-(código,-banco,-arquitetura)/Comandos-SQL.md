# Grants das tabelas


```
use [NFCe_In_DB]

grant select,insert,delete,update on [NFCeIn].[FilaProcessamento] to nfce_user

grant select,insert,delete,update on [NFCeIn].[Lote] to nfce_user

grant select,insert,delete,update on [NFCeIn].[LoteStaging_01] to nfce_user

grant select,insert,delete,update on [NFCeIn].[LoteStaging_02] to nfce_user

grant select,insert,delete,update on [NFCeIn].[ProcessamentoLog] to nfce_user

grant select,insert,delete,update on [NFCeIn].[Status] to nfce_user

grant select,insert,delete,update on [NFCeIn].[TempoMedioProcessamento] to nfce_user

use [NFCe_In_XML_DB]

grant select,insert,delete,update on [NFCeIn].[LoteCompl] to nfce_user

use [NFCe_Out_DB]

grant select,insert,update on [NFCeOut].[ValidacaoCertificadoLog] to nfce_user

grant select,insert,update on [NFCeOut].[TpDocumento] to nfce_user

grant select,insert,update on [NFCeOut].[TipoDestinatario] to nfce_user

grant select,insert,update on [NFCeOut].[ProtocoloRejeitado] to nfce_user

grant select,insert,update on [NFCeOut].[Protocolo] to nfce_user

grant select,insert,update on [NFCeOut].[NFCe] to nfce_user

grant select,insert,update on [NFCeOut].[LogUsoIndevido] to nfce_user

grant select,insert,update on [NFCeOut].[LogLoteEventoComErro] to nfce_user

grant select,insert,update on [NFCeOut].[InutNFCe] to nfce_user

grant select,insert,update on [NFCeOut].[Evento] to nfce_user

grant select,insert,update on [NFCeOut].[DadosTransmissao] to nfce_user

grant select,insert,update on [NFCeOut].[DadosApoio] to nfce_user

grant select,insert,update on [NFCeOut].[CodMunIBGE] to nfce_user

grant select,insert,update on [NFCeOut].[Certificado] to nfce_user

grant select,insert,update on [NFCeOut].[CancExtemporaneo] to nfce_user

grant select,insert,update on [NFCeOut].[CadespLogConsulta] to nfce_user

grant select,insert,update on [NFCeOut].[AutonumberProtocolo2] to nfce_user

grant select,insert,update on [NFCeOut].[AutonumberProtocolo] to nfce_user

use [NFCe_Out_Constraint_DB]

grant select,insert,update on [NFCeOut].[ConfigSistema] to nfce_user

grant select,insert,update on [NFCeOut].[ContribuintesHabilitados] to nfce_user

grant select,insert,update on [NFCeOut].[Evento_Constraint] to nfce_user

grant select,insert,update on [NFCeOut].[NFCe_Constraint] to nfce_user

use [NFCe_Out_XML_DB]

grant select,insert,update on [NFCeOut].[Certificado] to nfce_user

grant select,insert,update on [NFCeOut].[EventoXml] to nfce_user

grant select,insert,update on [NFCeOut].[InutXml] to nfce_user

grant select,insert,update on [NFCeOut].[NFCeXml] to nfce_user
```

