[Selenium](https://www.selenium.dev/) é um projeto que abrange uma variedade de ferramentas e bibliotecas que permitem e suportam a automação de navegadores da web.

Selenium Webdriver, por sua vez, é uma biblioteca que permite o controle das interações com o navegador através do protocolo WebDriver.

Utilizando em conjunto o Selenium Webdriver, uma implementação do WebDriver do browser de sua escolha, e um framework de testes unitários (MS Test, NUnit, XUnit), é possível criar testes automatizados da interação de um usuário com a aplicação.

Para esta documentação será utilizado o MS Test e o navegador Google Chrome.

[[_TOC_]]

# Instalação
## Selenium WebDriver
Instalar o pacote [Selenium.WebDriver](https://www.nuget.org/packages/Selenium.WebDriver)

##ChromeDriver 
Implementação do WebDriver para o Google Chrome. Instalar o pacote [Selenium.WebDriver.ChromeDriver](https://www.nuget.org/packages/Selenium.WebDriver.ChromeDriver)

O ChromeDriver necessita dos arquivos binários do navegador Google Chrome. Caso o navegator já esteja instalado em seu local padrão, os arquivos serão localizados automaticamente. É possível utilizar o ChromeDriver sem o navegador instalado, mas mesmo assim será necessário possuir os arquivos do navegador, e informar no construtor do ChromeDriver a sua localização.

A _major version_ do ChromeDriver deve ser igual à do navegador instalado. Logo, se o navegador for atualizado, será necessário atualizar o pacote Selenium.WebDriver.ChromeDriver também.

# Utilização básica
Independentemente da implementação do WebDriver utilizada, o objeto que fornece o acesso ao navegador é o que implementa a interface IWebDriver. No caso do Google Chrome, a classe que implementa a interface é a ChromeDriver. 

Partindo da instância do IWebDriver, é possível buscar um elemento que representa uma tag do HTML, e realizar operações no elemento recuperado (clicar, pressionar teclas, etc.).

O exemplo abaixo realiza uma busca no google pelo texto "selenium". No resultado da busca, procura pelo link que contém o texto "Selenium" e espera que ele contenha o endereço para o site https://www.selenium.dev/.

```
using Microsoft.VisualStudio.TestTools.UnitTesting;
using OpenQA.Selenium;
using OpenQA.Selenium.Chrome;

namespace Sefaz.Exemplo
{
    [TestClass]
    public class Teste
    {
        [TestMethod]
        public void HelloSeleniumWorld()
        {
            using (IWebDriver webDriver = new ChromeDriver())
            {
                webDriver.Url = "https://google.com";

                IWebElement busca = webDriver.FindElement(By.Name("q"));
                busca.SendKeys("selenium");
                busca.Submit();

                IWebElement linkParaSiteSelenium = webDriver.FindElement(By.PartialLinkText("Selenium"));

                Assert.AreEqual("https://www.selenium.dev/", linkParaSiteSelenium.GetAttribute("href"));

                webDriver.Quit();
            }
        }
    }
}
```

É importante usar a instrução _using_ ou chamar o método Dispose na instância do IWebDriver, para que o processo do ChromeDriver seja finalizado.

Seguem alguns exemplos de métodos disponíveis no Selenium WebDriver. A documentação completa para C# pode ser encontrada em https://www.selenium.dev/selenium/docs/api/dotnet/index.html.

###Buscar elementos
````
webDriver.FindElement(By.ClassName("classeCss"));
webDriver.FindElement(By.CssSelector("css"));
webDriver.FindElement(By.Id("id"));
webDriver.FindElement(By.LinkText("texto do link"));
webDriver.FindElement(By.Name("name"));
webDriver.FindElement(By.PartialLinkText("parte do texto"));
webDriver.FindElement(By.TagName("input"));
webDriver.FindElement(By.XPath("//*[@id='editor']"));
````

###Executar JavaScript
Deve ser realizado um _cast_ da instância do WebDriver para a interface IJavaScriptExecutor.

O tipo C# do retorno a ser usado deve ser condizente com o tipo JavaScript retornado (https://www.selenium.dev/selenium/docs/api/dotnet/html/M_OpenQA_Selenium_IJavaScriptExecutor_ExecuteScript.htm).
````
long resultado = (long)((IJavaScriptExecutor)webDriver).ExecuteScript("return 1 + 1");

Assert.AreEqual(2, resultado);
````
###Acessar janela Alert

````
IAlert alert = webDriver.SwitchTo().Alert();
alert.Accept();
alert.Dismiss();
````

###Aguardar até uma condição ser satisfeita
É possível aguardar uma condição através do WebDriverWait. A classe se encontra no pacote [Selenium.Support](https://www.nuget.org/packages/Selenium.Support/).
````
var wait = new WebDriverWait(driver, TimeSpan.FromSeconds(30));

//aguarda até que o elemento com id "id" seja encontrado no HTML.
wait.Until(d => d.FindElement(By.Id("id")));
````


#Dicas e utilidades
## Modo _Headless_
Alguns navegadores suportam a navegação _headless_ (sem interface visual). Além de consumir menos recursos, ser mais rápido, e não ocupar a área de trabalho do usuário, o código pode ser executado em um servidor de testes sem componentes gráficos.

No caso do Google Chrome, esta opção é ativada na linha de comando pela instrução --headless. Esta instrução pode ser passada ao ChromeDriver da seguinte forma:

````
var options = new ChromeOptions();
options.AddArgument("headless");

using (IWebDriver webDriver = new ChromeDriver(options))
{
    ...
}
````


## Screenshot
Quando há um teste sendo finalizado com erro, e possível incluir uma captura de tela antes de finalizar o teste. No MS Test, o código a ser executado no final de cada teste fica em [TestCleanup]:

````
public TestContext TestContext { get; set; }

[TestCleanup]
public void TestCleanup()
{
    if (TestContext.CurrentTestOutcome == UnitTestOutcome.Failed)
    {
        var screenshotFileName = "screenshot.png";

        Screenshot screenshot = ((ITakesScreenshot)webDriver).GetScreenshot();
        screenshot.SaveAsFile(screenshotFileName, ScreenshotImageFormat.Png);

        Console.WriteLine($"Error screenshot taken: {screenshotFileName});
    }
}
````

A imagem da captura do código acima fica limitada ao tamanho da tela do browser, perdendo a parte que fica "escondida" na barra de rolagem. Para capturar todo o html, é preciso aumentar o tamanho da tela antes de realizar a captura:

````
public static class IWebDriverExtensions
{
    public static Screenshot TakeScreenshot(this IWebDriver webDriver)
    {
        //guarda o tamanho original
        var originalSize = webDriver.Manage().Window.Size;

        var requiredWidth = (long)((IJavaScriptExecutor)webDriver).ExecuteScript("return document.body.parentNode.scrollWidth");
        var requiredHeight = (long)((IJavaScriptExecutor)webDriver).ExecuteScript("return document.body.parentNode.scrollHeight");

        //ajusta o tamanho da janela para o tamanho do html
        webDriver.Manage().Window.Size = new Size((int)requiredWidth, (int)requiredHeight);

        var screenshot = ((ITakesScreenshot)webDriver).GetScreenshot();

        //restaura o tamanho original
        webDriver.Manage().Window.Size = originalSize;

        return screenshot;
    }
}
````
##Encerrando processos do ChromeDriver
Existem relatos de situações em que o processo do ChromeDriver não é finalizado mesmo utilizando o _using_ ou a chamada ao método Dispose (Ex: https://stackoverflow.com/questions/21320837/release-selenium-chromedriver-exe-from-memory). O código a seguir é um exemplo para forçar o encerramento do processo.
````
private static void KillProcessAndChildren(string p_name)
{
    var searcher = new ManagementObjectSearcher
        ("Select * From Win32_Process Where Name = '" + p_name + "'");
    var moc = searcher.Get();
    foreach (var mo in moc)
    {
        try
        {
            KillProcessAndChildren(Convert.ToInt32(mo["ProcessID"]));
        }
        catch (ArgumentException)
        {
            break;
        }
    }
}

private static void KillProcessAndChildren(int pid)
{
    var searcher = new ManagementObjectSearcher
        ("Select * From Win32_Process Where ParentProcessID=" + pid);
    var moc = searcher.Get();
    foreach (var mo in moc)
    {
        try
        {
            KillProcessAndChildren(Convert.ToInt32(mo["ProcessID"]));
        }
        catch
        {
            break;
        }
    }

    try
    {
        Process proc = Process.GetProcessById(pid);
        proc.Kill();
    }
    catch (ArgumentException)
    {
        // Process already exited.
    }
}

[AssemblyCleanup] //Ou [TestCleanup] caso o webDriver esteja sendo instanciado a cada teste
public static void AssemblyCleanup()
{
    KillProcessAndChildren("chromedriver.exe");
}
````

#Informações adicionais
- Page Object Model - facilita o reuso de código e desacoplamento dos testes com o WebDriver.
https://www.selenium.dev/documentation/test_practices/encouraged/page_object_models/