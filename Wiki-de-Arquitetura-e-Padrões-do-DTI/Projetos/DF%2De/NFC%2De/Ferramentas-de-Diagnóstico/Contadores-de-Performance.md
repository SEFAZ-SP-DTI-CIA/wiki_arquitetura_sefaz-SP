[[_TOC_]]

# Tipos de contadores
Existem vários contadores de performance criados na NFC-e. São eles: 

##1) Contadores relacionados a lotes de NFC-e no Web Service
- Lotes_Assincronos_Recebidos_Por_Segundo            
- Lotes_Assincronos_Recebidos_Por_Segundo_Com_Sucesso 
- Tempo_Recepção_Lote_Assincrono                     
- Lotes_Sincronos_Processados_Com_Sucesso            
- Lotes_Sincronos_Processados_Com_Erro               
- Lotes_Sincronos_Processados_Por_Segundo            
- Tempo_Processamento_Lote_Sincrono                     

##2) Contadores relacionados a lotes de NFC-e no Windows Service
- Lotes_Assincronos_Em_Processamento                 
- Lotes_Assincronos_Processados_Com_Sucesso
- Lotes_Assincronos_Processados_Com_Erro   
- Lotes_Assincronos_Processados_Por_Segundo
- Tempo_Processamento_Lote_Assincrono      
- Lotes_Retirados_Da_Fila_Por_Segundo      
- Tempo_Retirar_Da_Fila_Processamento      
- Tempo_Esperando_Processar                
- Threads_Ativas                           

##3) Contadores relacionados a lotes de Eventos EPEC
- Tempo_Autorização_EPEC                   
- EPECs_Processados_Com_Erro               
- EPECs_Processados_Com_Sucesso            
- EPECs_Processados_Por_Segundo            
- Tempo_Processamento_LoteEPEC             
- Lotes_EPECs_Processados                  

##4) Contadores relacionados a lotes de Eventos Cancelamento
- Cancelamentos_Processados_Com_Erro       
- Cancelamentos_Processados_Com_Sucesso    
- Tempo_Processar_Cancelamento             
- Lotes_Cancelamentos_Processados

# Visualização

Um diagrama que mostra como os contadores são usados pode ser encontrado em http://portaltfs.intra.fazenda.sp.gov.br/sites/TPC-2010/NFC-e/Shared%20Documents/General/ContadoresDePerformance.pptx

A visualização desses contadores pode ser feita nas próprias máquinas pelo "PerfMon" ou através do SCOM (http://consolescom/operationsmanager/ e http://consolescomhml/operationsmanager/).

# Limpeza dos contadores

Alguns desses contadores são do tipo "quantidade", que vão se acumulando ao longo do tempo:

- Lotes_Sincronos_Processados_Com_Erro
- Lotes_Sincronos_Processados_Com_Sucesso
- Lotes_EPECs_Processados
- EPECs_Processados_Com_Erro
- EPECs_Processados_Com_Sucesso
- Lotes_Cancelamentos_Processados
- Cancelamentos_Processados_Com_Erro
- Cancelamentos_Processados_Com_Sucesso
- Lotes_Assincronos_Processados_Com_Sucesso
- Lotes_Assincronos_Processados_Com_Erro

O comportamento desses contadores de quantidade foi alterado incluindo-se um executável que zera esses contadores. Esse programa está colocado no diretório `D:\ReiniciadorContadores` das seguintes máquinas:

## Homologação:
- SRV72085 SRV72086 SRV72087 SRV72088 (web autorizadoras NFC-e)
- SRV72089 SRV72090 SRV72091 SRV72092 (windows autorizadoras NFC-e)
- SRV11030 SRV11031                   (web autorizadoras EPEC)

## Produção:
- SRV72131 SRV72132 SRV72133 SRV72134 (web autorizadoras NFC-e)
- SRV72135 SRV72136 SRV72137 SRV72138 (windows autorizadoras NFC-e)
- SRV11083 SRV11084 SRV11085 SRV11086 (web autorizadoras EPEC)

Esse executável mencionado utiliza o "Task Scheduler" do Windows para ser chamado às 0:00h diariamente. No caso das máquinas acima, a tarefa criada nesse Task Scheduler foi exportada para um arquivo "Iniciar Contadores NFCE.xml", o qual foi guardado no mesmo diretório do executável. Assim, deve-se tomar cuidado para que, em um novo deploy desse executável, que esse XML não seja apagado pois pode ser necessário para recriar a tarefa.