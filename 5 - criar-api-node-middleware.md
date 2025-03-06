Com certeza! Middlewares são uma parte essencial do Express e podem ser usados para várias finalidades, como autenticação, logging, manipulação de erros, entre outros. Vamos adicionar alguns middlewares ao nosso código para ilustrar como eles podem ser usados.

### Passo 1: Adicionar um Middleware de Logging

1. **Crie um middleware de logging** para registrar todas as requisições:
   ```javascript
   const logger = (req, res, next) => {
       console.log(`${req.method} ${req.url}`);
       next();
   };
   ```

2. **Adicione o middleware de logging ao seu servidor**:
   ```javascript
   app.use(logger);
   ```

### Passo 2: Adicionar um Middleware de Autenticação Simples

1. **Crie um middleware de autenticação** que verifica se um token está presente nos cabeçalhos da requisição:
   ```javascript
   const authenticate = (req, res, next) => {
       const token = req.headers['authorization'];
       if (token === 'seu-token-secreto') {
           next();
       } else {
           res.status(403).send('Acesso negado');
       }
   };
   ```

2. **Adicione o middleware de autenticação às rotas que precisam de proteção**:
   ```javascript
   app.use('/api', authenticate);
   ```

### Passo 3: Atualizar o Código do Servidor

1. **Atualize o arquivo `server.js`** para incluir os middlewares:
   ```javascript
   const express = require('express');
   const bodyParser = require('body-parser');
   const cors = require('cors');
   const fs = require('fs');
   const path = require('path');
   const prompt = require('prompt');

   const app = express();
   const initialPort = 3000;

   app.use(bodyParser.json());
   app.use(cors());

   // Middleware de logging
   const logger = (req, res, next) => {
       console.log(`${req.method} ${req.url}`);
       next();
   };
   app.use(logger);

   // Middleware de autenticação
   const authenticate = (req, res, next) => {
       const token = req.headers['authorization'];
       if (token === 'seu-token-secreto') {
           next();
       } else {
           res.status(403).send('Acesso negado');
       }
   };
   app.use('/api', authenticate);

   // Carregar dinamicamente todas as rotas na pasta 'routes'
   fs.readdirSync(path.join(__dirname, 'routes')).forEach(file => {
       const route = require(`./routes/${file}`);
       app.use('/api', route);
   });

   // Função para iniciar o servidor em uma porta específica
   const startServer = (port) => {
       app.listen(port, () => {
           console.log(`Servidor rodando na porta ${port}`);
       }).on('error', (err) => {
           if (err.code === 'EADDRINUSE') {
               console.log(`Porta ${port} está ocupada.`);
               const newPort = port + 1;
               promptUserForNewPort(newPort);
           } else {
               console.error(err);
           }
       });
   };

   // Função para perguntar ao usuário se deseja usar a nova porta
   const promptUserForNewPort = (newPort) => {
       prompt.start();
       const schema = {
           properties: {
               useNewPort: {
                   description: `Porta ${newPort} está disponível. Deseja usar essa porta? (sim/não)`,
                   pattern: /^(sim|não|s|n)$/i,
                   message: 'Responda com "sim" ou "não"',
                   required: true
               }
           }
       };

       prompt.get(schema, (err, result) => {
           if (result.useNewPort.toLowerCase() === 'sim' || result.useNewPort.toLowerCase() === 's') {
               startServer(newPort);
           } else {
               console.log('Servidor não iniciado.');
           }
       });
   };

   // Iniciar o servidor na porta inicial
   startServer(initialPort);
   ```

```javascript
import { json } from 'express'
export const bodyParser = json()
```

```javascript
export const contentType = (req, res, next) => {
  res.type('json');
  next();
}
```

```javascript
export const cors = (req, res, next) => {
  res.set('access-control-allow-origin', '*');
  res.set('access-control-allow-headers', '*');
  res.set('access-control-allow-methods', '*');
  next();
}
```

### Passo 4: Testar a API

1. **Inicie o servidor**:
   ```bash
   node server.js
   ```

2. **Use uma ferramenta como Postman ou Insomnia** para testar os endpoints, agora com os middlewares de logging e autenticação:
   - **POST /api/users**: Cria um novo usuário (necessário incluir o cabeçalho `Authorization` com o valor `seu-token-secreto`).
   - **GET /api/users**: Lista todos os usuários (necessário incluir o cabeçalho `Authorization` com o valor `seu-token-secreto`).
   - **PUT /api/users/:id**: Atualiza um usuário existente (necessário incluir o cabeçalho `Authorization` com o valor `seu-token-secreto`).
   - **DELETE /api/users/:id**: Deleta um usuário (necessário incluir o cabeçalho `Authorization` com o valor `seu-token-secreto`).

Com esses middlewares, você pode adicionar funcionalidades adicionais ao seu servidor de forma modular e reutilizável. Se precisar de mais alguma coisa, é só avisar! 😊