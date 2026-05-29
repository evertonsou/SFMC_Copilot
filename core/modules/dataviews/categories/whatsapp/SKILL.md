---
name: sfmc-dataviews-whatsapp
description: Informações sobre rastreamento de WhatsApp no SFMC — sem DataView nativa. Usa Chat Messaging Data Extract.
---

# DataViews - WhatsApp

## ⚠️ Importante: Não existe DataView nativa para WhatsApp

Diferente do e-mail (`_Sent`, `_Open`, `_Click`) ou SMS (`_SMSMessageTracking`), **o Salesforce Marketing Cloud NÃO possui uma DataView de sistema nativa para mensagens de WhatsApp**.

Qualquer referência a uma tabela como `_WhatsAppMessageTracking` em queries de SQL **é incorreta e irá falhar**.

O rastreamento de WhatsApp no SFMC é feito por dois meios distintos:

---

## Opção 1 — KPIs Agregados: Intelligence Reports (Datorama)

Para métricas consolidadas (taxa de entrega, leitura, falhas por campanha), use o painel nativo:

- **Acesso:** Marketing Cloud Intelligence → Aba **WhatsApp Engagement**
- **Métricas disponíveis:** Enviados, Entregues, Lidos, Taxa de Leitura, Falhas, Opt-outs
- **Referência:** [WhatsApp Engagement Dashboard](https://help.salesforce.com/s/articleView?id=mktg.mc_dat_dash_whatsapp_engage.htm&type=5)

> 💡 Use esta opção quando o objetivo for acompanhamento de performance de campanha (dashboards, relatórios executivos).

---

## Opção 2 — Dados Granulares por Assinante: Chat Messaging Data Extract

Para análise individual por contato (quem recebeu, quem leu, erros específicos), é necessário configurar um **Data Extract** no Automation Studio.

- **Tipo de extract:** `ChatMessagingDetailExtract`
- **Saída:** arquivo CSV gerado no Safe Harbor
- **Referência:** [Chat Messaging Data Extract](https://help.salesforce.com/s/articleView?id=mktg.mc_jb_whatsapp_data_extract.htm&type=5)

Após a extração, os dados são importados para uma **Data Extension personalizada** do cliente. Os campos disponíveis na DE variam conforme a configuração do extract.

> 💡 Use esta opção quando precisar de dados granulares para SQL, jornadas baseadas em comportamento de WhatsApp ou relatórios personalizados.

---

## Quando um usuário pede dados de WhatsApp — fluxo de decisão

```
Usuário quer dados de WhatsApp?
    │
    ├── Quer KPIs/métricas gerais?
    │       └── → Oriente para o Intelligence Reports (Datorama)
    │
    └── Quer dados granulares por assinante?
            │
            ├── Já tem DE com o extract configurado?
            │       ├── SIM → Peça o nome da DE e os campos disponíveis
            │       │         → Monte queries SQL personalizadas
            │       └── NÃO → Oriente o passo a passo no módulo `whatsapp`
```

Ver detalhes completos no módulo principal: `whatsapp`
