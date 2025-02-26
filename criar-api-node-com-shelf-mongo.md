Ótima escolha! A biblioteca `@shelf/jest-mongodb` é excelente para realizar testes com MongoDB em memória, o que facilita muito o desenvolvimento e a execução dos testes. Vamos configurar essa biblioteca no seu projeto.

### Passo 1: Instalar Dependências

1. **Instale as dependências necessárias**:
   ```bash
   npm install @shelf/jest-mongodb --save-dev
   ```

### Passo 2: Configurar o Jest

1. **Crie ou atualize o arquivo `jest.config.js`** na raiz do projeto com a seguinte configuração:
   ```javascript
   module.exports = {
       preset: '@shelf/jest-mongodb',
       testEnvironment: 'node',
       testMatch: ['**/__tests__/**/*.ts', '**/?(*.)+(spec|test).ts']
   };
   ```

2. **Crie o arquivo `jest-mongodb-config.js`** na raiz do projeto com a seguinte configuração:
   ```javascript
   module.exports = {
       mongodbMemoryServerOptions: {
           binary: {
               version: '4.0.3',
               skipMD5: true
           },
           autoStart: false,
           instance: {}
       },
       useSharedDBForAllJestWorkers: false
   };
   ```

### Passo 3: Escrever Testes Unitários com MongoDB

1. **No arquivo `tests/routes/userRoutes.test.ts`**, adicione o seguinte código:
   ```typescript
   import request from 'supertest';
   import express from 'express';
   import userRouter from '../../src/routes/userRoutes';
   import { MongoClient } from 'mongodb';

   const app = express();
   app.use(express.json());
   app.use('/api', userRouter);

   describe('User Routes with MongoDB', () => {
       let connection: MongoClient;
       let db: any;

       beforeAll(async () => {
           connection = await MongoClient.connect(global.__MONGO_URI__, {
               useNewUrlParser: true,
               useUnifiedTopology: true
           });
           db = await connection.db(global.__MONGO_DB_NAME__);
       });

       afterAll(async () => {
           await connection.close();
       });

       it('should create a new user', async () => {
           const response = await request(app)
              /users')
               .send({ id: '1', name: 'John Doe', email: 'john@example.com' });

           expect(response.status).toBe(201);
           expect(response.text).toBe('Usuário criado com sucesso!');
       });

       it('should list users', async () => {
           await request(app)
               .post('/api/users')
               .send({ id: '1', name: 'John Doe', email: 'john@example.com' });

          get('/api/users');

           expect(response.status).toBe(200);
           expect(response.body).toHaveLength(1);
           expect(response.body[0].name).toBe('John Doe');
       });
   });
   ```

### Passo 4: Atualizar os Scripts do `package.json`

1. **Adicione um script para rodar os testes** no `package.json`:
   ```json
   "scripts": {
       "test": "jest"
   }
   ```

### Passo 5: Rodar os Testes

1. **Execute os testes**:
   ```bash
   npm test
   ```

Com essas etapas, você terá configurado a biblioteca `@shelf/jest-mongodb` para realizar testes unitários com MongoDB em memória. Isso permitirá que você teste suas rotas e interações com o banco de dados de forma eficiente e segura. Se precisar de mais alguma coisa, é só avisar! 😊