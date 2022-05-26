Para criar um pipeline de release, acesse a página do projeto, clique em «Pipelines» e em «Releases», depois clique no botão «+ New» e em «New release pipeline».

Para aplicações web que utilizam servidores IIS, selecione o template “IIS website deployment”, mais simples e seguro.
![TemplateRelease.png](/.attachments/TemplateRelease-ea16e327-fcde-4d22-99d6-67d1188c814d.png =400x)

O Azure cria o primeiro Stage do pipeline. Atribua, a esse estágio, um nome que identifique o ambiente (DEV ou QA).
![NewPipeline.png](/.attachments/NewPipeline-d73a8e9c-03ec-42fd-80aa-3b4ec9a4ce46.png =600x)

No release pipeline criado, altere o texto “New release pipeline” para algo mais esclarecedor, como o nome da aplicação.

Sendo um pipeline de deploy de aplicação, no painel “Artifacts”, clique em «Add an artifact». Selecione um “Source (build pipeline)” para ligar o pipeline de release a um pipeline de build.
![Artifacts.PNG](/.attachments/Artifacts-d9944fe1-1ff9-4290-8198-a5eaa6edb9a8.PNG =600x)
Para configurar o pipeline como entrega contínua (CD), clique no ícone de raio e habilite o trigger:
![CD.png](/.attachments/CD-69112fe1-c176-4de7-87e7-af2d2f69cf42.png =600x)

>Havendo a configuração de um artifact, todo stage desse pipeline começará carregando seus arquivos em uma pasta local no servidor onde se encontra o agent job selecionado para a execução.
![Download_Artifacts.png](/.attachments/Download_Artifacts-2eb1f086-a821-424d-9c21-8222637dca3f.png =250x)
`Created artifacts directory: D:\Deployment_Agent\_work\r10\a`