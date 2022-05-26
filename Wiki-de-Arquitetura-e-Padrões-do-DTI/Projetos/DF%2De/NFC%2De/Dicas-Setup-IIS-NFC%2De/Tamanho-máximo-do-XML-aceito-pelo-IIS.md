# Erro HTTP 413 - Request Entity Too Large
### 

Para contornar esse erro no IIS 7.5:
 * Abrir IIS Manager
 * Selecionar projeto desejado
 * Duplo clique em Configuration Editor (ou Editor de Configurações)
 * Abrir opções de Section (ou Seção)
 * Selecionar system.webServer/serverRuntime
 * Alterar o valor de uploadAheadSize para um valor mais alto (no caso de NF-e/NFC-e,  maior que 500k  - p. ex. 1048576, que é 1M)
