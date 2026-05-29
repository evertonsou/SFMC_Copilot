# Module: eslint-config-ssjs

> **Skill**: sfmc-copilot | **Categoria**: Community Solutions | **Tokens**: ~1K  
> **Repositório**: [eslint-config-ssjs](https://github.com/JoernBerkefeld/eslint-config-ssjs) ⭐ 22

---

## Visão Geral

**eslint-config-ssjs** é uma configuração de **ESLint customizada para SSJS** (Server-Side JavaScript do Salesforce Marketing Cloud). Permite usar linting estático no seu editor de código, capturando erros e seguindo boas práticas antes de publicar no SFMC.

---

## Por que usar ESLint com SSJS?

```
Sem ESLint:
❌ Erros de sintaxe descobertos apenas em produção no SFMC
❌ Variáveis não declaradas causam bugs silenciosos
❌ Código inconsistente entre desenvolvedores
❌ Sem warnings para funções depreciadas

Com ESLint + eslint-config-ssjs:
✅ Erros de sintaxe detectados no editor (VS Code)
✅ Globals do SFMC reconhecidos (Platform, Variable, etc.)
✅ Padrões de código consistentes no time
✅ Integração com CI/CD para bloquear código com erros
```

---

## Instalação

### Pré-requisitos

```bash
# Node.js >= 14 e npm
node --version
npm --version

# VS Code com extensão ESLint
# Instalar: ESLint (Dirk Baeumer) no VS Code Extensions
```

### Instalar Pacotes

```bash
# Inicializar projeto (se não tiver package.json)
npm init -y

# Instalar ESLint e config SSJS
npm install --save-dev eslint eslint-config-ssjs

# Instalar dependências da config
npm install --save-dev eslint-plugin-unicorn
```

---

## Configuração

### Arquivo `.eslintrc.json`

```json
{
  "extends": "ssjs",
  "env": {
    "browser": false,
    "node": false
  },
  "globals": {
    /* Globais padrão do SFMC SSJS */
    "Platform": "readonly",
    "Variable": "readonly",
    "Attribute": "readonly",
    "Script": "readonly",
    "DataExtension": "readonly",
    "Email": "readonly",
    "TriggeredSend": "readonly",
    "ContentBlockByID": "readonly",
    "ContentBlockByKey": "readonly",
    "Write": "writable",
    "Stringify": "readonly",
    "GUID": "readonly"
  },
  "rules": {
    /* Customizações opcionais */
    "no-console": "warn",
    "no-unused-vars": "warn",
    "prefer-const": "error",
    "no-var": "error",
    "eqeqeq": ["error", "always"],
    "semi": ["error", "always"],
    "quotes": ["error", "double"],
    "indent": ["error", 2]
  }
}
```

### Arquivo `.eslintignore`

```
node_modules/
dist/
*.min.js
```

---

## Uso no VS Code

### Configuração do Workspace (`.vscode/settings.json`)

```json
{
  "eslint.enable": true,
  "eslint.validate": [
    "javascript",
    "html"
  ],
  "editor.formatOnSave": true,
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": true
  },
  "[javascript]": {
    "editor.defaultFormatter": "dbaeumer.vscode-eslint"
  }
}
```

### O que aparece no editor

```javascript
// ❌ ESLint vai alertar:
var x = 1              // use const/let
if (a == b)            // use ===
let unused = "string"  // variável não usada

// ✅ ESLint aceita:
const x = 1;
if (a === b) { }
const nome = Platform.Request.GetFormField("nome");
```

---

## Comandos de Linting

```bash
# Verificar todos os arquivos .js
npx eslint src/**/*.js

# Auto-corrigir erros simples (formatação, ponto-e-vírgula, aspas)
npx eslint src/**/*.js --fix

# Verificar arquivo específico
npx eslint meu-script.js

# Gerar relatório em HTML
npx eslint src/**/*.js --format html -o relatorio-lint.html
```

---

## Integração com CI/CD (GitHub Actions)

```yaml
# .github/workflows/lint.yml
name: SSJS Lint Check

on: [push, pull_request]

jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'
      
      - name: Install dependencies
        run: npm install
      
      - name: Run ESLint
        run: npx eslint src/**/*.js
```

---

## Regras Específicas para SSJS

### Padrões Recomendados

```javascript
// ✅ Declarar variáveis com const/let
const api = new Script.Util.WSProxy();
let result;

// ✅ Comparação estrita
if (status === "OK") { }

// ✅ Try/catch para operações críticas
try {
  const data = Platform.Function.ParseJSON(response);
} catch(e) {
  // Logar erro
}

// ✅ Semicolons em todas as linhas
const nome = Platform.Request.GetFormField("nome");
Variable.SetValue("@nome", nome);

// ✅ Funções com documentação
/**
 * Busca dados de subscriber na DE
 * @param {string} email - Email do subscriber
 * @returns {Object|null} - Dados do subscriber ou null
 */
function getSubscriberData(email) {
  const de = DataExtension.Init("DE_Subscribers");
  const rows = de.Rows.Retrieve({
    Property: "Email",
    SimpleOperator: "equals",
    Value: email
  });
  return rows.length > 0 ? rows[0] : null;
}
```

---

## Estrutura de Projeto SSJS com ESLint

```
projeto-sfmc/
├── .eslintrc.json        ← Configuração ESLint
├── .eslintignore         ← Arquivos ignorados
├── .vscode/
│   └── settings.json     ← Config do VS Code
├── package.json
├── node_modules/         ← .gitignore
└── src/
    ├── scripts/
    │   ├── form-handler.js
    │   ├── unsubscribe.js
    │   └── preference-center.js
    └── lib/
        └── utils.js
```

---

## Boas Práticas

```
✅ Configure ESLint no início do projeto, não depois
✅ Compartilhe .eslintrc.json no repositório (padronização de time)
✅ Configure auto-fix no save (VS Code settings)
✅ Inclua lint check no CI/CD para bloquear PRs com erros
✅ Use "warn" para regras estilísticas, "error" para bugs reais
✅ Documente exceções com // eslint-disable-line comentado
✅ Revise as regras trimestralmente com o time
```

---

## Links

- **GitHub**: https://github.com/JoernBerkefeld/eslint-config-ssjs
- **npm**: https://www.npmjs.com/package/eslint-config-ssjs
- **ESLint Docs**: https://eslint.org/docs/latest/
- **VS Code ESLint Extension**: https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint
