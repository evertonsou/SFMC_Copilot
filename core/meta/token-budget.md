# Token Budget Guide

Guia de consumo de tokens para otimização da skill SFMC Copilot.

## Carregamento Inicial

| Componente | Tokens Estimados |
|------------|------------------|
| SKILL.md raiz | ~150 |
| Sistema base | ~200 |
| **Total inicial** | **~350 tokens** |

## Módulos - Tamanhos

| Módulo | Tamanho Médio | Tamanho Máximo |
|--------|--------------|----------------|
| AMPscript | ~3.000 | ~4.500 |
| SQL | ~2.000 | ~2.500 |
| DataViews | ~2.500 | ~3.000 |
| HTML Email | ~4.000 | ~5.000 |
| SSJS | ~2.500 | ~3.000 |
| CloudPages | ~3.000 | ~4.000 |
| API Integration | ~5.000 | ~6.000 |
| Journey Builder | ~2.000 | ~2.500 |
| Automation Studio | ~1.500 | ~2.000 |
| Content Builder | ~1.000 | ~1.500 |
| Data Extension | ~1.500 | ~2.000 |
| Community Solutions | ~1.500 | ~2.000 |
| Copy/CTAs/KPIs | ~1.500 | ~2.000 |

## Cenários de Uso

### Cenário 1: Tarefa Simples
**Prompt**: "Como usar LookupRows?"

```
SKILL.md raiz: ~150 tokens
Módulo AMPscript: ~2.000 tokens
Categoria data-extension: ~500 tokens
Total: ~2.650 tokens
```

### Cenário 2: Módulo Completo
**Prompt**: "Preciso criar um email completo do zero"

```
SKILL.md raiz: ~150 tokens
Módulo AMPscript: ~3.000 tokens
Módulo HTML Email: ~4.000 tokens
Módulo DataViews: ~1.000 tokens (referência)
Total: ~8.150 tokens
```

### Cenário 3: Múltiplos Módulos
**Prompt**: "Crie uma landing page com formulário que grave em DE"

```
SKILL.md raiz: ~150 tokens
Módulo CloudPages: ~3.000 tokens
Módulo Data Extension: ~1.000 tokens
Módulo API Integration: ~1.500 tokens
Total: ~5.650 tokens
```

### Cenário 4: Sessão Completa (Full Knowledge)
**Prompt**: "Preciso de tudo sobre SFMC"

```
SKILL.md raiz: ~150 tokens
Todos os módulos: ~35.000 tokens
Design System: ~5.000 tokens
Total: ~40.000 tokens
```

## Estratégias de Otimização

### 1. Carregamento Sob Demanda
Carregue apenas módulos necessários:
```
"Load ampscript module" vs "Load everything"
```

### 2. Sub-módulos Específicos
Carregue categorias específicas quando possível:
```
"Load ampscript/data-extension" vs "Load ampscript"
```

### 3. Design System por Cliente
Carregue apenas o design system ativo:
```json
"active": "_default"  // Não carrega todos os design systems
```

### 4. Code Snippets em Arquivos Separados
Mantenha exemplos em `examples/` ao invés de inline no SKILL.md

## Estimativa por Sessão

| Tipo de Sessão | Tokens Estimados |
|----------------|------------------|
| Quick question | ~500 - 1.000 |
| Single module | ~2.000 - 5.000 |
| Multi-module | ~5.000 - 15.000 |
| Full knowledge | ~35.000 - 40.000 |

## Recomendações

1. **Prefira módulos específicos** para tarefas simples
2. **Agrupe módulos relacionados** para tarefas complexas
3. **Carregue Design System** apenas quando necessário
4. **Use sub-módulos** quando a tarefa é bem definida
5. **Evite carregar tudo** a menos que necessário