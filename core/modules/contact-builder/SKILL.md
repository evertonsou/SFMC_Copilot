# Module: Contact Builder

> **Skill**: sfmc-copilot | **Categoria**: Contact Management | **Tokens**: ~2.5K

---

## Visão Geral

O **Contact Builder** é o hub central de gerenciamento de contatos no SFMC. Centraliza dados de múltiplas fontes (DEs, Sales Cloud, mobile) em um perfil unificado por `ContactKey`.

---

## Contact Model

### Conceitos Fundamentais

| Conceito | Descrição |
|----------|-----------|
| **Contact** | Registro único identificado por `ContactKey` |
| **ContactKey** | Identificador universal do contato (string, até 36 chars) |
| **Attribute Set** | Grupo de atributos (campos) associados a um contato |
| **Data Source** | Origem dos dados (DE, Sales Cloud, Mobile) |
| **Population** | Segmento de contatos com atributos comuns |
| **Relationship** | Vínculo entre Attribute Sets via chave compartilhada |

### ContactKey — Boas Práticas

```
✅ Use o mesmo ContactKey em todos os canais (email, SMS, push)
✅ Prefira o CRM ID / Subscriber Key existente
✅ Evite e-mails como ContactKey (mudam com o tempo)
✅ Formato recomendado: UUID ou ID numérico do sistema fonte
✅ Mantenha consistência entre BUs (Business Units)
```

---

## Attribute Sets

### O que são
Attribute Sets são **grupos de campos** vinculados ao Contact Record. Funcionam como extensões do perfil do contato.

### Tipos

| Tipo | Descrição |
|------|-----------|
| **System** | Criados automaticamente pelo SFMC (Email Address, Mobile Number) |
| **Custom** | Criados pelo usuário via Contact Builder |
| **Synchronized** | Mapeados de objetos do Sales Cloud |

### Criar Attribute Set

1. **Contact Builder** → **Attribute Sets** → **Create**
2. Definir nome, descrição
3. Adicionar campos (tipo, obrigatoriedade)
4. Associar Data Source
5. Definir relacionamento com Contact Record

### Tipos de Campo

| Tipo | Uso |
|------|-----|
| `Text` | Strings gerais (até 4.000 chars) |
| `Number` | Inteiros e decimais |
| `Date` | Datas sem hora |
| `Boolean` | Verdadeiro/Falso |
| `Email Address` | Validação automática de formato |
| `Phone Number` | Validação de formato de telefone |
| `Locale` | Configurações regionais |

---

## Data Sources

### Tipos de Data Source

| Tipo | Descrição |
|------|-----------|
| **Contact Data** | Dados nativos do Contact Builder |
| **Marketing Cloud Data** | Data Extensions do SFMC |
| **Synchronized** | Objetos sincronizados do Sales Cloud |
| **Mobile** | Dados de push/SMS |
| **External** | Fontes externas via API |

### Conectar Data Extension como Data Source

1. Contact Builder → **Data Sources** → **Create**
2. Selecionar **Marketing Cloud Data**
3. Escolher a Data Extension
4. Mapear campo chave → `ContactKey`
5. Selecionar campos a incluir

```
⚠️ A DE deve ter uma coluna mapeada ao ContactKey
⚠️ Alterações na DE refletem no Contact Builder automaticamente
```

---

## Relacionamentos (Relationships)

### Tipos de Relacionamento

| Tipo | Cardinalidade |
|------|---------------|
| **One-to-One** | Um contato → Um registro |
| **One-to-Many** | Um contato → Múltiplos registros |

### Criar Relacionamento

1. Contact Builder → **Data Designer** → **Link** entre Attribute Sets
2. Selecionar campo chave de cada lado
3. Definir tipo (1:1 ou 1:N)
4. Salvar e publicar

### Exemplo: Contato → Histórico de Compras

```
[Contact Record]
    ContactKey: "12345"
         |
         | (1:N via CustomerID)
         |
[Purchase History DE]
    CustomerID: "12345"
    OrderDate: 2026-01-15
    Amount: 150.00
```

---

## Population Management

### O que é Population
Uma **Population** agrupa contatos com base em um Attribute Set específico (ex: "Email Subscribers", "Mobile App Users").

### Configurar Population

1. Contact Builder → **Populations** → **Create**
2. Selecionar Attribute Set base
3. Definir filtros (opcional)
4. Publicar

### Population Padrão
- **All Contacts**: inclui todos os contatos de todas as origens
- Não pode ser excluída ou modificada

---

## Merge Rules

Define como resolver conflitos quando o mesmo contato existe em múltiplas fontes.

| Estratégia | Descrição |
|-----------|-----------|
| **Most Recent** | Usa o valor mais recente |
| **Oldest** | Usa o valor mais antigo |
| **Source Priority** | Prioridade configurável por fonte |

### Configurar Merge Rules
Contact Builder → **Administration** → **Merge Rules**

---

## AMPscript com Contact Builder

### Acessar Atributos do Contato

```ampscript
%%[
/* Acessar atributo do Contact Record */
SET @email = AttributeValue("EmailAddress")
SET @nome = AttributeValue("FirstName")
SET @customerID = AttributeValue("ContactKey")
]%%

Olá, %%=v(@nome)=%%!
Seu e-mail cadastrado: %%=v(@email)=%%
```

### Lookup em Attribute Set Personalizado

```ampscript
%%[
SET @rows = LookupRows("NomeDoAttributeSet", "ContactKey", _ContactKey)
SET @numRows = RowCount(@rows)

IF @numRows > 0 THEN
  SET @row = Row(@rows, 1)
  SET @valorCampo = Field(@row, "NomeCampo")
ENDIF
]%%
```

---

## SSJS com Contact Builder

### Inserir/Atualizar Contato via API

```javascript
<script runat="server">
Platform.Load("Core", "1");

var api = new Script.Util.WSProxy();

// Criar/Atualizar contato
var contact = {
  "ContactKey": "12345",
  "AttributeSets": [
    {
      "Name": "Email Addresses",
      "Items": [
        {
          "Values": [
            { "Name": "Email Address", "Value": "contato@exemplo.com" },
            { "Name": "HTML Email", "Value": true }
          ]
        }
      ]
    }
  ]
};

var result = api.create(
  "ContactData",
  contact,
  null,
  null
);

if (result.Status === "OK") {
  Write("Contato criado com sucesso");
} else {
  Write("Erro: " + Stringify(result));
}
</script>
```

### REST API — Upsert Contato

```javascript
// POST /contacts/v1/contacts
var payload = {
  "contactKey": "12345",
  "attributeSets": [
    {
      "name": "Email Addresses",
      "items": [
        {
          "values": [
            { "name": "Email Address", "value": "contato@exemplo.com" }
          ]
        }
      ]
    }
  ]
};
```

---

## Boas Práticas

```
✅ Defina o ContactKey antes de implementar qualquer canal
✅ Use um identificador estável (não e-mail, não nome)
✅ Documente todos os Attribute Sets e seus campos
✅ Mantenha relacionamentos simples — evite encadeamentos profundos
✅ Teste Merge Rules com dados reais antes de produção
✅ Audite contatos duplicados mensalmente via SQL no Contact Builder
✅ Use Synchronized DEs para dados CRM em vez de reimportar manualmente
```

---

## SQL para Auditoria de Contatos

```sql
-- Contatos duplicados por e-mail
SELECT
  EmailAddress,
  COUNT(ContactKey) AS TotalContacts
FROM _Subscribers
GROUP BY EmailAddress
HAVING COUNT(ContactKey) > 1
ORDER BY TotalContacts DESC

-- Contatos sem atividade nos últimos 180 dias
SELECT s.ContactKey, s.EmailAddress
FROM _Subscribers s
LEFT JOIN _Sent se ON s.ContactKey = se.SubscriberKey
WHERE se.EventDate >= DATEADD(DAY, -180, GETDATE())
  AND s.Status = 'Active'
```

---

## Referências

- [Contact Builder Docs](https://help.salesforce.com/s/articleView?id=sf.mc_co_contact_builder.htm)
- [API Contacts](https://developer.salesforce.com/docs/marketing/marketing-cloud/references/mc_contacts_contacts)
- [Attribute Sets Reference](https://help.salesforce.com/s/articleView?id=sf.mc_co_attribute_sets.htm)
