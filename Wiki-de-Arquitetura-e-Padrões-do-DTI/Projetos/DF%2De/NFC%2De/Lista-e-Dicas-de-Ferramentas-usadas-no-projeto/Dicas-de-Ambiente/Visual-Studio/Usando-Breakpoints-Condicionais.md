Além do modo tradicional de usar o breakpoint, ou seja, acionar o debugador quando aquele ponto for executado, podemos também usar uma condição de parada. Por exemplo, parar apenas se uma dada variável tiver um dado conteúdo.
Veja aqui como fazer:
 
 * Crie um breakpoint no modo tradicional
 * Acione a janela de Breakpoints (Crtl-D, B) ou menu Debug / Windows / Breakpoints
 * Clique no breakpoint desejado com o botão direito do mouse e a você verá o menu de contexto como mostrado a seguir:

![VS2010-BkptCond-01.jpeg](/.attachments/VS2010-BkptCond-01-b9e2140e-b71d-450f-b4df-a155af220657.jpeg)

 * Selecione a opção "Condition" e a dialog abaixo será exibida. Como condição entre o teste que deseja efetuar.

![VS2010-BkptCond-02.jpeg](/.attachments/VS2010-BkptCond-02-7aafc5da-33bd-48e8-8c33-b429b8e97adf.jpeg)

 * e pronto! Basta depurar seu programa que o breakpoint será ativado apenas quando a condição for atingida.