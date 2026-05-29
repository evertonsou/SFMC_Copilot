# Module: QR Code SFMC SSJS

> **Skill**: sfmc-copilot | **Categoria**: Community Solutions | **Tokens**: ~1K  
> **Repositório**: [qrcode-sfmc-ssjs](https://github.com/rafaelramirez/qrcode-sfmc-ssjs) ⭐ 10

---

## Visão Geral

**qrcode-sfmc-ssjs** é uma solução para **gerar QR Codes diretamente em CloudPages ou e-mails** do Salesforce Marketing Cloud usando SSJS, sem necessidade de serviços externos.

---

## Casos de Uso

```
✅ QR Code único por subscriber (cupom, ticket, convite)
✅ QR Code com URL personalizada rastreável (UTM)
✅ Ingresso digital gerado por e-mail
✅ Código de resgate para campanhas de campo
✅ Link para pesquisa ou landing page customizada
✅ Código de voucher com validade
```

---

## Instalação

### Usando API de QR Code (Abordagem Recomendada)

A forma mais simples é usar uma **API externa de geração de QR Code** com URL dinâmica:

```ampscript
%%[
/* Gerar URL com dados do subscriber para o QR */
SET @subKey  = _subscriberkey
SET @email   = emailaddr
SET @token   = CONCAT(@subKey, "-", Format(Now(), "yyyyMMddHHmm"))

/* URL de destino do QR Code */
SET @destURL = CONCAT(
  "https://seusite.com/validar?",
  "token=", EncodeURL(@token),
  "&email=", EncodeURL(@email)
)

/* URL da API de QR Code — Google Charts (gratuito) */
SET @qrURL = CONCAT(
  "https://chart.googleapis.com/chart?",
  "cht=qr",
  "&chs=300x300",
  "&chl=", EncodeURL(@destURL),
  "&choe=UTF-8",
  "&chld=M|4"
)
]%%

<img src="%%=v(@qrURL)=%%" 
     width="200" 
     height="200" 
     alt="QR Code — escaneie para acessar"
     style="display:block;">
```

---

## Biblioteca SSJS para QR Code (qrcode-sfmc-ssjs)

### Configuração

```javascript
<script runat="server">
Platform.Load("Core", "1");

// 1. Copiar código da biblioteca do GitHub para Script Asset
// 2. Referenciar via CloudPagesURL ou carregar inline

// A lib gera QR Code como SVG ou canvas (data URI)
var qr = QRCode({
  version: 4,
  errorCorrectionLevel: "M",
  mode: "Byte"
});

qr.addData("https://seusite.com/voucher?code=ABC123");
qr.make();

// Obter como SVG
var svg = qr.createSvgTag({ scalable: true });
Variable.SetValue("@qrSVG", svg);
</script>

%%=v(@qrSVG)=%%
```

---

## QR Code Personalizado por Subscriber

### AMPscript Completo para E-mail

```ampscript
%%[
/* Gerar QR Code personalizado por subscriber */

/* 1. Dados do subscriber */
SET @subKey   = _subscriberkey
SET @nome     = AttributeValue("FirstName")
SET @voucher  = CONCAT("VCH-", SUBSTRING(@subKey, 1, 8))

/* 2. URL de destino com rastreamento */
SET @destURL = CONCAT(
  "https://loja.exemplo.com/voucher",
  "?code=",    EncodeURL(@voucher),
  "&sub=",     EncodeURL(@subKey),
  "&utm_source=email",
  "&utm_medium=qrcode",
  "&utm_campaign=blackfriday2026"
)

/* 3. QR Code via API (300x300px, nível de correção M) */
SET @qrAPI = "https://api.qrserver.com/v1/create-qr-code/"
SET @qrURL = CONCAT(
  @qrAPI,
  "?size=300x300",
  "&data=", EncodeURL(@destURL),
  "&format=png",
  "&ecc=M",
  "&color=000000",
  "&bgcolor=ffffff"
)
]%%

<table width="100%" cellpadding="0" cellspacing="0">
  <tr>
    <td align="center" style="padding: 20px;">
      <h2>Seu Voucher Exclusivo</h2>
      <p>Código: <strong>%%=v(@voucher)=%%</strong></p>
      <img src="%%=v(@qrURL)=%%" 
           width="200" height="200"
           alt="QR Code %%=v(@voucher)=%%"
           style="border: 2px solid #eee; border-radius: 8px;">
      <p style="font-size: 12px; color: #999;">
        Escaneie para resgatar | Válido até 31/12/2026
      </p>
    </td>
  </tr>
</table>
```

---

## APIs de QR Code Gratuitas

| API | Limite Gratuito | Formatos |
|-----|-----------------|---------|
| **api.qrserver.com** | Ilimitado | PNG, SVG, EPS |
| **chart.googleapis.com** | Sem limite declarado | PNG |
| **quickchart.io** | 10k/mês free | PNG, SVG |
| **goqr.me API** | 1M/dia free | PNG, SVG |

### Exemplo com api.qrserver.com (Mais Completo)

```
https://api.qrserver.com/v1/create-qr-code/
  ?size=300x300          → Tamanho em pixels
  &data=ENCODED_URL      → Dados (URL encode obrigatório)
  &format=png            → Formato: png, svg, eps
  &ecc=M                 → Error correction: L, M, Q, H
  &color=1a1a2e          → Cor do QR (hex sem #)
  &bgcolor=ffffff        → Cor do fundo
  &margin=10             → Margem em pixels
  &qzone=1               → Zona quieta em módulos
```

---

## QR Code em CloudPage (Formulário de Evento)

```ampscript
%%[
/* Gerar ticket digital com QR Code após inscrição */
SET @nome     = RequestParameter("nome")
SET @email    = RequestParameter("email")
SET @evento   = "Conferencia_Tech_2026"
SET @ticketID = CONCAT("TKT-", Format(Now(), "yyyyMMddHHmmss"))

IF NOT Empty(@nome) AND NOT Empty(@email) THEN
  /* Salvar inscrição */
  SET @saved = UpsertData(
    "DE_EventRegistrations",
    1, "Email", @email,
    "Nome", @nome,
    "TicketID", @ticketID,
    "Evento", @evento,
    "DataInscricao", Now()
  )
  
  /* Gerar URL do ticket */
  SET @ticketURL = CONCAT(
    "https://evento.empresa.com/check-in?",
    "ticket=", EncodeURL(@ticketID),
    "&email=", EncodeURL(@email)
  )
  
  /* URL do QR Code */
  SET @qrURL = CONCAT(
    "https://api.qrserver.com/v1/create-qr-code/",
    "?size=250x250&data=", EncodeURL(@ticketURL)
  )
  
  SET @sucesso = "true"
ENDIF
]%%

%%[ IF @sucesso == "true" THEN ]%%
<h1>✅ Inscrição Confirmada!</h1>
<p>Seu ingresso: <strong>%%=v(@ticketID)=%%</strong></p>
<img src="%%=v(@qrURL)=%%" width="200" alt="QR Code do seu ingresso">
<p><small>Apresente este QR Code na entrada do evento</small></p>
%%[ ENDIF ]%%
```

---

## Boas Práticas

```
✅ Use EncodeURL() em todos os dados embutidos no QR
✅ Teste em 3+ leitores de QR Code antes de publicar
✅ QR Code mínimo: 150x150px para leitura confiável
✅ Nível de correção M ou H para maior robustez
✅ Contraste mínimo de 4:1 entre QR e fundo
✅ Sempre inclua texto alternativo (código) além do QR
✅ Para rastreamento: use UTM params na URL de destino
✅ Log de resgates na DE para auditoria
```

---

## Links

- **GitHub Original**: https://github.com/rafaelramirez/qrcode-sfmc-ssjs
- **api.qrserver.com**: https://goqr.me/api/
- **quickchart.io**: https://quickchart.io/qr
