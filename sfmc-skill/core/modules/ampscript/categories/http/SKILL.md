---
name: sfmc-ampscript-http
description: AmpScript HTTP request functions.
---

# AmpScript - HTTP Functions

## Funções HTTP

### HTTPGet
Faz requisição GET.

```ampscript
HTTPGet(URL)
HTTPGet(URL, 0, 0)  // com timeout e redirect flags
```

### HTTPPost
Faz requisição POST.

```ampscript
HTTPPost(URL, ContentType, Payload)
HTTPPost(URL, ContentType, Payload, HeaderName, HeaderValue)
```

### HTTPGetHeader
Obtém header da resposta.

```ampscript
HTTPGetHeader(HeaderName)
```

### RedirectTo
Redireciona para URL (usado em links).

```ampscript
RedirectTo(URL)
```

### CloudPagesURL
Gera URL tracking para CloudPages.

```ampscript
CloudPagesURL(PageID)
```

## Tratamento de Erros

```ampscript
%%[
  SET @url = "https://api.example.com/data"
  SET @response = HTTPGet(@url)
  
  IF EMPTY(@response) THEN
    SET @error = "Failed to retrieve data"
  ENDIF
]%%
```

## Exemplos

### GET Request
```ampscript
%%[
  SET @url = "https://api.example.com/customer/"
  SET @url = Concat(@url, @customerId)
  SET @response = HTTPGet(@url)
]%%
```

### POST Request
```ampscript
%%[
  SET @url = "https://api.example.com/webhook"
  SET @payload = Concat('{"email":"', @email, '"}')
  SET @response = HTTPPost(@url, "application/json", @payload)
]%%
```

### Com Headers
```ampscript
%%[
  SET @url = "https://api.example.com/secure"
  SET @authHeader = Concat("Bearer ", @token)
  SET @response = HTTPPost(@url, "application/json", @payload, "Authorization", @authHeader)
]%%
```

## Limitações

- Timeout: ~30 segundos
- Não suporta HTTPS com autenticação complexa
- Para APIs REST completas, considere SSJS com `HTTP.GlobalHeader`