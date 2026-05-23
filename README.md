# SFMC Copilot

Assistente especialista em Salesforce Marketing Cloud com arquitetura modular otimizada para consumo eficiente de tokens.

## Estrutura

```
sfmc-copilot/
├── docs/                          # Documentação e planos
│   ├── IMPLEMENTATION-CONTROL.md  # Controle de implementação (v1.0)
│   └── IMPLEMENTATION-CONTROL-v2.md # Plano v2.0 (novas habilidades)
├── sfmc-skill/                    # Skill principal
│   ├── SKILL.md                  # Arquivo raiz da skill
│   ├── README.md                 # Documentação da skill
│   ├── design-system.md          # Design system
│   └── core/modules/             # 13 módulos de conhecimento
└── .gitignore
```

## Habilidades Atuais (v1.0)

- AMPscript (8 categorias)
- SQL Query Activities
- DataViews (8 categorias: Email, SMS, Push, Subscribers, Journeys, Automation, Content, Enterprise)
- HTML Email (componentes, Outlook, Dark Mode)
- SSJS (Server-Side JavaScript)
- CloudPages (formulários, segurança)
- API Integration (REST, SOAP, WSProxy, Auth)
- Journey Builder
- Automation Studio
- Content Builder
- Data Extension
- Community Solutions
- Copywriting/CTAs/KPIs

## Design System

O design system está em `sfmc-skill/design-system.md`. Para usar em outro cliente, duplique a pasta `sfmc-skill` e edite o arquivo de design.

## Próximos Passos (v2.0)

Veja `docs/IMPLEMENTATION-CONTROL-v2.md` para o plano de:
- Novos módulos (Contact Builder, Suppression Lists, Einstein AI, etc.)
- Community Solutions (SFMC-Cookbook, mcdev, email360, etc.)

## Uso

Carregue módulos específicos conforme necessário:

```
"Load ampscript module"
"Preciso de ajuda com queries SQL no SFMC"
"Como criar um formulário em CloudPages?"
```

## Referências

- [Salesforce Marketing Cloud Developer Center](https://developer.salesforce.com/developer-centers/marketing-cloud)
- [Awesome SFMC](https://github.com/ttntm/awesome-sfmc)

---

**Versão**: 1.0
**Última Atualização**: 2026-05-23