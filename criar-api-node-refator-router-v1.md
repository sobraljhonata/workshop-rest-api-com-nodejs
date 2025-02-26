Ótima ideia! Vamos organizar o código separando as rotas em um arquivo externo. Vou te mostrar como fazer isso.

### Passo 1: Estrutura do Projeto

1. **Crie a seguinte estrutura de pastas e arquivos**:
   ```
   user-api/
   ├── node_modules/
   ├── routes/
   │   └── userRoutes.js
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

### Passo 3: Atualização do Servidor

1. **No arquivo `server.js`**, atualize o código para usar as rotas externas:
   ```javascript
   const express = require('express');
   const bodyParser = require('body-parser');
   const cors = require('cors');
   const userRoutes = require('./routes/userRoutes');

   const app = express();
   const port = 3000;

   app.use(bodyParser.json());
   app.use(cors());

   // Usar as rotas de usuário
   app.use('/api', userRoutes);

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

Agora, seu código está mais organizado e fácil de manter. Se precisar de mais alguma coisa, é só avisar! 😊