Sim, é possível configurar o `git-commit-msg-linter` em um arquivo próprio. Vamos criar um arquivo de configuração separado para isso.

### Passo 1: Criar o Arquivo de Configuração

1. **Crie um arquivo chamado `commitlinterrc.json`** na raiz do projeto com a seguinte configuração:
   ```json
   {
       "pattern": "^(feat|fix|docs|style|refactor|perf|test|chore|revert)(\\(.+\\))?: .{1,50}",
       "errorMsg": "Sua mensagem de commit deve seguir o padrão: tipo(scope): descrição"
   }
   ```

### Passo 2: Atualizar o Husky para Usar o Arquivo de Configuração

1. **Certifique-se de que o hook `commit-msg` do Husky está configurado** para usar o `git-commit-msg-linter`:
   ```bash
   npx husky add .husky/commit-msg 'npx git-commit-msg-linter'
   ```

### Passo 3: Remover a Configuração do `package.json`

1. **Remova a configuração do `git-commit-msg-linter` do `package.json`** se ela estiver presente:
   ```json
   "config": {
       "commit-msg-linter": {
           // Remova esta seção
       }
   }
   ```

### Passo 4: Testar a Configuração

1. **Faça uma alteração no código e tente fazer um commit** para verificar se o linter está funcionando corretamente:
   ```bash
   git add .
   git commit -m "feat: adicionar configuração do ESLint, Husky, Lint-Staged e Git Commit Msg Linter"
   ```

Com essas etapas, você terá configurado o `git-commit-msg-linter` para usar um arquivo de configuração separado, mantendo o `package.json` mais limpo e organizado. Se precisar de mais alguma coisa, é só avisar! 😊