Ferramenta de Diff: [Winmerge](http://winmerge.org/)

-bem melhor que a ferramenta default do Windows
-integrada ao Windows Explorer -> permite fazer diff de dois arquivos mostrados no Windows Explorer
-para habilitá-la no VisualStudio como ferramenta default de diff:
 * [Tools] -> [Options]
 * [Source Control] -> [Visual Studio Team Foundation]
 * [Configure User Tools]
 * [Add] 
 * [Extension] .cs
 * [Operation] Compare
 * [Commnand] C:\Program Files\WinMerge\WinMergeU.exe
 * [Arguments] %1 %2
 * Depois configure outras extensions: .config, .sql , etc.
 

Adição:
-------

Também pode ser configurada para fazer o Merge no TFS. Veja o que achei nesta página: http://jonfuller.codingtomusic.com/2008/08/26/tfs-using-winmerge-as-your-mergediff-tool/

Copiando aqui:


```
Go To: Tools/Options/SourceControl/Visual Studio Team Foundation Server/Configure User Tools

Click "Add..."
Extension:  .*
Operation: Compare
Command: C:\Program Files\WinMerge\WinMergeU.exe
Arguments: /e /wl /dl %6 /dr %7 %1 %2
Click "Add..."

Extension:  .*
Operation: Merge
Command: C:\Program Files\WinMerge\WinMergeU.exe
Arguments: /ub /dl %6 /dr %7 %1 %2 %4
```
