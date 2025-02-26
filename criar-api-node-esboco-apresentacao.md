Claro! Vamos criar os slides para o seu workshop e, em seguida, gerar um arquivo zipado com todo o projeto.

### Passo 1: Criar os Slides

Vou criar um esboço dos slides para você. Você pode usar uma ferramenta como PowerPoint, Google Slides ou qualquer outra de sua preferência para criar os slides finais.

#### Slide 1: Título
- **Título:** Workshop: Desenvolvimento de API com Node.js e Express
- **Subtítulo:** Passo a Passo para Criar uma API Completa
- **Nome do Apresentador:** [Seu Nome]

#### Slide 2: Introdução
- **Objetivo:** Aprender a criar uma API RESTful usando Node.js, Express e TypeScript
- **Agenda:**
  - Configuração do Projeto
  - Criação do Servidor Express
  - Implementação de Endpoints
  - Adição de Middlewares
  - Organização do Código com Padrões de Projeto
  - Integração com TypeScript
  - Garantia de Qualidade com Ferramentas de Linting e Hooks

#### Slide 3: Configuração do Projeto
- **Passos:**
  - Criação da pasta do projeto
  - Inicialização do projeto Node.js
  - Instalação das dependências

#### Slide 4: Criação do Servidor Express
- **Código:**
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
      const route = require(`./routes/${file}`).default;
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

#### Slide 5: Implementação de Endpoints
- **Código:**
  ```javascript
  const createCompositeRouter = require('../composites/routeComposite');
  const userRouter = createCompositeRouter();

  let users = [];

  // Endpoint para criação de usuário
  userRouter.addRoute('/users', 'post', (req, res) => {
      const user = req.body;
      users.push(user);
      res.status(201).send('Usuário criado com sucesso!');
  });

  // Endpoint para listagem de usuários
  userRouter.addRoute('/users', 'get', (req, res) => {
      res.json(users);
  });

  // Endpoint para atualização de usuário
  userRouter.addRoute('/users/:id', 'put', (req, res) => {
      const id = req.params.id;
      const updatedUser = req.body;
      users = users.map(user => user.id === id ? updatedUser : user);
      res.send('Usuário atualizado com sucesso!');
  });

  // Endpoint para deletar usuário
  userRouter.addRoute('/users/:id', 'delete', (req, res) => {
      const id = req.params.id;
      users = users.filter(user => user.id !== id);
      res.send('Usuário deletado com sucesso!');
  });

  module.exports = userRouter.getRouter();
  ```

#### Slide 6: Adição de Middlewares
- **Middleware de Logging:**
  ```javascript
  const logger = (req, res, next) => {
      console.log(`${req.method} ${req.url}`);
      next();
  };
  app.use(logger);
  ```
- **Middleware de Autenticação:**
  ```javascript
  const authenticate = (req, res, next) => {
      const token = req.headers['authorization'];
      if (token === 'seu-token-secreto') {
          next();
      } else {
          res.status(403).send('Acesso negado');
      }
  };
  app.use('/api', authenticate);
  ```

#### Slide 7: Organização do Código com Padrões de Projeto
- **Padrão Composite:**
  ```javascript
  const createCompositeRouter = () => {
      const router = Router();
      const routes = [];

      const addRoute = (path, method, handler) => {
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

  module.exports = createCompositeRouter;
  ```

#### Slide 8: Integração com TypeScript
- **Configuração do TypeScript:**
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

#### Slide 9: Garantia de Qualidade com Ferramentas de Linting e Hooks
- **Configuração do ESLint:**
  ```json
  {
      "parser": "@typescript-eslint/parser",
      "extends": [
          "eslint:recommended",
          "plugin:@typescript-eslint/recommended"
      ],
      "parserOptions": {
          "ecmaVersion": 2020,
          "sourceType": "module"
      },
      "rules": {
          "semi": ["error", "always"],
          "quotes": ["error", "single"]
      }
  }
  ```
- **Configuração do Husky e Lint-Staged:**
  ```json
  "lint-staged": {
      "src/**/*.{ts,tsx}": [
          "eslint --fix",
          "git add"
      ]
  }
  ```

#### Slide 10: Conclusão
- **Resumo:** Revisão dos passos seguidos para criar a API
- **Perguntas:** Espaço para perguntas e respostas

### Passo 2: Gerar Arquivo Zipado do Projeto

Vou gerar um arquivo zipado com todo o projeto que criamos. 

**Baixar Projeto Zipado**

Espero que isso ajude a preparar seu workshop! Se precisar de mais alguma coisa, é só avisar. Boa sorte na apresentação! 🚀