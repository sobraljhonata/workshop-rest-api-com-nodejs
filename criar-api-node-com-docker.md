Ótima ideia! Usar Docker e Docker Compose vai ajudar a criar uma estrutura escalável e fácil de gerenciar para sua aplicação. Vamos configurar isso.

### Passo 1: Criar o Dockerfile

1. **Crie um arquivo chamado `Dockerfile`** na raiz do projeto com o seguinte conteúdo:
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

   # Expor a porta que a aplicação vai rodar
   EXPOSE 3000

   # Comando para iniciar a aplicação
   CMD ["npm", "start"]
   ```

### Passo 2: Criar o docker-compose.yml

1. **Crie um arquivo chamado `docker-compose.yml`** na raiz do projeto com o seguinte conteúdo:
   ```yaml
   version: '3.8'

   services:
     app:
       build: .
       ports:
         - '3000:3000'
       environment:
         - PORT=3000
         - DB_PASSWORD=secreta
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

### Passo 3: Atualizar o Código para Usar Variáveis de Ambiente

1. **Certifique-se de que o código está usando as variáveis de ambiente corretamente**. Já fizemos isso anteriormente com o `dotenv`.

### Passo 4: Testar a Configuração

1. **Construa e inicie os contêineres**:
   ```bash
   docker-compose up --build
   ```

2. **Verifique se os contêineres estão rodando**:
   ```bash
   docker ps
   ```

3. **Acesse a aplicação em** `http://localhost:3000`.

Com essas etapas, você terá configurado Docker e Docker Compose para rodar sua aplicação Node.js e o serviço MongoDB em contêineres separados, proporcionando uma estrutura escalável e fácil de gerenciar. Se precisar de mais alguma coisa, é só avisar! 😊