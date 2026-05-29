# SFMC Copilot Skill - Controle de Implementação

> **Status**: Implementação Concluída ✅ | **Data**: 2026-05-25
> **Versão**: 2.0 - IMPLEMENTAÇÃO V2.0 CONCLUÍDA | Novos módulos adicionados: 12 + 9 Community Solutions

---

## 📋 Visão Geral do Projeto

**Objetivo**: Criar uma skill especializada em Salesforce Marketing Cloud com arquitetura otimizada para consumo de tokens, conhecimento modular e design system intercambiável.

---

## 🎯 Arquitetura da Skill

### Princípios de Design
| Princípio | Implementação |
|-----------|---------------|
| Otimização de Tokens | SKILL.md raiz (~150 tokens) + módulos carregados sob demanda |
| Modularidade | 12+ módulos independentes no core |
| Design System | Pasta intercambiável via configuração |
| Profundidade | Todas as funções, DataViews e componentes |

---

## 📁 Estrutura de Diretórios

```
sfmc-skill/
├── SKILL.md                           # Arquivo principal (carregamento progressivo)
├── README.md                          # Documentação
├── IMPLEMENTATION-CONTROL.md          # Este arquivo
│
├── core/
│   └── modules/
│       ├── ampscript/
│       ├── sql/
│       ├── dataviews/
│       ├── html-email/
│       ├── ssjs/
│       ├── cloudpages/
│       ├── api-integration/
│       │   ├── rest/                   # REST API (connection, endpoints, auth)
│       │   ├── soap/                   # SOAP API patterns
│       │   ├── wsproxy/                # WSProxy advanced usage
│       │   ├── authentication/          # OAuth, JWT, Server-to-Server
│       │   ├── endpoints/              # All API endpoints reference
│       │   └── advanced/               # Rate limiting, bulk, streaming
│       ├── journey-builder/
│       ├── automation-studio/
│       ├── content-builder/
│       ├── data-extension/
│       └── community-solutions/
│
├── design-system/
│   ├── _default/
│   ├── config.json
│   └── [cliente-specific_folders]
│
└── config/
    └── default-design-system.json
```

---

## ✅ Checklist de Implementação

### Fase 1: Estrutura Base
- [x] Criar diretório `sfmc-skill/`
- [x] Criar `SKILL.md` raiz com carregamento progressivo
- [x] Criar estrutura de diretórios `core/modules/`
- [x] Criar estrutura de diretórios `design-system/`
- [x] Criar `config/default-design-system.json`

### Fase 2: Design System
- [x] Implementar `_default/` design system base
  - [x] `brand-colors.json` (paletas Verde/Roxo conforme Cemig SIM)
  - [x] `typography.json` (escala tipográfica)
  - [x] `spacing.json` (sistema 8pt grid)
  - [x] `components/` (buttons, cards, hero, footer, preheader, header)
  - [x] `email-templates/` (newsletter, promotional, transactional, onboarding)
  - [x] `guidelines/` (diretrizes de uso)
- [x] Documentar padrão de alternância de design system

### Fase 3: Módulos Core

#### 3.1 AMPscript (~4-5K tokens)
- [ ] `SKILL.md` principal
- [ ] `categories/data-extension/` - Lookup, InsertData, UpdateData, DeleteData, UpsertData, DEExtract, etc.
- [ ] `categories/string/` - Concat, Replace, Substring, IndexOf, etc.
- [ ] `categories/datetime/` - Now, DateAdd, DateDiff, Format, etc.
- [ ] `categories/formatting/` - FormatCurrency, FormatNumber, FormatDate, etc.
- [ ] `categories/personalization/` - TreatAs, TreatAsContent, etc.
- [ ] `categories/logic/` - IIF, IF/THEN/ELSE, FOR
- [ ] `categories/http/` - HTTPGet, HTTPPost, HTTPGetHeader
- [ ] `categories/encryption/` - Encrypt, Decrypt, EncryptSymmetric
- [ ] `examples/` - Code snippets

#### 3.2 SQL (~2.5K tokens)
- [ ] `SKILL.md` principal
- [ ] Syntax reference
- [ ] `patterns/` - JOIN patterns, aggregation, subqueries
- [ ] Common patterns - de-duplication, segmentation, data export

#### 3.3 DataViews (~3K tokens)
- [ ] `SKILL.md` principal
- [ ] `categories/email/` - _Sent, _Open, _Click, _Bounce, _Unsubs, _Survey, _Forward
- [ ] `categories/sms/` - _SMSMessageTracking, _SMSSubscriptionLog, _SMSJob, _SMSLightItinerary
- [ ] `categories/push/` - _PushAddress, _PushTag, _PushTrackLink, _PushAbuseReport
- [ ] `categories/subscribers/` - _Subscribers, _ListSubscribers, _Status, _BusinessUnit
- [ ] `categories/journeys/` - _Journey, _JourneyActivity, _JourneyExecution, _JourneyHistory
- [ ] `categories/automation/` - _Automation, _AutomationInstance, _Activity
- [ ] `categories/content/` - _Content, _ContentArea, _Asset, _Folder
- [ ] `categories/enterprise/` - ENT._Subscribers, ENT._EnterpriseAttribute

#### 3.4 HTML Email (~5K tokens)
- [ ] `SKILL.md` principal
- [ ] Table-based layouts guide
- [ ] Inline CSS best practices
- [ ] MSO conditionals for Outlook
- [ ] VML bulletproof buttons
- [ ] First mobile responsive design
- [ ] Media queries patterns
- [ ] `email-clients/outlook-classic/` - Outlook Windows fixes
- [ ] `email-clients/outlook-365/` - Outlook 365 fixes
- [ ] `email-clients/gmail/` - Gmail fixes
- [ ] `email-clients/apple-mail/` - Apple Mail fixes
- [ ] `email-clients/dark-mode/` - Dark mode support
- [ ] AMP for Email guide
- [ ] `templates/` - Starter templates

#### 3.5 SSJS (~3K tokens)
- [ ] `SKILL.md` principal
- [ ] Platform library reference
- [ ] Core library reference
- [ ] Error handling patterns
- [ ] API integration patterns
- [ ] `examples/` - Form processing, data manipulation

#### 3.6 CloudPages (~4K tokens)
- [ ] `SKILL.md` principal
- [ ] CloudPages architecture
- [ ] `forms/` - Form handling patterns (POST/GET)
- [ ] `data-capture/` - Data Extension integration
- [ ] SSJS form handlers
- [ ] AJAX submissions
- [ ] `security/` - Validation, sanitization, CSRF
- [ ] URL parameters handling
- [ ] SmartForm basics
- [ ] `templates/` - CloudPages templates

#### 3.7 API Integration (~6K tokens)
**Referência Principal**: https://developer.salesforce.com/developer-centers/marketing-cloud

- [ ] `SKILL.md` principal com visão geral da API
- [ ] **Autenticação**
  - [ ] OAuth 2.0 Web Server Flow
  - [ ] OAuth 2.0 JWT Bearer Token Flow
  - [ ] OAuth 2.0 Client Credentials (Server-to-Server)
  - [ ] Installed App Flow
  - [ ] Refresh Token handling
  - [ ] Token expiration management
  - [ ] Multiple authentication methods comparison

- [ ] **REST API - Fundamentos**
  - [ ] Base URL e versionamento (`/v1/`, `/v2/`, `/v3/`, `/v4/`, `/v5/`)
  - [ ] Headers required (Authorization, Content-Type, Accept)
  - [ ] HTTP methods (GET, POST, PUT, PATCH, DELETE)
  - [ ] Response status codes
  - [ ] Error handling e retry logic
  - [ ] Pagination (page, pageSize, limit, offset)
  - [ ] Filtering e ordering

- [ ] **REST API - Endpoints por Categoria**
  - [ ] **Assets** (`/asset/v1/content/assets/`) - Create, read, update, delete assets
  - [ ] **Data Extension** (`/data/v1/`) - DE operations, rows, schema
  - [ ] **Contacts** (`/contacts/v1/`) - Contact operations, attributes
  - [ ] **Contacts** (`/contact/v1/`) - Contact Key, merge rules
  - [ ] **Email** (`/messaging/v1/`) - Send triggers, validate
  - [ ] **Journey** (`/interaction/v1/`) - Journeys, activities, events
  - [ ] **Automation** (`/automation/v1/`) - Automations, schedules
  - [ ] **Campaign** (`/campaign/v1/`) - Campaign management
  - [ ] **Tracking** (`/tracking/v1/`) - Events, records
  - [ ] **List/Publication** (`/contacts/v1/`) - Lists, publications
  - [ ] **Content Builder** (`/asset/v1/`) - Content Builder assets
  - [ ] **Tenant-specific endpoints** - Understanding tenant URLs

- [ ] **SOAP API**
  - [ ] SOAP Envelope structure
  - [ ] WSDL reference
  - [ ] Authentication header
  - [ ] Create, Update, Delete operations
  - [ ] Retrieve operations with filter
  - [ ] Describe operations (schema discovery)
  - [ ] SOAP vs REST comparison

- [ ] **WSProxy (Advanced)**
  - [ ] `Script.Util.WSProxy` initialization
  - [ ] `retrieve` - Query objects
  - [ ] `create` - Create records
  - [ ] `update` - Update records
  - [ ] `delete` - Delete records
  - [ ] `describe` - Get object schema
  - [ ] `perform` - Execute actions
  - [ ] Custom headers and options
  - [ ] Pagination with WSProxy
  - [ ] Batch operations
  - [ ] Error handling with WSProxy

- [ ] **Operações Avançadas**
  - [ ] **Bulk API** - Handling large data volumes
  - [ ] **Async Operations** - Job creation, status polling
  - [ ] **Data Extract** - Extract patterns
  - [ ] **File Transfer** - Import/export files
  - [ ] **REST API - Query** - SOQL-like queries
  - [ ] **Streaming** - Real-time event handling

- [ ] **SSJS API Patterns**
  - [ ] Platform.Request methods
  - [ ] HTTP.Get, HTTP.Post, HTTP.Put
  - [ ] Platform.Function.InvokeEnperform
  - [ ] Script.Util.HttpRequest (when available)
  - [ ] Building request payloads
  - [ ] Parsing responses

- [ ] **Rate Limiting & Best Practices**
  - [ ] API rate limits per package type
  - [ ] Retry with exponential backoff
  - [ ] Request batching
  - [ ] Efficient pagination
  - [ ] Caching strategies
  - [ ] Concurrent request limits
  - [ ] Monitoring usage

- [ ] **SDKs e Integrações**
  - [ ] Python SDK (FuelSDK-Python)
  - [ ] Java SDK
  - [ ] Node.js/JavaScript SDK
  - [ ] Ruby SDK
  - [ ] PHP SDK
  - [ ] .NET/C# SDK
  - [ ] REST API via Postman/Insomnia
  - [ ] External integrations patterns

- [ ] **Common Use Cases**
  - [ ] Triggered sends via API
  - [ ] Data sync between systems
  - [ ] Automated journey injection
  - [ ] Custom reporting extraction
  - [ ] Contact management automation
  - [ ] Asset creation/management
  - [ ] Campaign creation workflow

#### 3.8 Journey Builder (~2.5K tokens)
- [ ] `SKILL.md` principal
- [ ] Activity types reference
- [ ] Entry sources
- [ ] Decision splits
- [ ] Journey patterns
- [ ] A/B testing in journeys

#### 3.9 Automation Studio (~2K tokens)
- [ ] `SKILL.md` principal
- [ ] Activity types
- [ ] Scheduling patterns
- [ ] Trigger types
- [ ] Common automation patterns

#### 3.10 Content Builder (~1.5K tokens)
- [ ] `SKILL.md` principal
- [ ] Asset types
- [ ] Templates
- [ ] Folders structure
- [ ] Content Block types

#### 3.11 Data Extension (~2K tokens)
- [ ] `SKILL.md` principal
- [ ] Field types reference
- [ ] Keys (Primary, Secondary, Composite)
- [ ] Relationships
- [ ] `design-patterns/` - Best practices

#### 3.12 Community Solutions (~2K tokens)
- [ ] `SKILL.md` principal
- [ ] `options/` - Lista de soluções disponíveis
- [ ] Integrações com repositórios selecionados:
  - [ ] SFMC-Cookbook (JoernBerkefeld)
  - [ ] email360/ssjs-lib
  - [ ] mc-cloud-pages-tricks (MarketingThibs)
  - [ ] sfmc-data-views (MarketingThibs)
  - [ ] mc-data-views-sql-schema (MateuszDabrowski)

### Fase 4: Copy, CTAs e KPIs
- [ ] `SKILL.md` principal
- [ ] Copywriting best practices
- [ ] CTA optimization
- [ ] Sistema de botões (anatomia, tamanhos, cores)
- [ ] KPI reference with benchmarks
- [ ] A/B testing strategies
- [ ] Personalization strategies

### Fase 5: Documentação
- [ ] `README.md` completo
- [ ] `core/meta/token-budget.md` - Guia de consumo de tokens
- [ ] Exemplos de uso por módulo

---

## 📊 Estimativa de Tokens por Módulo

| Módulo | Tamanho Estimado |
|--------|-----------------|
| SKILL.md raiz | ~150 tokens |
| AMPscript | ~4.500 tokens |
| SQL | ~2.500 tokens |
| DataViews | ~3.000 tokens |
| HTML Email | ~5.000 tokens |
| SSJS | ~3.000 tokens |
| CloudPages | ~4.000 tokens |
| API Integration | ~6.000 tokens |
| Journey Builder | ~2.500 tokens |
| Automation Studio | ~2.000 tokens |
| Content Builder | ~1.500 tokens |
| Data Extension | ~2.000 tokens |
| Community Solutions | ~2.000 tokens |
| **Total Skill Completa** | **~38.000+ tokens** |
| **Por Sessão (otimizado)** | **~5.000 - 18.000 tokens** |

---

## 🎨 Design System - Referência

Baseado em: `cemig-sim-design-system.html`

### Paletas de Cores
```
VERDE:  #00AA9A (primary), #8BC53F (secondary), #A0F2D3 (tertiary), #F0F8F7 (surface)
ROXO:   #7453E8 (primary), #A854E8 (secondary), #D6CAFF (tertiary), #F2EFFF (surface)
DESTAQUE: #FFCF00 (gold), #0071BC (blue-deep), #00A0C6 (turquoise)
NEUTROS: #333333 (dark), #696969 (medium), #F0F2F5 (light)
```

### Sistema de Espaçamento (8pt Grid)
```
xs: 4px | sm: 8px | md: 16px | lg: 24px | xl: 32px | 2xl: 40px | 3xl: 64px
```

### Tipografia
```
Fontes: Calibri, Arial, Helvetica, Verdana (web-safe)
H1: 32px (Bold) | H2: 24px (Bold) | H3: 20px (Bold) | Body: 16px | Caption: 12-14px
```

### Componentes
- Botões: 4px, 8px, 16px, 50px (pill) border-radius
- CTAs: padding mínimo 15px vertical, 25px horizontal
- Touch target mínimo: 44x44px

---

## 🔄 Alternância de Design System

```json
// config/default-design-system.json
{
  "active": "_default",
  "available": [
    "_default",
    "cliente-x-brasil",
    "cliente-y-usa"
  ]
}
```

**Para trocar de cliente**: Alterar apenas o campo `active`.

---

## 🔗 Referências Oficiais

### Salesforce Marketing Cloud Developer Center
**URL**: https://developer.salesforce.com/developer-centers/marketing-cloud

Contém toda a documentação oficial para:
- **APIs**: REST, SOAP, Bulk API
- **SDKs**: Python, Java, Node.js, Ruby, PHP, .NET
- **Autenticação**: OAuth 2.0, JWT, Client Credentials
- **Reference**: Todas as APIs documentadas com exemplos
- **Guides**: Best practices e getting started

### Documentação por Tipo de API

| API | Uso Principal | Versões |
|-----|--------------|---------|
| **REST API** | Operações CRUD, triggers, jornadas | v1, v2, v3, v4, v5 |
| **SOAP API** | Operaçes complexas, metadata | v1 (XML envelope) |
| **Bulk API** | Grandes volumes de dados | v1 |
| **Streaming API** | Eventos real-time | v1 |

---

## 📝 Histórico de Alterações

| Data | Versão | Descrição |
|------|--------|-----------|
| 2026-05-23 | 1.0 | Criação do documento de controle |
| 2026-05-23 | 1.1 | Expansão da seção API Integration (~3K → ~6K tokens) |
| 2026-05-23 | 1.1 | Inclusão de referência ao Salesforce Developer Center |
| 2026-05-23 | 2.0 | **IMPLEMENTAÇÃO CONCLUÍDA** - Todos os módulos v1.0 criados |
| 2026-05-25 | 2.1 | **IMPLEMENTAÇÃO V2.0** - 12 novos módulos + 9 Community Solutions |

---

## 🏷️ Metadata

- **Autor**: SFMC Copilot
- **Versão do Plano**: 2.0
- **Data de Criação**: 2026-05-23
- **Última Atualização**: 2026-05-23
- **Status**: ✅ Implementação Concluída

---

*Este documento serve como controle e checklist para implementação da skill SFMC Copilot.*