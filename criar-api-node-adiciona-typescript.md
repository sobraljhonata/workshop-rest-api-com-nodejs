Adicionar TypeScript ao seu projeto pode trazer muitos benefícios, como tipagem estática e melhor suporte a IDEs. Vou te guiar pelo processo de configuração do TypeScript no seu projeto.

### Passo 1: Instalar Dependências do TypeScript

1. **Instale as dependências necessárias**:
   ```bash
   npm install typescript ts-node @types/node @types/express --save-dev
   ```

2. **Inicialize o TypeScript no projeto**:
   ```bash
   npx tsc --init
   ```

### Passo 2: Configurar o TypeScript

1. **No arquivo `tsconfig.json`**, ajuste as configurações conforme necessário. Aqui está um exemplo básico:
   ```json
   {
       "compilerOptions": {
           "target": "ES6",
           "module": "commonjs",
           "strict": true,
           "esModuleInterop": true,
           "skipLibCheck": true,
           "outDir": "./dist",
           "rootDir": "./src"
       },
       "include": ["src/**/*"],
       "exclude": ["node_modules"]
   }
   ```

### Passo 3: Estrutura do Projeto

1. **Reorganize a estrutura do projeto para usar a pasta `src`**:
   ```
   user-api/
   ├── node_modules/
   ├── src/
   │   ├── routes/
   │   │   ├── userRoutes.ts
   │   │   └── anotherResourceRoutes.ts
   │   ├── composites/
   │   │   └── routeComposite.ts
   │   └── server.ts
   ├── tsconfig.json
   ├── package.json
   └── package-lock.json
   ```

### Passo 4: Atualizar os Arquivos para TypeScript

1. **Atualize o arquivo `src/composites/routeComposite.ts`**:
   ```typescript
   import { Router, Request, Response } from 'express';

   const createCompositeRouter = () => {
       const router = Router();
       const routes: { path: string, method: 'get' | 'post' | 'put' | 'delete', handler: (req: Request, res: Response) => void }[] = [];

       const addRoute = (path: string, method: 'get' | 'post' | 'put' | 'delete', handler: (req: Request, res: Response) => void) => {
           routes.push({ path, method, handler });
       };

       const applyRoutes = () => {
           routes.forEach(route => {
               routerroute.method;
           });
       };

       const getRouter = () => {
           applyRoutes();
           return router;
       };

       return {
           addRoute,
           getRouter
       };
   };

   export default createCompositeRouter;
   ```

2. **Atualize o arquivo `src/routes/userRoutes.ts`**:
   ```typescript
   import { Request, Response } from 'express';
   import createCompositeRouter from '../composites/routeComposite';

   const userRouter = createCompositeRouter();

   interface User {
       id: string;
       name: string;
       email: string;
   }

   let users: User[] = [];

   // Endpoint para criação de usuário
   userRouter.addRoute('/users', 'post', (req: Request, res: Response) => {
       const user: User = req.body;
       users.push(user);
       res.status(201).send('Usuário criado com sucesso!');
   });

   // Endpoint para listagem de usuários
   userRouter.addRoute('/users', 'get', (req: Request, res: Response) => {
       res.json(users);
   });

   // Endpoint para atualização de usuário
   userRouter.addRoute('/users/:id', 'put', (req: Request, res: Response) => {
       const id = req.params.id;
       const updatedUser: User = req.body;
       users = users.map(user => user.id === id ? updatedUser : user);
       res.send('Usuário atualizado com sucesso!');
   });

   // Endpoint para deletar usuário
   userRouter.addRoute('/users/:id', 'delete', (req: Request, res: Response) => {
       const id = req.params.id;
       users = users.filter(user => user.id !== id);
       res.send('Usuário deletado com sucesso!');
   });

   export default userRouter.getRouter();
   ```

3. **Atualize o arquivo `src/routes/anotherResourceRoutes.ts`**:
   ```typescript
   import { Request, Response } from 'express';
   import createCompositeRouter from '../composites/routeComposite';

   const resourceRouter = createCompositeRouter();

   interface Resource {
       id: string;
       name: string;
   }

   let resources: Resource[] = [];

   // Endpoint para criação de recurso
   resourceRouter.addRoute('/resources', 'post', (req: Request, res: Response) => {
       const resource: Resource = req.body;
       resources.push(resource);
       res.status(201).send('Recurso criado com sucesso!');
   });

   // Endpoint para listagem de recursos
   resourceRouter.addRoute('/resources', 'get', (req: Request, res: Response) => {
       res.json(resources);
   });

   // Endpoint para atualização de recurso
   resourceRouter.addRoute('/resources/:id', 'put', (req: Request, res: Response) => {
       const id = req.params.id;
       const updatedResource: Resource = req.body;
       resources = resources.map(resource => resource.id === id ? updatedResource : resource);
       res.send('Recurso atualizado com sucesso!');
   });

   // Endpoint para deletar recurso
   resourceRouter.addRoute('/resources/:id', 'delete', (req: Request, res: Response) => {
       const id = req.params.id;
       resources = resources.filter(resource => resource.id !== id);
       res.send('Recurso deletado com sucesso!');
   });

   export default resourceRouter.getRouter();
   ```

4. **Atualize o arquivo `src/server.ts`**:
   ```typescript
   import express from 'express';
   import bodyParser from 'body-parser';
   import cors from 'cors';
   import fs from 'fs';
   import path from 'path';
   import prompt from 'prompt';

   const app = express();
   const initialPort = 3000;

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
   startServer(initialPort);
   ```

### Passo 5: Atualizar os Scripts do `package.json`

1. **Atualize os scripts no arquivo `package.json`** para compilar e rodar o TypeScript:
   ```json
   "scripts": {
       "start": "ts-node src/server.ts",
       "build": "tsc"
   }
   ```

### Passo 6: Testar a API

1. **Inicie o servidor**:
   ```bash
   npm start
   ```

2. **Use uma ferramenta como Postman ou Insomnia** para testar os endpoints:
   - **POST /api/users**: Cria um novo usuário.
   - **GET /api/users**: Lista todos os usuários.
   - **PUT /api/users/:id**: Atualiza