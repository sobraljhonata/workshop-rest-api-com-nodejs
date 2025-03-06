Claro! Vamos adicionar a biblioteca `cors` para permitir que a API receba requisições de fontes externas.

### Passo 1: Instalar a Biblioteca `cors`

1. **Instale a biblioteca `cors`**:
   ```bash
   npm install cors
   ```

### Passo 2: Atualizar o Código do Servidor

1. **Atualize o arquivo `server.js`** para incluir e usar o `cors`:
   ```javascript
   const express = require('express');
   const bodyParser = require('body-parser');
   const cors = require('cors');

   const app = express();
   const port = 3000;

   app.use(bodyParser.json());
   app.use(cors());

   let users = [];

   // Endpoint para criação de usuário
   app.post('/users', (req, res) => {
       const user = req.body;
       users.push(user);
       res.status(201).send('Usuário criado com sucesso!');
   });

   // Endpoint para listagem de usuários
   app.get('/users', (req, res) => {
       res.json(users);
   });

   // Endpoint para atualização de usuário
   app.put('/users/:id', (req, res) => {
       const id = req.params.id;
       const updatedUser = req.body;
       users = users.map(user => user.id === id ? updatedUser : user);
       res.send('Usuário atualizado com sucesso!');
   });

   // Endpoint para deletar usuário
   app.delete('/users/:id', (req, res) => {
       const id = req.params.id;
       users = users.filter(user => user.id !== id);
       res.send('Usuário deletado com sucesso!');
   });

   app.listen(port, () => {
       console.log(`Servidor rodando na porta ${port}`);
   });
   ```

### Passo 3: Testar a API com `cors`

1. **Inicie o servidor**:
   ```bash
   node server.js
   ```

2. **Use uma ferramenta como Postman ou Insomnia** para testar os endpoints, agora com suporte a CORS:
   - **POST /users**: Cria um novo usuário.
   - **GET /users**: Lista todos os usuários.
   - **PUT /users/:id**: Atualiza um usuário existente.
   - **DELETE /users/:id**: Deleta um usuário.

Com isso, sua API estará pronta para receber requisições de fontes externas. Se precisar de mais alguma coisa, é só avisar! 😊