# Cemig SIM - Especificações de WhatsApp e Extração de Dados

Este documento centraliza as regras, templates e fluxos específicos para o canal WhatsApp na Cemig SIM no Salesforce Marketing Cloud (SFMC).

---

## 1. Rastreamento e Extração de Dados (Chat Messaging Extract)

Conforme a arquitetura do SFMC, a Cemig SIM não possui uma Data View nativa para WhatsApp. O rastreamento de envios, entregas e leituras é realizado da seguinte forma:

- **Métricas Consolidadas (KPIs)**: Acompanhadas via **Marketing Cloud Intelligence Reports (Datorama)**.
- **Métricas Granulares (Nível de Contato)**: Extraídas via processo de **Chat Messaging Data Extract** (`ChatMessagingDetailExtract`) no Automation Studio.

### Fluxo de Extração e Tabelas de Destino:
1. **Ativação da Extração**: A extração é realizada via atividade **Chat Messaging Data Extract** (`ChatMessagingDetailExtract`) no Automation Studio.
2. **Importação para DEs de Tracking**: Como não existem System Data Views nativas de WhatsApp no SFMC, os dados de comportamento e status do canal são importados e consolidados em três Data Extensions gerais de tracking da org:
   - **`DEX_WHATSAPP_TRACKING_SEND`**: Contém o histórico detalhado de disparos efetuados.
   - **`DEX_WHATSAPP_TRACKING_DELIVERY`**: Contém o histórico de recebimento, leitura e falhas de entrega.
   - **`DEX_WHATSAPP_POTENCIALUNSUBS`**: Registra contatos com alto índice de bounces ou potencial de opt-out (descadastro).

> [!IMPORTANT]
> **Consulta Otimizada (Tabela Consolidada):** Como a tabela **`DEX_WHATSAPP_TRACKING_DELIVERY`** já armazena de forma consolidada os dados do envio (como `ActivityName` e `MobileNumber`) e os status de recebimento/engajamento no campo `Status` (sendo o campo **`SendIdentifier`** o equivalente ao `JobID` de e-mail que identifica de forma única o disparo), ela deve ser utilizada como a fonte primária e direta para a maioria das queries de análise de métricas e disparos, eliminando a necessidade de cruzamento (JOIN) com as outras tabelas, a não ser que dados específicos das outras DEs sejam exigidos.

---

## 2. Modelos de Templates do WhatsApp (Aprovados)

*(Esta seção será preenchida à medida que os templates de WhatsApp forem desenvolvidos e homologados na Meta para a régua de aquecimento).*
