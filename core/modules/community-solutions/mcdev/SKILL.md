# Module: Accenture mcdev (DevTools)

> **Skill**: sfmc-copilot | **Categoria**: Community Solutions | **Tokens**: ~1.5K  
> **Repositório**: [Accenture/sfmc-devtools](https://github.com/Accenture/sfmc-devtools) ⭐ 155

---

## Visão Geral

**mcdev** (Marketing Cloud DevTools) é uma ferramenta CLI open-source da Accenture para **deployment, backup e CI/CD** de assets do Salesforce Marketing Cloud. Permite versionar, migrar e automatizar a gestão de assets SFMC como código.

---

## O que o mcdev Gerencia

| Asset Type | Descrição |
|-----------|-----------|
| `dataExtension` | Data Extensions e schemas |
| `emailTemplate` | Templates de email |
| `cloudPage` | CloudPages e Code Resources |
| `journey` | Journey Builder journeys |
| `automationSudio` | Automation Studio workflows |
| `query` | SQL Query Activities |
| `script` | SSJS scripts |
| `ampscript` | AMPscript files |
| `contentArea` | Content Areas |
| `asset` | Content Builder assets |
| `dataExtensionField` | Campos de DE |
| `triggeredSend` | Triggered Send definitions |

---

## Instalação

```bash
# Requisitos: Node.js >= 14
npm install -g mcdev

# Verificar instalação
mcdev --version
```

---

## Configuração Inicial

### Inicializar Projeto

```bash
# Criar estrutura de projeto
mcdev init

# Estrutura criada:
# ├── .mcdevrc.json          (configuração)
# ├── credentials.json       (credenciais — NÃO versionar)
# └── deploy/                (assets gerenciados)
```

### Arquivo `.mcdevrc.json`

```json
{
  "version": "4.0.0",
  "api_version": "50.0",
  "defaultBU": "Production",
  "gitEnabled": true,
  "metaDataTypes": {
    "retrieve": [
      "dataExtension",
      "emailTemplate",
      "cloudPage",
      "query",
      "script",
      "automationStudio",
      "journey"
    ]
  },
  "directories": {
    "retrieve": "retrieve",
    "deploy": "deploy",
    "deltaPackage": "delta"
  }
}
```

### Arquivo `credentials.json` (não versionar!)

```json
{
  "Production": {
    "client_id": "SEU_CLIENT_ID",
    "client_secret": "SEU_CLIENT_SECRET",
    "auth_url": "https://YOUR_SUBDOMAIN.auth.marketingcloudapis.com/",
    "account_id": "123456789",
    "businessUnits": {
      "Production": "123456789",
      "BU_Marketing": "987654321"
    }
  }
}
```

---

## Comandos Principais

### Retrieve (Baixar Assets do SFMC)

```bash
# Baixar tudo de uma BU
mcdev retrieve Production/*

# Baixar tipo específico
mcdev retrieve Production/query

# Baixar asset específico
mcdev retrieve Production/query/MeuQuery_SQL

# Baixar de múltiplas BUs
mcdev retrieve Production/* Staging/*
```

### Deploy (Enviar Assets para o SFMC)

```bash
# Deploy de todos os assets na pasta deploy/
mcdev deploy Production/*

# Deploy de tipo específico
mcdev deploy Production/emailTemplate

# Deploy de asset específico
mcdev deploy Production/emailTemplate/Newsletter_Junho

# Deploy com delta (apenas mudanças)
mcdev deployDelta Production/*
```

### Utilitários

```bash
# Listar Business Units configuradas
mcdev listBUs

# Verificar diferenças entre retrieve e deploy
mcdev diff Production/*

# Criar pacote de delta para CI/CD
mcdev createDeltaPkg HEAD~1 --filter "Production/*"
```

---

## Estrutura de Diretórios

```
projeto-sfmc/
├── .mcdevrc.json
├── credentials.json          ← .gitignore
├── retrieve/
│   └── Production/
│       ├── dataExtension/
│       │   ├── DE_Clientes.dataExtension.json
│       │   └── DE_Compras.dataExtension.json
│       ├── query/
│       │   └── Query_Engajamento.query.sql
│       ├── emailTemplate/
│       │   └── Newsletter.html
│       └── journey/
│           └── Onboarding.journey.json
└── deploy/
    └── Production/
        └── (assets prontos para deploy)
```

---

## CI/CD com GitHub Actions

```yaml
# .github/workflows/sfmc-deploy.yml
name: SFMC Deploy

on:
  push:
    branches: [main]
    paths: ['deploy/**']

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'
      
      - name: Install mcdev
        run: npm install -g mcdev
      
      - name: Configure credentials
        run: |
          echo '${{ secrets.MCDEV_CREDENTIALS }}' > credentials.json
      
      - name: Deploy to SFMC Production
        run: mcdev deploy Production/*
      
      - name: Cleanup
        run: rm credentials.json
```

---

## Workflow de Desenvolvimento Recomendado

```
1. RETRIEVE: Baixar estado atual do SFMC
   mcdev retrieve Production/*
   
2. BRANCH: Criar branch de feature no Git
   git checkout -b feature/nova-query-engajamento
   
3. EDITAR: Modificar arquivos localmente
   (editar query SQL, template, etc.)
   
4. DIFF: Verificar diferenças
   mcdev diff Production/query
   
5. COMMIT: Versionar mudanças
   git add . && git commit -m "feat: query engajamento v2"
   
6. PR + REVIEW: Code review no GitHub
   
7. DEPLOY: Após merge, CI/CD faz deploy automático
   mcdev deploy Production/query/Query_Engajamento
```

---

## Boas Práticas

```
✅ NUNCA versione credentials.json (.gitignore obrigatório)
✅ Sempre faça retrieve antes de deploy (estado atualizado)
✅ Use branches para cada mudança significativa
✅ Configure CI/CD para deploy automático em main
✅ Mantenha ambientes: Development → Staging → Production
✅ Documente mudanças nos commit messages (convenção: feat/fix/chore)
✅ Use deltaPackage para deploys grandes (apenas o que mudou)
✅ Faça backup via retrieve antes de mudanças críticas
```

---

## Links e Recursos

- **GitHub**: https://github.com/Accenture/sfmc-devtools
- **Documentação**: https://accenture.github.io/sfmc-devtools/
- **npm**: https://www.npmjs.com/package/mcdev
- **Issues**: https://github.com/Accenture/sfmc-devtools/issues
