Para inserir contribuintes em homologação na NF-e, deve-se executar a proc [NFe_Out].[dbo].[ContribuintesHabilitadosInsert] na SRVNFEHML:

`EXEC [dbo].[ContribuintesHabilitadosInsert]  @CNPJ = N'',  @IE = N'956224310481', @Empresa = N'', @indObrig = null, @dVigObrig = null`

Deve-se preencher apenas o CNPJ e a Empresa. A IE é fixa (fake) em homologação.

Pode-se fazer isso diretamente com o usuário credenciamentonfe_user, ou abrir incidente.