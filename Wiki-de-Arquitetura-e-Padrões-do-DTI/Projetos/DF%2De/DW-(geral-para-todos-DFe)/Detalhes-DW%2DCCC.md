## ​Descrição das Procedures:

`[DW].[usp_CCC_select]`
- Deleta N registros da DW.Queue
- Inser os N registros na ProcessamentoQueue, mantendo pkey e timestampReg
É chamado no início da extração

`[DWCCC].[usp_LogErro]`
- passa GUID, NSUInc, NSUAtu, etc.
- Insere um registro na tabela logErros
- Pega aquele registro da ProcessamentoQueue e joga na ResultadoProcessamento, com flag de erro = 1
É chamado em várias situações de erro.

`[DWCCC].[usp_Grava_Resultado_Com_Sucesso_Por_UID]`
- Passa GUID
- Deleta N registros da ProcessamentoQueue por GUID
- Insere os N registros na ResultadoProcessamento com flag de erro = 0 , mantendo pkey e timestampReg
É chamado independente de ter dado erro ou não ao gravar dados do Sybase, ao final do processo.


## Procedimento de erro

Se houver erro na execução do DW-CCC, deve-se verificar se existem erros na tabela ResultadoProcessamento ou na FilaProcessamento:


```
SELECT '' AS '[ETL_NFE].[DWCCC].[ResultadoProcessamento] - com erro', *
  FROM [ETL_NFE].[DWCCC].[ResultadoProcessamento] (NOLOCK)
 WHERE houveErro = 1
 ORDER BY dataProcessamento DESC​

SELECT '' AS '[ETL_NFE].[DWCCC].[ProcessamentoQueue]',*
  FROM [ETL_NFE].[DWCCC].[ProcessamentoQueue] (NOLOCK)
ORDER BY pKey DESC
```



1) Havendo registros na ProcessamentoQueue (situação rara que deve ser investigada, executável deve ter travado ou morrido), deve-se reenfileirar os registros:


```
SET IDENTITY_INSERT [CCC].[DW].[Queue] ON
GO

DELETE FROM [ETL_NFE].[DWCCC].[ProcessamentoQueue]
OUTPUT 
    DELETED.pKey,
    DELETED.NSUInc,
    DELETED.NSUAtu,
    DELETED.timestampReg
INTO 
    [CCC].[DW].[QUEUE] 
    (pKey, 
     NSUInc, 
     NSUAtu, 
     timestampReg)

SET IDENTITY_INSERT [CCC].[DW].[Queue] OFF
GO
```


2) Havendo registros de erro na ResultadoProcessamento, deve-se reenfileirar esses registros antes de processar novamente o DW:


```
SET IDENTITY_INSERT [CCC].[DW].[Queue] ON
GO

DELETE FROM [ETL_NFE].[DWCCC].[ResultadoProcessamento]
OUTPUT 
    DELETED.pKey,
    DELETED.NSUInc,
    DELETED.NSUAtu,
    DELETED.timestampReg
INTO 
    [CCC].[DW].[QUEUE] 
    (pKey, 
     NSUInc, 
     NSUAtu, 
     timestampReg)
WHERE houveErro = 1 
  AND dataProcessamento > '<aaaa>-<MM>-<dd> <hh>:<mm>:<ss>' -- esse AND é para não reenfileirar registros antigos, deve ser ajustado para o horário imediatamente anterior à execução que falhou 

SET IDENTITY_INSERT [CCC].[DW].[Queue] OFF
GO
```
