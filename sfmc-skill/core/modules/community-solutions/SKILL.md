---
name: sfmc-community-solutions
description: Community custom solutions and workarounds for non-native SFMC features.
---

# SFMC Community Solutions

Soluções customizadas da comunidade para recursos não-nativos do SFMC.

## Repositórios Principais

### SFMC-Cookbook
- **URL**: https://github.com/JoernBerkefeld/SFMC-Cookbook
- **Estrelas**: 130+
- **Descrição**: Best practices, AMPscript patterns, SSJS utilities, encryption examples
- **Use**: Referência geral para desenvolvimento

### email360/ssjs-lib
- **URL**: https://github.com/email360/ssjs-lib
- **Estrelas**: 54
- **Descrição**: Library com JWT, logging, WSProxy wrappers
- **Use**: Production-ready SSJS utilities

### mc-cloud-pages-tricks
- **URL**: https://github.com/MarketingThibs/mc-cloud-pages-tricks
- **Descrição**: CloudPages debug tools e tricks
- **Use**: Troubleshooting CloudPages

### sfmc-data-views
- **URL**: https://github.com/MarketingThibs/sfmc-data-views
- **Descrição**: Clone queries para DataViews
- **Use**: Reporting queries

### qrcode-sfmc-ssjs
- **URL**: https://github.com/jp-ed/qrcode-sfmc-ssjs
- **Descrição**: QR Code Generator via SSJS
- **Use**: Gerar QR codes em emails

### shorten-sfmc-links-ampscript-sms
- **URL**: https://github.com/MarketingThibs/shorten-sfmc-links-ampscript-sms
- **Descrição**: Personalized Bitly links para SMS
- **Use**: Link tracking em SMS

### sfmc-data-views-sql-schema
- **URL**: https://github.com/MateuszDabrowski/sfmc-data-views-sql-schema
- **Descrição**: Complete SQL schema for DataViews
- **Use**: Query reference

## Referências da Comunidade

### Blogs Técnicos
| Blog | URL | Conteúdo |
|------|-----|----------|
| Mateusz Dabrowski | mateuszdabrowski.pl | SQL, SSJS, Config |
| sfmarketing.cloud | sfmarketing.cloud | API, CloudPages, AMPscript |

### Ferramentas
| Tool | URL | Uso |
|------|-----|-----|
| ampscript.io | ampscript.io | Test AMPscript |
| dataviews.io | dataviews.io | Visual DataViews reference |

## Exemplos de Uso

### JWT Generation (email360/ssjs-lib)
```javascript
<script runat="server">
// Com a library
var jwt = email360.jwt.encode(payload, secret);
// Sem library - implementar manualmente
</script>
```

### QR Code
```javascript
<script runat="server">
// Usar library jp-ed/qrcode-sfmc-ssjs
var qrCode = QRCode.generate('https://example.com');
</script>
```

### Bitly Links (SMS)
```javascript
%%[
SET @longUrl = "https://example.com/page"
SET @shortUrl = BitlyShorten(@longUrl, @contactKey)
]%%
<a href="%%=v(@shortUrl)=%%">Link</a>
```

## Contribuidores Notáveis

| Dev | GitHub | Contribuições |
|-----|--------|----------------|
| Jörn Berkefeld | @JoernBerkefeld | SFMC-Cookbook, eslint-config-ssjs |
| Mateusz Dabrowski | @MateuszDabrowski | DataViews schema, blog |
| MarketingThibs | @MarketingThibs | CloudPages tricks, utilities |

## Como Usar

1. Clone/baixe o repositório
2. Analise se atende necessidade
3. Implemente parcialmente (não tudo)
4. Teste em sandbox antes de produção

## Aviso

- Libraries de terceiros não são suportadas pela Salesforce
- Revise código antes de usar em produção
- Mantenha cópias locais para segurança