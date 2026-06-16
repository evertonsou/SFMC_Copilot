---
name: sfmc-copilot
description: Salesforce Marketing Cloud specialist skill v2.0. Load modules on demand for AmpScript, SQL, DataViews, HTML emails, CloudPages, APIs, Contact Builder, Einstein AI, Deliverability, and more.
version: "2.0"
---

# SFMC Copilot Skill v2.0

Assistente especialista em Salesforce Marketing Cloud. Conhecimento modular otimizado para consumo eficiente de tokens.

## Como Usar

Carregue módulos específicos conforme necessário:

> "Load ampscript module" ou "Preciso de ajuda com queries SQL no SFMC"

## Módulos Disponíveis — Core (v1.0)

| Módulo            | Descrição                                      | Tamanho      |
| ----------------- | ---------------------------------------------- | ------------ |
| `ampscript`       | Funções, personalização, debugging             | ~4.5K tokens |
| `sql`             | Query Activities, Data Extension queries       | ~2.5K tokens |
| `dataviews`       | System Data Views (Email, SMS, Push, Journeys) | ~3K tokens   |
| `html-email`      | Email development, responsividade, Outlook     | ~5K tokens   |
| `ssjs`            | Server-Side JavaScript                         | ~3K tokens   |
| `cloudpages`      | Landing pages, formulários, CloudPages         | ~4K tokens   |
| `api`             | REST/SOAP API, autenticação, WSProxy           | ~6K tokens   |
| `journey-builder` | Journey Builder configuration                  | ~2.5K tokens |
| `automation`      | Automation Studio workflows                    | ~2K tokens   |
| `content-builder` | Content Builder assets                         | ~1.5K tokens |
| `data-extension`  | Data Extension design patterns                 | ~2K tokens   |
| `community`       | Soluções customizadas da comunidade            | ~2K tokens   |
| `copy`            | Copywriting, CTAs, KPIs                        | ~2K tokens   |

## Módulos Disponíveis — Novos (v2.0)

| Módulo                   | Descrição                                                       | Tamanho      |
| ------------------------ | --------------------------------------------------------------- | ------------ |
| `contact-builder`        | Contact model, attribute sets, ContactKey, relationships        | ~2.5K tokens |
| `suppression-lists`      | Global/BU suppression, exclusion logic, compliance              | ~2K tokens   |
| `preference-centers`     | Custom preference center pages, AMPscript + SSJS                | ~2.5K tokens |
| `custom-unsubscribe`     | Unsubscribe page, SSJS handler, segurança                       | ~2K tokens   |
| `salesforce-integration` | CRM sync, Synchronized DEs, Salesforce entry events             | ~3K tokens   |
| `einstein-ai`            | Einstein Recommendations, Predictive Scoring, STO               | ~2K tokens   |
| `deliverability`         | IP warming, inbox testing, spam filters, DMARC                  | ~2.5K tokens |
| `engagement-scoring`     | Scoring models, tier segmentation, sunset policy                | ~2.5K tokens |
| `test-control-sends`     | Test sends, A/B tests, holdout groups                           | ~2K tokens   |
| `subscriber-management`  | Publication lists, subscription states, double opt-in           | ~2K tokens   |
| `mobileconnect`          | Keywords, short codes, SMS best practices                       | ~2K tokens   |
| `journey-advanced`       | A/B splits, Custom Activities, Salesforce entry events          | ~2.5K tokens |
| `whatsapp`               | WhatsApp Integration, templates, AMPscript variables, DataViews | ~2.5K tokens |

## Community Solutions (v2.0)

| Solução                       | Descrição                            | Módulo                                |
| ----------------------------- | ------------------------------------ | ------------------------------------- |
| **SFMC-Cookbook**             | Receitas AMPscript/SSJS (⭐ 136)     | `community/sfmc-cookbook`             |
| **mcdev (Accenture)**         | CI/CD e deploy de assets (⭐ 155)    | `community/mcdev`                     |
| **ssjs-lib (email360)**       | Biblioteca SSJS completa (⭐ 54)     | `community/ssjs-lib`                  |
| **awesome-sfmc**              | Lista curada 100+ recursos (⭐ 29+)  | `community/awesome-sfmc`              |
| **ssjs-docs.xyz**             | Documentação SSJS verificada         | `community/ssjs-docs`                 |
| **qrcode-sfmc-ssjs**          | Gerador de QR codes em SSJS (⭐ 10)  | `community/qrcode-sfmc`               |
| **sfmc-ai-query**             | GPT-4 → SQL queries (⭐ 11)          | `community/sfmc-ai-query`             |
| **asset-relationship-finder** | Mapear dependências de assets (⭐ 8) | `community/asset-relationship-finder` |
| **eslint-config-ssjs**        | ESLint para SSJS (⭐ 22)             | `community/eslint-ssjs`               |

## Personalização por Cliente (Clients)

A Skill é projetada de forma universal, mas possui seções customizadas para as particularidades de cada projeto, localizadas na pasta `clients/{cliente-slug}/`:

- **Design System:** `clients/{cliente-slug}/design-system` em formato `.md`, `.txt` ou `.html` (ex: `cemig-sim-design-system.html`). Contém tokens de cores, tipografia, componentes de e-mail e regras de layout do cliente. O assistente deve ler e analisar qualquer um desses formatos.
- **Modelos de DEs:** `clients/{cliente-slug}/data-extensions.md` (definições de campos, chaves e estruturas de tabelas sincronizadas gerais da organização. **IMPORTANTE:** Tabelas específicas de projetos/campanhas não devem ser salvas aqui, mas sim em `/src/data-extensions/` de forma local).
- **Rastreamento de Canais:** `clients/{cliente-slug}/whatsapp.md` (arquitetura de extração granular e templates de WhatsApp do cliente).
- **Fluxo de Trabalho:** `clients/{cliente-slug}/workflow.md` (diretrizes de organização de arquivos funcionais, localização de scripts em `/src/` e convenções de commit).

_Para novos projetos, crie a pasta correspondente ao cliente e implemente seus respectivos arquivos de diretrizes._

## Diretrizes Globais de Desenvolvimento de E-mails (AMPscript e Nomenclatura)

- **Proibição de Conteúdo Dinâmico Complexo:** Evite o uso de lógicas condicionais complexas em AMPscript para alternar conteúdos inteiros, blocos de texto ou banners. Estruture os e-mails como HTML estático normal e reserve o AMPscript exclusivamente para variáveis básicas do contato (ex: nome, link de destino do CTA).
- **Padrão de Nomenclatura de Arquivos HTML:** Todos os arquivos de e-mail criados no repositório devem, por padrão, ser nomeados seguindo a estrutura contextual de disparo:
  `{campanha}_{etapa_jornada}_{segmentacao_publico}_{regua_dia}_{tipo_peca}.html`
  - _Exemplo:_ `aquecimento_cruzeiro_prospeccao_socio5estrelas_d05_oferta.html`
- **Tag de Tracking do SFMC (Obrigatória):** Todo código HTML de e-mail criado para o Salesforce Marketing Cloud deve obrigatoriamente conter o snippet `<custom name="opencounter" type="tracking"/>` inserido no início do `<body>` (logo após a tag de abertura `<body>` e antes do pre-header invisível). Isso é essencial para que o SFMC registre as aberturas de e-mail.
- **Auditoria de Código Herdado:** Sempre realize uma auditoria de conformidade com o Design System em layouts recebidos/antigos. Certifique-se de remover estilos obsoletos ou que causem inconsistências (ex: `box-shadow` que falha no Outlook) e converta-os para estilos inline puros com fontes e cores seguras do cliente.
- **Identidade em Parcerias (Co-branding):** Em campanhas de co-branding, a estrutura visual (cards, botões, rodapés) deve pertencer estritamente à identidade do remetente principal (ex: Cemig SIM). A marca parceira (ex: Cruzeiro) deve ser introduzida de forma secundária apenas no conteúdo da peça (imagens de banners e cores de destaques pontuais).
- **Acessibilidade e Semântica:** Prefira o uso de tags HTML estruturadas (`<h1>`, `<h2>`) com CSS inline para títulos importantes, em vez de apenas spans ou parágrafos em negrito, otimizando a leitura por leitores de tela.
- **Suporte a Bordas Arredondadas em Tabelas:** Para garantir a renderização de cantos arredondados (`border-radius`) em tabelas em clientes web e mobile modernos, utilize estilos inline contendo `border-collapse: separate !important;` e `overflow: hidden;` aplicados diretamente na tag `<table>`.
- **Simplificação de Preheaders:** Quando a campanha prever a inclusão de preheader dinâmico ou estático usando o recurso nativo da interface de envio do SFMC, não insira blocos de pre-header invisíveis no HTML, evitando redundâncias.
- **Botões CTA Responsivos no Mobile:** Evite desalinhamentos de botões no mobile concentrando todas as propriedades visuais (fundo, borda e raio de borda) diretamente na tag `<a>` (e não na célula `<td>` que a envolve). Para garantir que o botão continue centralizado no celular, o estilo responsivo do media query deve aplicar `display: inline-block !important;` (em vez de `display: block !important`), respeitando o alinhamento `align="center"` do container.
- **Degradação Graciosa em Cards Complexos (Outlook Classic):** Para cards ou blocos com layouts internos que contêm bordas laterais específicas (ex: borda verde esquerda) e margens precisas, evite tentar forçar cantos arredondados no Outlook Classic utilizando estruturas VML como `<v:roundrect>`, pois elas causam falhas horizontais (linhas brancas de renderização) e quebras de margem. Adote a degradação graciosa: mantenha a tabela HTML simples com `border-radius` (arredondada em gerenciadores modernos) e permita que ela exiba cantos retos de 90 graus no Outlook Classic de forma estável.
- **Círculos Perfeitos no Mobile (Checklists e Linhas do Tempo):** Para evitar que marcadores numerados fiquem deformados ou elípticos no mobile quando o texto lateral quebrar linhas, estilize a própria tag `<table>` interna (fundo e `border-radius: 50%` ou px correspondente) em vez de aplicar no `<td>` ou usar `div`s. Trave as dimensões com `!important` na altura e largura em todas as propriedades inline da tabela (`width`, `height`, `min-width`, `min-height`, `max-width`, `max-height`).
- **Centralização Vertical de Números no Outlook Classic:** Para centralizar números perfeitamente dentro de blocos de progresso no Outlook, use `valign="middle" align="center"` no TD interno de 32x32px e limite o `line-height` ao tamanho exato da fonte (ex: `line-height: 16px` para fontes de 16px), em vez de usar line-heights maiores que forçam offsets desalinhados no Word.
- **Quebras de Linha Sem Espaço Duplo:** Nunca combine `display: block` em elementos de texto (`span`, `div`) com a tag `<br />` física. Mantenha os elementos de texto como inline (comportamento padrão) e deixe a quebra exclusivamente a cargo do `<br />` físico, prevenindo que leitores modernos criem uma linha em branco indesejada (espaço duplo).
- **Verificação Prévia Obrigatória:** Antes de apresentar qualquer peça ou código HTML finalizado ao usuário, o assistente DEVE validar rigorosamente o código e os estilos visuais para garantir que eles atendem a todas as melhores práticas de responsividade móvel e renderização do Outlook Classic (degradação graciosa e centralização baseada em tabelas).
- **AMPscript em Linha Única contra Injeção de HTML:** O editor visual (WYSIWYG) do Content Builder no SFMC costuma injetar tags HTML indesejadas (como `<br>` ou `<p>`) dentro de blocos de AMPscript que possuem quebras de linha. Para personalizações ou extrações de nome, sempre utilize blocos escritos em uma única linha contínua, sem quebras de linha no código.
- **Modelo de Extração de Primeiro Nome (Fórmula Oficial):** Para extrair o primeiro nome de um campo `NomeCompleto` com formatação adequada e sem quebras, utilize a estrutura oficial testada:
  `%%[ VAR @name, @primeiroNome SET @name = AttributeValue("NomeCompleto") IF IndexOf(@name, " ") > 0 THEN SET @primeiroNome = ProperCase(Substring(@name, 1, Subtract(IndexOf(@name, " "), 1))) ELSE SET @primeiroNome = ProperCase(@name) ENDIF ]%%`

## Diretrizes Globais de Tomada de Decisão (Evitar Suposições)

- **Proibição de Suposições:** O assistente **NÃO** deve tomar decisões autônomas, pressupor ou inventar regras de negócio, lógicas de manipulação de dados (ex: formatações, cálculos) ou estruturas de tabelas sincronizadas que não estejam explicitamente especificadas nos requisitos ou documentos do projeto.
- **Diferenciação de Data Extensions (Gerais vs. Específicas):** Diferencie rigorosamente as tabelas gerais/sincronizadas da organização (ex: `Lead_Salesforce`, `Contact_Salesforce` em `data-extensions.md` do cliente) das tabelas criadas para campanhas ou projetos específicos (ex: DEX de disparo). As DEs específicas de projetos devem ser armazenadas localmente em `/src/data-extensions/` (formato JSON) e documentadas apenas no `implementation_plan.md` do projeto, nunca inseridas no arquivo geral do cliente.
- **Rastreamento de Canais sem Data Views Nativas (WhatsApp, Push, etc.):** Como o SFMC não possui Data Views de sistema nativas para tracking de alguns canais (como WhatsApp e MobilePush), o assistente **DEVE** ler o arquivo `clients/{cliente-slug}/data-extensions.md` do respectivo cliente para identificar as Data Extensions customizadas que atuam como tabelas de tracking geral da org. Nessas consultas, identifique qual campo da tabela customizada do cliente atua como o identificador único do disparo (equivalente ao `JobID` do e-mail) e dê preferência a consultas consolidadas para evitar JOINS desnecessários.
- **Habilidade de Perguntar:** Sempre que houver ambiguidades, lacunas de informação, campos desconhecidos ou cenários cinzentos nos requisitos, o assistente **DEVE**:
  1. Interromper a geração de código ou alteração de arquivos de execução do projeto.
  2. Identificar e listar claramente o ponto que gera dúvida.
  3. Formular perguntas diretas, concisas e em português brasileiro para que o usuário decida a abordagem correta antes de prosseguir.

## Referências

- **Repositório Oficial**: https://github.com/evertonsou/SFMC_Copilot
- **Developer Center**: https://developer.salesforce.com/developer-centers/marketing-cloud
- **AMPscript Guide**: https://ampscript.guide
- **SSJS Docs**: https://ssjsdocs.xyz
- **Documentação**: Módulos individuais contêm referências completas

---

_Skill v2.0 — 25 módulos + 9 community solutions | ~71K tokens totais | Atualizado: 2026-05-25_
