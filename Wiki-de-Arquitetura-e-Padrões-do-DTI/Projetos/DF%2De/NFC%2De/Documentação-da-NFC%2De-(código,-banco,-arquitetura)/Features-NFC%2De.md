# Mecanismo de "Features" na NFC-e
 

Muitas vezes existem funcionalidades que só podem ser ativadas sob determinadas circunstâncias:
&nbsp; - código incompleto (por exemplo um developer desenvolve uma funcionalidade mas não pode ainda disponibilizá-la pois não está ainda completa, e ele tem que fazer check-in)
&nbsp; - determinada data (por exemplo quando determinada validação de uma NT seja só pode ser ativada a partir de determinada data)
&nbsp; - etc.
 
O mecanismo a ser criado se baseia em:
&nbsp; - número da feature
&nbsp; - máscara de bits na tabela de ConfigSistemas, coluna 'parametro' = 'FEATURES'
  
Mapa dos bits atualmente alocados:
 * Bit 1: _ImplementacaoCDU06EditandoVariasPaginas_
   * não utilizado, seria para habiltar o Portal NFC-e ter todas as páginas editáveis pelo Portal Fazendário. Hoje o Portal Fazendário já pode editar qualquer página do Portal NFC-e
 * Bit 2: _ImplementacaoDanfeResponsivel_
   * se ligado, o layout da Consulta Pública (resumida - DANFE) é responsivo, ou seja, ele se adapta a telas de dispositivos móveis
   * se desligado, o layout da Consulta Pública (resumida - DANFE) não é responsivo, ou seja, ele não se adapta a telas de dispositivos móveis
 * Bit 3: _ImplementacaoDanfeResponsivel_RJ_ -> depende do Bit 2 estar ligado para funcionar.
   * se ligado, o layout da Consulta Pública (resumida - DANFE) responsiva é o implementado pelo RJ
   * se desligado, o layout da Consulta Pública (resumida - DANFE) responsiva é o implementado por S
 * Bit 4: _ImplementacaoNT2015_002_
   * se ligada, implementa as alterações apontadas na NT2015_002
   * se desligada, não implementa as alterações apontadas na NT2015_002
   * Nota: essa funcionalidade será ligada independentemente do bit 4 em todos os ambientes exceto produção a partir de 1/Outubro/2015 e em produção a partir de 3/Novembro/2015.

Nota: na tabela de ConfigSistema, o valor é assumido em hexadecimal, não precisa colocar o 0x na frente. 
 
Exemplos de valores a serem colocados na coluna 'valor' na tabela de ConfigSistemas, coluna 'parametro' = 'FEATURES':
 * Ligar as features relativas aos bits 3 e 4: colocar valor "C" (que em binário é 1100)
 * Ligar as features relativas aos bits 1 e 8: colocar valor "81" (que em binário é 1000 0001)