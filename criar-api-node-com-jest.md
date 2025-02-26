Ótima ideia! Adicionar testes unitários com Jest vai ajudar a garantir a qualidade e a estabilidade do nosso código. Vamos configurar o Jest no projeto.

### Passo 1: Instalar Dependências do Jest

1. **Instale as dependências necessárias**:
   ```bash
   npm install jest ts-jest @types/jest --save-dev
   ```

2. **Inicialize o Jest no projeto**:
   ```bash
   npx ts-jest config:init
   ```

### Passo 2: Configurar o Jest

1. **No arquivo `jest.config.js`** gerado, ajuste as configurações conforme necessário. Aqui está um exemplo básico:
   ```javascript
   module.exports = {
       preset: 'ts-jest',
       testEnvironment: 'node',
       testMatch: ['**/__tests__/**/*.ts', '**/?(*.)+(spec|test).ts']
   };
   ```

### Passo 3: Estrutura do Projeto

1. **Adicione uma pasta para os testes**:
   ```
   user-api/
   ├── node_modules/
   ├── src/
   │   ├── routes/
   │   │   ├── userRoutes.ts
   │   │   └── anotherResourceRoutes.ts
   │   ├── composites/
     └── server.ts
   ├── tests/
   │   ├── routes/
   │   │   ├── userRoutes.test.ts
   │   │   └── anotherResourceRoutes.test.ts
   │   ├── composites/
   │   │   └── routeComposite.test.ts
   ├── tsconfig.json
   ├── package.json
   ├── package-lock.json
   ├── .eslintrc.json
   ├── .husky/
   │   ├── pre-commit
   │   └── commit-msg
   ├── commitlinterrc.json
   └── lint-staged.config.js
   ```

### Passo 4: Escrever Testes Unitários

1. **No arquivo `tests/composites/routeComposite.test.ts`**, adicione o seguinte código:
   ```typescript
   import createCompositeRouter from '../../src/composites/routeComposite';
   import { Request, Response } from 'express';

   describe('Route Composite', () => {
       let router: ReturnType<typeof createCompositeRouter>;

       beforeEach(() => {
           router = createCompositeRouter();
       });

       it('should add and apply routes correctly', () => {
           const mockHandler = (req: Request, res: Response) => res.send('Hello');
           router.addRoute('/test', 'get', mockHandler);

           const appliedRouter = router.getRouter();
           const routeStack = appliedRouter.stack;

           expect(routeStack).toHaveLength(1);
           expect(routeStack[0].route.path).toBe('/test');
           expect(routeStack[0].route.methods.get).toBe(true);
       });
   });
   ```

2. **No arquivo `tests/routes/userRoutes.test.ts`**, adicione o seguinte código:
   ```typescript
   import request from 'supertest';
   import express from 'express';
   import userRouter from '../../src/routes/userRoutes';

   const app = express();
   app.use(express.json());
   app.use('/api', userRouter);

   describe('User Routes', () => {
       it('should create a new user', async () => {
           const response = await request(app)
               .post('/api/users')
               .send({ id: '1', name: 'John Doe', email: 'john@example.com' });

           expect(response.status).toBe(201);
           expect(response.text).toBe('Usuário criado com sucesso!');
       });

       it('should list users', async () => {
           await request(app)
               .post('/api/users')
               .send({ id: '1', name: 'John Doe', email: 'john@example.com' });

           const response = await request(app).get('/api/users');

           expect(response.status).toBe(200);
           expect(response.body).toHaveLength(1);
           expect(response.body[0].name).toBe('John Doe');
       });
   });
   ```

### Passo 5: Atualizar os Scripts do `package.json`

1. **Adicione um script para rodar os testes** no `package.json`:
   ```json
   "scripts": {
       "test": "jest"
   }
   ```

### Passo 6: Rodar os Testes

1. **Execute os testes**:
   ```bash
   npm test
   ```

Com essas etapas, você terá configurado o Jest para rodar testes unitários no seu projeto. Se precisar de mais alguma coisa, é só avisar! 😊