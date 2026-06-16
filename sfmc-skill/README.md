# SFMC Copilot Skill

Assistente especialista em Salesforce Marketing Cloud com arquitetura otimizada para consumo de tokens.

## Visão Geral

Esta skill fornece conhecimento modular sobre Salesforce Marketing Cloud, organizado para carregamento sob demanda. Cada módulo contém referência completa da tecnologia, exemplos práticos e patterns consolidados.

**Referência Principal**: https://developer.salesforce.com/developer-centers/marketing-cloud

## Arquitetura

### Carregamento Progressivo

A skill usa carregamento progressivo para otimizar o consumo de tokens:

1. **SKILL.md raiz** (~150 tokens) - carrega sempre
2. **Módulos específicos** (~2.000-5.000 tokens) - carregados sob demanda

### Como Usar

Carregue módulos específicos conforme necessário:

```
"Load ampscript module"
"Preciso de ajuda com queries SQL no SFMC"
"Como fazer um formulário em CloudPages?"
```

## Módulos Disponíveis

| Módulo | Descrição | Tamanho |
|--------|-----------|---------|
| `ampscript` | Funções, personalização, debugging | ~4.5K tokens |
| `sql` | Query Activities, Data Extension queries | ~2.5K tokens |
| `dataviews` | System Data Views | ~3K tokens |
| `html-email` | Email development, responsividade, Outlook | ~5K tokens |
| `ssjs` | Server-Side JavaScript | ~3K tokens |
| `cloudpages` | Landing pages, formulários | ~4K tokens |
| `api` | REST/SOAP API, autenticação, WSProxy | ~6K tokens |
| `journey-builder` | Journey Builder | ~2.5K tokens |
| `automation` | Automation Studio | ~2K tokens |
| `content-builder` | Content Builder | ~1.5K tokens |
| `data-extension` | Data Extension design | ~2K tokens |
| `community` | Soluções customizadas | ~2K tokens |
| `copy` | Copywriting, CTAs, KPIs | ~2K tokens |

## Design System

O design system é intercambiável por cliente. Cada design system contém:

- `brand-colors.json` - Paletas de cores
- `typography.json` - Sistema tipográfico
- `spacing.json` - Sistema de espaçamento
- `components/` - Componentes (botões, cards, etc.)
- `email-templates/` - Templates base
- `guidelines/` - Diretrizes de uso

### Trocar Design System

Edite `config/default-design-system.json`:

```json
{
  "active": "cliente-x",
  "available": ["_default", "cliente-x", "cliente-y"]
}
```

## Estrutura de Diretórios

```
sfmc-skill/
├── SKILL.md                    # Arquivo principal
├── README.md                   # Esta documentação
├── core/
│   └── modules/               # Módulos de conhecimento
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
│       └── copy/
├── design-system/
│   ├── _default/               # Design system base
│   └── [cliente-folder]/      # Design systems específicos
└── config/
    └── default-design-system.json
```

## Recursos Oficiais

- **Developer Center**: https://developer.salesforce.com/developer-centers/marketing-cloud
- **Trailhead**: https://trailhead.salesforce.com/pt-BR/content/learn/modules/marketing-cloud-developer
- **Stack Exchange**: https://salesforce.stackexchange.com/questions/tagged/marketing-cloud

## Contribuidores e Créditos

### Repositórios da Comunidade

- [SFMC-Cookbook](https://github.com/JoernBerkefeld/SFMC-Cookbook)
- [email360/ssjs-lib](https://github.com/email360/ssjs-lib)
- [mc-data-views-sql-schema](https://github.com/MateuszDabrowski/sfmc-data-views-sql-schema)

### Referências

- Design System baseado em `cemig-sim-design-system.html`
- Boas práticas de [Litmus](https://www.litmus.com/), [Campaign Monitor](https://www.campaignmonitor.com/)

## Licença

Este projeto é para uso interno. Soluções da comunidade mantêm suas próprias licenças.

---

**Versão**: 1.0
**Última Atualização**: 2026-05-23