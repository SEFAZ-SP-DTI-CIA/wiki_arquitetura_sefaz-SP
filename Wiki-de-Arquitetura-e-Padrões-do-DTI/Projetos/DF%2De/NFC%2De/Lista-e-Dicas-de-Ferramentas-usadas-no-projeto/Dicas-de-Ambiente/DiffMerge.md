Ferramenta de Diff: [DiffMerge](http://www.sourcegear.com/diffmerge/)
-- bem melhor que a ferramenta default do Windows
-- integrada ao Windows Explorer -> permite fazer diff de dois arquivos mostrados no Windows Explorer
-- para habilitá-la no VisualStudio como ferramenta default de diff:
 * [Tools] -> [Options]
 * [Source Control] -> [Visual Studio Team Foundation]
 * [Configure User Tools]
 * [Add] 
 * [Extension] .*

# 
 * [Operation] Compare
 * [Commnand] C:\Program Files\SourceGear\DiffMerge\DiffMerge.exe
 * [Arguments] %1 %2

# 
 * [Operation] Merge
 * [Command] C:\Program Files\SourceGear\DiffMerge\DiffMerge.exe
 * [Arguments] -merge -result=%4 %2 %3 %1