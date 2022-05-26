Site com acesso aos logs de extração:
http://srv11336/Reports/report/Sefaz.NFe.MonitoracaoDW/Monitoracao


[Queries SQL usadas para teste do DW NF-e](http://tfs.intra.fazenda.sp.gov.br:8080/tfs/ADMIN/Wiki_Arquitetura/_wiki/wikis/Wiki_Arquitetura.wiki/268/Queries-DW)
​


**Re-Extrator**

A pedido do Áquila (Equipe DI DW), criamos uma aplicação Windows Form que faz a extração e carga no Sybase a partir de uma lista de chaves.

O procedimento padrão para execução dessa aplicação é:

1) Áquila avisa que vai colocar novas chaves para re-extração

2) No dia seguinte, verificar se a extração regular terminou.
No Sybase de produção, rodar os comandos:


```
commit;
select * from ods.tb_dsod_nfe_controle_execucao
select * from ods.tb_dsod_nfeuf_controle_execucao
```


Se o resultado for "cod_status <> 0" para pelo menos uma das tabelas, avisar o Áquila.
Senão, continuar o procedimento.

3) Verificar que o Áquila carregou a lista de chaves a serem extraídas.
No Sybase de produção, rodar o comando:

`SELECT COUNT(1) FROM [ods].[tb_dsod_nfe_reextracao] WHERE data_extracao IS NULL`

O resultado deve ser um número (normalmente na cada dos milhões) compatível com o que o Áquila disse que carregaria.

Até hoje, só houve extração de notas de SP. Se houver extração de outras UFs, a query deve ser ligeiramente diferente:
`SELECT COUNT(1) FROM [ods].[tb_dsod_nfeuf_reextracao] WHERE data_extracao IS NULL`

4) No servidor de Produção (SRV11336), rodar a aplicação Windows Form:
`O:\Disk19\ReExtrator_NFeDW\Sefaz.NFe.Dw.ServiceReprocessamento.Front​.exe`

 ![TelaReextrator.png](/.attachments/TelaReextrator-d3195d90-dddc-4f8c-9549-07485c869cb0.png)


5) Preenchimento dos campos:
5.1) Normalmente, colocamos 1000000 (um milhão) no campo "Limite de chaves para re-extrair".
5.2) O número de repetições depende da quantidade pedida pelo Áquila. 
5.3) As tabelas carregadas depende do que o Áquila pedir. _**A única tabela que recomendo colocar sempre é a primeira da lista ("ods.tb_dsod_nfe_tmp_doc_fiscal_prot_rec"), pois é usada para verificar quais chaves reextraídas foram carregadas no Sybase com sucesso.**_
5.4) A opção "ignorar flag de controle" é usada somente para debug ou para tentar arrumar algum problema.
5.5) A "Parada de Emergência" esperará que a execução atual termine (inclusive atualizando a flag de status para "5") antes de abortar a próxima repetição.

6) Os arquivos temporários (txt) ficam armazenados em `O:\Disk19\ReExtrator_NFeDW\ArquivosTemporariosReextraidos`

7) Logs de execução (que também são mostrados no texto de "Avisos/Erros" da interface gráfica) ficam gravados em: `O:\Disk19\ReExtrator_NFeDW\log.txt`