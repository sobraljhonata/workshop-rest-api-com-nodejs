Sim, podemos implementar uma solução para verificar se a porta está ocupada e, se estiver, procurar a próxima porta disponível. Vou te mostrar como fazer isso e perguntar ao usuário se ele deseja usar a nova porta.

### Passo 1: Instalar a Biblioteca `prompt`

1. **Instale a biblioteca `prompt`** para interagir com o usuário no terminal:
   ```bash
   npm install prompt
   ```

### Passo 2: Atualizar o Código do Servidor

1. **Atualize o arquivo `server.js`** para incluir a lógica de verificação de porta e interação com o usuário:
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

   // Carregar dinamicamente todas as rotas na pasta 'routes'
   fs.readdirSync(path.join(__dirname, 'routes')).forEach(file => {
       const route = require(`./routes/${file}`);
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

### Passo 3: Testar a API

1. **Inicie o servidor**:
   ```bash
   node server.js
   ```

2. **Se a porta inicial estiver ocupada**, o servidor perguntará se você deseja usar a próxima porta disponível. Responda com "sim" ou "não" conforme desejado.

Com essa implementação, o servidor verificará se a porta está ocupada e, se estiver, perguntará ao usuário se deseja usar a próxima porta disponível. Isso deve ajudar a evitar conflitos de porta e tornar o processo mais interativo. Se precisar de mais alguma coisa, é só avisar! 😊