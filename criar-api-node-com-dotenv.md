Vamos configurar a aplicação para usar variáveis de ambiente tanto dentro do container Docker quanto através de um arquivo `.env`. Isso permitirá flexibilidade na configuração da aplicação.

### Passo 1: Atualizar o Dockerfile

1. **Atualize o `Dockerfile`** para copiar o arquivo `.env` para dentro do container:
   ```dockerfile
   # Usar a imagem oficial do Node.js como base
   FROM node:14

   # Definir o diretório de trabalho dentro do contêiner
   WORKDIR /usr/src/app

   # Copiar o package.json e o package-lock.json
   COPY package*.json ./

   # Instalar as dependências
   RUN npm install

   # Copiar o restante do código da aplicação
   COPY . .

   # Copiar o arquivo .env
   COPY .env .env

   # Expor a porta que a aplicação vai rodar
   EXPOSE 3000

   # Comando para iniciar a aplicação
   CMD ["npm", "start"]
   ```

### Passo 2: Atualizar o docker-compose.yml

1. **Atualize o `docker-compose.yml`** para incluir variáveis de ambiente:
   ```yaml
   version: '3.8'

   services:
     app:
       build: .
       ports:
         - '3000:3000'
       environment:
         - PORT=3000
         - DB_USER=${DB_USER}
         - DB_PASSWORD=${DB_PASSWORD}
         - DB_HOST=${DB_HOST}
       volumes:
         - .:/usr/src/app
         - /usr/src/app/node_modules
       depends_on:
         - mongo

     mongo:
       image: mongo:4.0.3
       ports:
         - '27017:27017'
       volumes:
         - mongo-data:/data/db

   volumes:
     mongo-data:
   ```

### Passo 3: Criar o Arquivo .env

1. **Crie um arquivo `.env`** na raiz do projeto com as variáveis de ambiente:
   ```env
   PORT=3000
   DB_USER=admin
   DB_PASSWORD=secreta
   DB_HOST=mongo
   ```

### Passo 4: Atualizar o Código para Usar Variáveis de Ambiente

1. **No arquivo `src/server.ts`**, certifique-se de que o código está usando as variáveis de ambiente corretamente:
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

### Passo 5: Testar a Configuração

1. **Construa e inicie os contêineres**:
   ```bash
   docker-compose up --build
   ```

2. **Verifique se os contêineres estão rodando**:
   ```bash
   docker ps
   ```

3. **Acesse a aplicação em** `http://localhost:3000`.

Com essas etapas, você terá configurado a aplicação para usar variáveis de ambiente tanto dentro do container Docker quanto através de um arquivo `.env`, proporcionando flexibilidade e segurança na configuração da aplicação. Se precisar de mais alguma coisa, é só avisar! 😊