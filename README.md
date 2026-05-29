# SFMC Copilot Skill

Assistente especialista em Salesforce Marketing Cloud com arquitetura otimizada para consumo de tokens.

## Visão Geral

Esta skill fornece conhecimento modular sobre Salesforce Marketing Cloud, organizado para carregamento sob demanda. Cada módulo contém referência completa da tecnologia, exemplos práticos e patterns consolidados.

**Referência Principal**: https://developer.salesforce.com/developer-centers/marketing-cloud

## Arquitetura

### Carregamento Progressivo

A skill usa carregamento progressivo para otimizar o consumo de tokens:

1. **SKILL.md raiz** (~150 tokens) - carrega sempre
2. **Módulos específicos** (~2.000-6.000 tokens) - carregados sob demanda

### Como Usar

Carregue módulos específicos conforme necessário:

```
"Load ampscript module"
"Preciso de ajuda com queries SQL no SFMC"
"Como fazer um formulário em CloudPages?"
```

## Módulos Disponíveis — V2.0

| Módulo | Descrição | Tamanho |
|--------|-----------|---------|
| `ampscript` | Funções, personalização, debugging | ~4.5K tokens |
| `sql` | Query Activities, Data Extension queries | ~2.5K tokens |
| `dataviews` | System Data Views (Email, SMS, Push, Subscribers, Journeys) | ~3K tokens |
| `html-email` | Email development, responsividade, Outlook, Dark Mode | ~5K tokens |
| `ssjs` | Server-Side JavaScript | ~3K tokens |
| `cloudpages` | Landing pages, formulários, segurança | ~4K tokens |
| `api` | REST/SOAP API, autenticação, WSProxy, Bulk | ~6K tokens |
| `journey-builder` | Journey Builder | ~2.5K tokens |
| `automation` | Automation Studio | ~2K tokens |
| `content-builder` | Content Builder | ~1.5K tokens |
| `data-extension` | Data Extension design | ~2K tokens |
| `copy` | Copywriting, CTAs, KPIs | ~2K tokens |
| `contact-builder` | Contact model, relationships | ~2.5K tokens |
| `suppression-lists` | Global/BU suppression, compliance | ~2K tokens |
| `preference-centers` | Custom preference center pages | ~2.5K tokens |
| `custom-unsubscribe` | Unsubscribe page, SSJS handler | ~2K tokens |
| `salesforce-integration` | CRM sync, Synchronized DEs | ~3K tokens |
| `einstein-ai` | Einstein Recommendations, STO | ~2K tokens |
| `deliverability` | IP warming, inbox testing, DMARC | ~2.5K tokens |
| `engagement-scoring` | Scoring models, tier segmentation | ~2.5K tokens |
| `test-control-sends` | Test sends, A/B tests | ~2K tokens |
| `subscriber-management` | Publication lists, double opt-in | ~2K tokens |
| `mobileconnect` | Keywords, short codes, SMS | ~2K tokens |
| `journey-advanced` | A/B splits, Custom Activities | ~2.5K tokens |

### Community Solutions

| Solução | Descrição | Módulo |
|---------|-----------|--------|
| **SFMC-Cookbook** | Receitas AMPscript/SSJS (⭐ 136) | `community/sfmc-cookbook` |
| **mcdev (Accenture)** | CI/CD e deploy de assets (⭐ 155) | `community/mcdev` |
| **ssjs-lib (email360)** | Biblioteca SSJS completa (⭐ 54) | `community/ssjs-lib` |
| **awesome-sfmc** | Lista curada 100+ recursos (⭐ 29+) | `community/awesome-sfmc` |
| **ssjs-docs.xyz** | Documentação SSJS verificada | `community/ssjs-docs` |
| **qrcode-sfmc-ssjs** | Gerador de QR codes em SSJS (⭐ 10) | `community/qrcode-sfmc` |
| **sfmc-ai-query** | GPT-4 → SQL queries (⭐ 11) | `community/sfmc-ai-query` |
| **asset-relationship-finder** | Mapear dependências de assets (⭐ 8) | `community/asset-relationship-finder` |
| **eslint-config-ssjs** | ESLint para SSJS (⭐ 22) | `community/eslint-ssjs` |

## Personalização por Cliente (Clients)

A Skill é projetada de forma universal, mas possui seções customizadas para as particularidades de cada projeto, localizadas na pasta `clients/{cliente-slug}/`:
- **Design System:** `clients/{cliente-slug}/design-system` em formato `.md`, `.txt` ou `.html` (ex: `cemig-sim-design-system.html`). Contém tokens de cores, tipografia, componentes de e-mail e regras de layout do cliente.
- **Modelos de DEs:** `clients/{cliente-slug}/data-extensions.md` (definições de campos e padrões de nomenclatura das Data Extensions).
- **Rastreamento de Canais:** `clients/{cliente-slug}/whatsapp.md` (arquitetura de extração granular e modelos de mensagens do cliente).

## Estrutura de Diretórios

```
sfmc-copilot/
├── SKILL.md                    # Arquivo principal da skill
├── README.md                   # Esta documentação
├── clients/                    # Configurações específicas de clientes
│   └── cemig-sim/
│       ├── cemig-sim-design-system.html # Design system específico da Cemig SIM (.html, .txt ou .md)
│       ├── data-extensions.md  # Modelos de Data Extensions específicas
│       ├── whatsapp.md         # Rastreamento/Templates de WhatsApp
│       └── workflow.md         # Diretrizes de fluxo de trabalho do cliente
├── core/
│   └── modules/               # Módulos de conhecimento (26 módulos)
│       ├── ampscript/
│       ├── sql/
│       ├── dataviews/
│       ├── html-email/
│       ├── ssjs/
│       ├── cloudpages/
│       ├── api-integration/
│       ├── journey-builder/
│       ├── automation-studio/
│       ├── content-builder/
│       ├── data-extension/
│       ├── community-solutions/
│       ├── copy/
│       ├── whatsapp/
│       └── ... (v2.0 modules)
├── docs/
│   └── IMPLEMENTATION-CONTROL-v2.md
└── .gitignore
```

## Recursos Oficiais

- **Developer Center**: https://developer.salesforce.com/developer-centers/marketing-cloud
- **Trailhead**: https://trailhead.salesforce.com/pt-BR/content/learn/modules/marketing-cloud-developer
- **AMPscript Guide**: https://ampscript.guide
- **SSJS Docs**: https://ssjsdocs.xyz

---

**Versão**: 2.0  
**Última Atualização**: 2026-05-25  
**Total de Módulos**: 25 (12 novos na v2.0)  
**Total de Community Solutions**: 9  
**Tokens Estimados**: ~71K tokens