#Configuração do ambiente para uso do Node.js e NPM (Node Package Manager)
Verificar o proxy:

```
npm config get proxy
npm config get https-proxy
```


Configuração do proxy para npm em estações de trabalho:
 
```
npm config set proxy http://cs_git_servico:git_cia@10.216.28.109:8080
npm config set https-proxy http://cs_git_servico:git_cia@10.216.28.109:8080
```
Se houver problemas com o proxy, remova e reconfigure:
 
```
npm config rm proxy
npm config rm https-proxy
```

Pode também limpar o cache:
`npm cache clean --force`
 
Configuração do proxy para npm em servidores:
 
```
npm config set proxy 10.216.28.112:8080
npm config set https-proxy 10.216.28.112:8080
```

Desativar/ativar verificação de ssl:
 `npm set strict-ssl false/true`

É possível ver as configurações do npm no arquivo  
c:\users\\_seu nome de usuário_\\.npmrc

Para testar o proxy utilize:
 ```
 npm ping
 ```

 
Atualizar o node:
 `npm install npm -g`


Se houver um erro "cb() never called!", atualize o nodejs, que apresenta esse bug em algumas versões.

