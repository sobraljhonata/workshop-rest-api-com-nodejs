Ótima ideia! Usar variáveis de ambiente para armazenar informações sensíveis como senhas e configurações é uma prática recomendada para melhorar a segurança da aplicação. Vamos configurar a biblioteca `dotenv` para gerenciar essas variáveis.

### Passo 1: Instalar a Biblioteca `dotenv`

1. **Instale a biblioteca `dotenv`**:
   ```bash
   npm install dotenv
   ```

### Passo 2: Criar o Arquivo `.env`

1. **Crie um arquivo chamado `.env`** na raiz do projeto e adicione as variáveis de ambiente:
   ```env
   PORT=3000
   DB_PASSWORD=secreta
   ```

### Passo 3: Atualizar o Código para Usar Variáveis de Ambiente

1. **No arquivo `src/server.ts`**, atualize o código para carregar as variáveis de ambiente:
   ```typescript
   import express from 'express';
   import bodyParser from 'body-parser';
   import cors from 'cors';
   import fs from 'fs';
   import path from 'path';
   import prompt from 'prompt';
   import dotenv from 'dotenv';

   dotenv.config();

   const app = express();
   const initialPort = process.env.PORT || 3000;

   app.use(bodyParser.json());
   app.use(cors());

   // Middleware de logging
   const logger = (req: express.Request, res: express.Response, next: express.NextFunction) => {
       console.log(`${req.method} ${req.url}`);
       next();
   };
   app.use(logger);

   // Middleware de autenticação
   const authenticate = (req: express.Request, res: express.Response, next: express.NextFunction) => {
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
       const route = require(`./routes/${file}`).default;
       app.use('/api', route);
   });

   // Função para iniciar o servidor em uma porta específica
   const startServer = (port: number) => {
       app.listen(port, () => {
           console.log(`Servidor rodando na porta ${port}`);
       }).on('error', (err: NodeJS.ErrnoException) => {
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
   const promptUserForNewPort = (newPort: number) => {
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
   startServer(Number(initialPort));
   ```

### Passo 4: Atualizar o `.gitignore`

1. **Adicione o arquivo `.env` ao `.gitignore`** para garantir que ele não seja versionado:
   ```gitignore
   node_modules/
   dist/
   .env
   ```

### Passo 5: Testar a Configuração

1. **Inicie o servidor**:
   ```bash
   npm start
   ```

2. **Verifique se o servidor está rodando na porta especificada no arquivo `.env`.

Com essas etapas, você terá configurado a biblioteca `dotenv` para gerenciar variáveis de ambiente, melhorando a segurança da sua aplicação. Se precisar de mais alguma coisa, é só avisar! 😊