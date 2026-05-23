---
name: sfmc-ampscript-encryption
description: AmpScript encryption and decryption functions.
---

# AmpScript - Encryption Functions

## Funções de Criptografia

### EncryptSymmetric
Criptografa usando chave simétrica.

```ampscript
EncryptSymmetric(Value, Algorithm, SecretKey, Salt, IV)
```

**Algoritmos**: "AES", "DES", "TripleDES"

### DecryptSymmetric
Descriptografa.

```ampscript
DecryptSymmetric(Value, Algorithm, SecretKey, Salt, IV)
```

### Encrypt
Criptografa (legado, usar EncryptSymmetric).

### Decrypt
Descriptografa (legado, usar DecryptSymmetric).

### Base64Encode
Codifica para Base64.

```ampscript
Base64Encode(Value)
```

### Base64Decode
Decodifica Base64.

```ampscript
Base64Decode(Value)
```

## Exemplos

```ampscript
%%[
  SET @secretKey = "my-secret-key-12345"
  SET @encrypted = EncryptSymmetric(@plainText, "AES", @secretKey, "", "")
  SET @decrypted = DecryptSymmetric(@encrypted, "AES", @secretKey, "", "")
]%%
```

```ampscript
%%[
  SET @encoded = Base64Encode(@data)
  SET @decoded = Base64Decode(@encoded)
]%%
```

## Casos de Uso

- Ofuscar IDs em URLs
- Proteger dados sensíveis
- Integrar com sistemas externos criptografados