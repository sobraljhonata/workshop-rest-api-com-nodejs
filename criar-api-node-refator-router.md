Sim, é possível fazer isso! Podemos configurar o servidor para carregar dinamicamente todos os arquivos de rotas na pasta `routes`. Vou te mostrar como fazer isso.

### Passo 1: Estrutura do Projeto

1. **Crie a seguinte estrutura de pastas e arquivos**:
   ```
   user-api/
   ├── node_modules/
   ├── routes/
   │   ├── userRoutes.js
   │   └── anotherResourceRoutes.js
   ├── server.js
   ├── package.json
   └── package-lock.json
   ```

### Passo 2: Configuração das Rotas

1. **No arquivo `routes/userRoutes.js`**, adicione o seguinte código:
   ```javascript
   const express = require('express');
   const router = express.Router();

   let users = [];

   // Endpoint para criação de usuário
   router.post('/users', (req, res) => {
       const user = req.body;
       users.push(user);
       res.status(201).send('Usuário criado com sucesso!');
   });

   // Endpoint para listagem de usuários
   router.get('/users', (req, res) => {
       res.json(users);
   });

   // Endpoint para atualização de usuário
   router.put('/users/:id', (req, res) => {
       const id = req.params.id;
       const updatedUser = req.body;
       users = users.map(user => user.id === id ? updatedUser : user);
       res.send('Usuário atualizado com sucesso!');
   });

   // Endpoint para deletar usuário
   router.delete('/users/:id', (req, res) => {
       const id = req.params.id;
       users = users.filter(user => user.id !== id);
       res.send('Usuário deletado com sucesso!');
   });

   module.exports = router;
   ```

2. **No arquivo `routes/anotherResourceRoutes.js`**, adicione o seguinte código (exemplo para outro recurso):
   ```javascript
   const express = require('express');
   const router = express.Router();

   let resources = [];

   // Endpoint para criação de recurso
   router.post('/resources', (req, res) => {
       const resource = req.body;
       resources.push(resource);
       res.status(201).send('Recurso criado com sucesso!');
   });

   // Endpoint para listagem de recursos
   router.get('/resources', (req, res) => {
       res.json(resources);
   });

   // Endpoint para atualização de recurso
   router.put('/resources/:id', (req, res) => {
       const id = req.params.id;
       const updatedResource = req.body;
       resources = resources.map(resource => resource.id === id ? updatedResource : resource);
       res.send('Recurso atualizado com sucesso!');
   });

   // Endpoint para deletar recurso
   router.delete('/resources/:id', (req, res) => {
       const id = req.params.id;
       resources = resources.filter(resource => resource.id !== id);
       res.send('Recurso deletado com sucesso!');
   });

   module.exports = router;
   ```

### Passo 3: Atualização do Servidor

1. **No arquivo `server.js`**, atualize o código para carregar dinamicamente todas as rotas:
   ```javascript
   const express = require('express');
   const bodyParser = require('body-parser');
   const cors = require('cors');
   const fs = require('fs');
   const path = require('path');

   const app = express();
   const port = 3000;

   app.use(bodyParser.json());
   app.use(cors());

   // Carregar dinamicamente todas as rotas na pasta 'routes'
   fs.readdirSync(path.join(__dirname, 'routes')).forEach(file => {
       const route = require(`./routes/${file}`);
       app.use('/api', route);
   });

   app.listen(port, () => {
       console.log(`Servidor rodando na porta ${port}`);
   });
   ```

### Passo 4: Testar a API

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

Agora, você pode adicionar quantos arquivos de rotas quiser na pasta `routes`, e eles serão carregados automaticamente quando o servidor iniciar. Se precisar de mais alguma coisa, é só avisar! 😊