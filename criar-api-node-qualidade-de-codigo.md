Ótima ideia! Adicionar essas ferramentas vai ajudar a manter a qualidade e a consistência do código. Vamos configurar cada uma delas passo a passo.

### Passo 1: Instalar Dependências

1. **Instale as dependências necessárias**:
   ```bash
   npm install husky lint-staged eslint @typescript-eslint/parser @typescript-eslint/eslint-plugin git-commit-msg-linter --save-dev
   ```

### Passo 2: Configurar o ESLint

1. **Crie um arquivo `.eslintrc.json`** na raiz do projeto com a seguinte configuração:
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

2. **Adicione um script para rodar o ESLint** no `package.json`:
   ```json
   "scripts": {
       "lint": "eslint 'src/**/*.{ts,tsx}'"
   }
   ```

### Passo 3: Configurar o Husky

1. **Inicialize o Husky**:
   ```bash
   npx husky install
   ```

2. **Crie um hook de pre-commit**:
   ```bash
   npx husky add .husky/pre-commit "npx lint-staged"
   ```

3. **Crie um hook de commit-msg**:
   ```bash
   npx husky add .husky/commit-msg 'npx git-commit-msg-linter'
   ```

### Passo 4: Configurar o Lint-Staged

1. **Adicione a configuração do `lint-staged`** no `package.json`:
   ```json
   "lint-staged": {
       "src/**/*.{ts,tsx}": [
           "eslint --fix",
           "git add"
       ]
   }
   ```

### Passo 5: Configurar o Git Commit Msg Linter

1. **Adicione a configuração do `git-commit-msg-linter`** no `package.json`:
   ```json
   "config": {
       "commit-msg-linter": {
           "pattern": "^(feat|fix|docs|style|refactor|perf|test|chore|revert)(\\(.+\\))?: .{1,50}",
           "errorMsg": "Sua mensagem de commit deve seguir o padrão: tipo(scope): descrição"
       }
   }
   ```

### Passo 6: Testar a Configuração

1. **Faça uma alteração no código e tente fazer um commit** para verificar se os hooks estão funcionando corretamente:
   ```bash
   git add .
   git commit -m "feat: adicionar configuração do ESLint, Husky, Lint-Staged e Git Commit Msg Linter"
   ```

Com essas configurações, você terá um ambiente de desenvolvimento mais robusto, garantindo a qualidade e a consistência do código. Se precisar de mais alguma coisa, é só avisar! 😊