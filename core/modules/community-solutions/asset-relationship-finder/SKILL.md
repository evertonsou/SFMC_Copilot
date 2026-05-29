# Module: Asset Relationship Finder

> **Skill**: sfmc-copilot | **Categoria**: Community Solutions | **Tokens**: ~1K  
> **Repositório**: [asset_relationship_finder](https://github.com/sfmcdeveloper/asset_relationship_finder) ⭐ 8

---

## Visão Geral

**Asset Relationship Finder** é uma ferramenta para **mapear dependências entre assets** no Salesforce Marketing Cloud. Responde perguntas como: "Onde este Content Block é usado?", "Quais jornadas usam este e-mail?" e "O que vai quebrar se eu deletar este template?".

---

## Problema que Resolve

```
Sem a ferramenta:
❌ Deletar um Content Block que está em 12 e-mails ativos
❌ Modificar um template sem saber quantas versões o usam
❌ Refatorar uma DE sem saber quais queries dependem dela
❌ Mover um asset para outra pasta e quebrar referências

Com a ferramenta:
✅ Mapa visual de dependências antes de qualquer mudança
✅ "Impact analysis" antes de deletar ou modificar
✅ Auditoria completa de uso de assets
```

---

## Tipos de Relacionamentos Mapeados

| Asset A | Relacionamento | Asset B |
|---------|---------------|---------|
| Content Block | usado em | Email Template |
| Email Template | usado em | Email Send |
| Email | disparado por | Journey Activity |
| Journey | aciona | Automation |
| Query | popula | Data Extension |
| DE | fonte de entrada | Journey |
| Triggered Send | referencia | Email |
| CloudPage | usa | Script Asset |

---

## Uso via REST API (Manual)

Mesmo sem a ferramenta, você pode mapear relacionamentos via API:

### Buscar Emails que Usam um Content Block

```javascript
<script runat="server">
Platform.Load("Core", "1");

// Buscar via REST API — Assets endpoint
function findAssetUsage(contentBlockId, accessToken, subdomain) {
  var req = new Script.Util.HttpRequest(
    "https://" + subdomain + ".rest.marketingcloudapis.com" +
    "/asset/v1/content/assets/" + contentBlockId + "/relationships"
  );
  req.method = "GET";
  req.setHeader("Authorization", "Bearer " + accessToken);
  req.setHeader("Content-Type", "application/json");
  
  var response = req.send();
  return Platform.Function.ParseJSON(response.content);
}

var usage = findAssetUsage("12345", accessToken, "mc123456");
Write(Stringify(usage));
</script>
```

### Listar Assets via WSProxy

```javascript
<script runat="server">
Platform.Load("Core", "1");

var api = new Script.Util.WSProxy();

// Buscar todos os e-mails que referenciam uma DE específica
var cols = ["ID", "Name", "CustomerKey", "Description", "Subject"];
var filter = {
  Property: "AssetType.Name",
  SimpleOperator: "equals",
  Value: "htmlemail"
};

var result = api.retrieve("Asset", cols, filter);

if (result.Results) {
  for (var i = 0; i < result.Results.length; i++) {
    var asset = result.Results[i];
    Write(asset.Name + " (ID: " + asset.ID + ")<br>");
  }
}
</script>
```

---

## Auditoria Manual via SQL

### Queries que usam uma DE específica

```sql
-- Nota: SQL no SFMC não acessa metadados de Query Activities diretamente
-- Use via API ou ferramenta externa para mapear

-- Alternativa: buscar via _Sent quais emails foram enviados para uma DE
SELECT DISTINCT
  j.EmailName,
  j.JobID,
  j.SchedTime,
  COUNT(DISTINCT se.SubscriberKey) AS TotalSent
FROM _Job j
JOIN _Sent se ON j.JobID = se.JobID
WHERE j.SchedTime >= DATEADD(MONTH, -3, GETDATE())
GROUP BY j.EmailName, j.JobID, j.SchedTime
ORDER BY j.SchedTime DESC
```

---

## Checklist de Impact Analysis (Manual)

Antes de deletar ou modificar qualquer asset, verificar:

```
CONTENT BLOCK:
□ Procurar referência no HTML de todos os e-mails ativos
□ Buscar em templates e snippets
□ Verificar CloudPages que podem referenciar

EMAIL/TEMPLATE:
□ Verificar Journey Builder (Activities → Email)
□ Verificar Triggered Sends
□ Verificar Automation Studio (Email Activities)
□ Verificar A/B Tests em andamento

DATA EXTENSION:
□ Verificar Query Activities que fazem INSERT INTO esta DE
□ Verificar Journey Entry Sources
□ Verificar Automation Steps
□ Verificar CloudPages que fazem Lookup/UpsertData

AUTOMATION:
□ Verificar se está ativa ou pausada
□ Verificar dependências de DEs de entrada/saída
□ Verificar se alimenta alguma Journey

JOURNEY:
□ Verificar se está ativa (não pode excluir ativas)
□ Verificar DEs de entrada e saída
□ Versões históricas (arquivar, não deletar)
```

---

## Boas Práticas de Gerenciamento de Assets

```
✅ Nunca deletar um asset sem impact analysis
✅ Usar naming convention clara: [Tipo]_[Cliente]_[Descrição]_[Versão]
✅ Manter pasta "Arquivo" para assets inativos (não deletar)
✅ Documentar em comentários no HTML o que cada bloco faz
✅ Usar tags/labels no Content Builder para facilitar busca
✅ Revisar assets não utilizados trimestralmente
✅ Manter registro de mudanças em DE_AssetChangelog
✅ Testar em sandbox antes de modificar em produção
```

---

## DE de Changelog de Assets (Sugestão)

```
Nome: DE_AssetChangelog
Campos:
  AssetID      Text(50)   PK
  AssetName    Text(200)
  AssetType    Text(50)
  ChangeType   Text(50)   (Create, Update, Delete, Archive)
  ChangedBy    Text(100)
  ChangeDate   Date
  Reason       Text(500)
  RelatedAssets Text(500) (lista de IDs afetados)
```

---

## Links

- **GitHub**: https://github.com/sfmcdeveloper/asset_relationship_finder
- **Documentação API Assets**: https://developer.salesforce.com/docs/marketing/marketing-cloud/references/mc_rest_content_asset_v1
- **Complemento**: Usar com mcdev para versionamento de assets
