Entendi! Vamos criar um `RouterAdapter` para simplificar a implementação das rotas. Vou voltar ao código anterior e aplicar o `RouterAdapter`.

### Passo 1: Estrutura do Projeto

1. **Crie a seguinte estrutura de pastas e arquivos**:
   ```
   user-api/
   ├── node_modules/
   ├── routes/
   │   ├── userRoutes.js
   │   └── anotherResourceRoutes.js
   ├── adapters/
   │   └── routerAdapter.js
   ├── server.js
   ├── package.json
   └── package-lock.json
   ```

### Passo 2: Criação do RouterAdapter

1. **No arquivo `adapters/routerAdapter.js`**, adicione o seguinte código:
   ```javascript
   const express = require('express');

   class RouterAdapter {
       constructor() {
           this.router = express.Router();
       }

       createRoute(path, method, handler) {
           this.routermethod;
       }

       getRouter() {
           return this.router;
       }
   }

   module.exports = RouterAdapter;
   ```

### Passo 3: Atualização das Rotas

1. **No arquivo `routes/userRoutes.js`**, atualize o código para usar o `RouterAdapter`:
   ```javascript
   const RouterAdapter = require('../adapters/routerAdapter');
   const userAdapter = new RouterAdapter();

   let users = [];

   // Endpoint para criação de usuário
   userAdapter.createRoute('/users', 'post', (req, res) => {
       const user = req.body;
       users.push(user);
       res.status(201).send('Usuário criado com sucesso!');
   });

   // Endpoint para listagem de usuários
   userAdapter.createRoute('/users', 'get', (req, res) => {
       res.json(users);
   });

   // Endpoint para atualização de usuário
   userAdapter.createRoute('/users/:id', 'put', (req, res) => {
       const id = req.params.id;
       const updatedUser = req.body;
       users = users.map(user => user.id === id ? updatedUser : user);
       res.send('Usuário atualizado com sucesso!');
   });

   // Endpoint para deletar usuário
   userAdapter.createRoute('/users/:id', 'delete', (req, res) => {
       const id = req.params.id;
       users = users.filter(user => user.id !== id);
       res.send('Usuário deletado com sucesso!');
   });

   module.exports = userAdapter.getRouter();
   ```

2. **No arquivo `routes/anotherResourceRoutes.js`**, adicione o código para usar o `RouterAdapter` (exemplo para outro recurso):
   ```javascript
   const RouterAdapter = require('../adapters/routerAdapter');
   const resourceAdapter = new RouterAdapter();

   let resources = [];

   // Endpoint para criação de recurso
   resourceAdapter.createRoute('/resources', 'post', (req, res) => {
       const resource = req.body;
       resources.push(resource);
       res.status(201).send('Recurso criado com sucesso!');
   });

   // Endpoint para listagem de recursos
   resourceAdapter.createRoute('/resources', 'get', (req, res) => {
       res.json(resources);
   });

   // Endpoint para atualização de recurso
   resourceAdapter.createRoute('/resources/:id', 'put', (req, res) => {
       const id = req.params.id;
       const updatedResource = req.body;
       resources = resources.map(resource => resource.id === id ? updatedResource : resource);
       res.send('Recurso atualizado com sucesso!');
   });

   // Endpoint para deletar recurso
   resourceAdapter.createRoute('/resources/:id', 'delete', (req, res) => {
       const id = req.params.id;
       resources = resources.filter(resource => resource.id !== id);
       res.send('Recurso deletado com sucesso!');
   });

   module.exports = resourceAdapter.getRouter();
   ```

### Passo 4: Atualização do Servidor

1. **No arquivo `server.js`**, mantenha o código como está, pois ele já carrega dinamicamente todas as rotas.

### Passo 5: Testar a API

1. **Inicie o servidor**:
   ```bash
   node server.js
   ```

2. **Use uma ferramenta como Postman ou Insomnia** para testar os endpoints:
   - **POST /api/users**: Cria um novo usuário.
   - **GET /api/users**: Lista todos os usuários.
   - **PUT /api/users/:id**: Atualiza um usuário existente.
   - **DELETE /api/users/:id**: Deleta um usuário.
   - **POST /api/resources**: Cria um novo recurso.
   - **GET /api/resources**: Lista todos os recursos.
   - **PUT /api/resources/:id**: Atualiza um recurso existente.
   - **DELETE /api/resources/:id**: Deleta um recurso.

Com o `RouterAdapter`, a implementação das rotas fica mais simplificada e modular. Se precisar de mais alguma coisa, é só avisar! 😊