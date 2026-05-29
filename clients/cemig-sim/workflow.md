# Cemig SIM - Modelo de Trabalho e Entregáveis do Projeto

Este documento especifica a estrutura do projeto e o fluxo de entregas técnicas offline para os projetos desenvolvidos para a Cemig SIM.

---

## 1. Diretrizes de Entregáveis Funcionais

Como os projetos do cliente funcionam offline (sem integração por API direta com a org do Marketing Cloud), adotamos uma estrutura de entrega híbrida:

1. **Documentos de Negócio e Ações Funcionais (MD na Raiz):**
   - **`analise_requisitos.md`**: Centraliza os requisitos funcionais, fluxogramas (Mermaid) e regras de negócios revisadas pelo cliente.
   - **`implementation_plan.md`**: Funciona como o mapa central da entrega. **Toda e qualquer ação funcional de dados e campos deve estar documentada aqui de forma detalhada e visível** (ex: estruturas de Data Extensions com tipos e chaves primárias, mapeamento de campos CRM $\rightarrow$ SFMC, lógicas dinâmicas de e-mails, etc.).

2. **Arquivos de Código Fonte (`/src`):**
   - Apenas arquivos de código-fonte de execução ficam na pasta `/src`:
     - Queries SQL: `/src/sql/`
     - Códigos HTML / AMPscript: `/src/emails/`
     - Estrutura JSON de DEs: `/src/data-extensions/`
     - Templates de WhatsApp: `/src/whatsapp/`
   - **Regra de Ouro:** Todos os arquivos de código criados dentro de `/src` devem ser **obrigatoriamente referenciados no `implementation_plan.md`** com links diretos clicáveis para que o cliente tenha fácil acesso a partir do documento principal.

---

## 2. Controle de Banco de Dados Offline

- A estrutura das tabelas de stage sincronizadas do CRM (ex: `Lead_Salesforce`) deve ser mantida offline dentro do arquivo `clients/cemig-sim/data-extensions.md`.
- **Controle de Atualização:** Toda vez que um novo campo do CRM for necessário, o Copilot deve alertar o usuário para atualizar essa tabela offline de stage.
- **Diferenciação de Data Extensions (Gerais vs. Específicas):** Apenas estruturas de dados globais e permanentes da organização (como as bases de stage sincronizadas do CRM) devem ficar registradas no arquivo geral `clients/cemig-sim/data-extensions.md`. Tabelas criadas especificamente para campanhas ou réguas de disparo (DEXs locais) devem ser mantidas na pasta `/src/data-extensions/` do projeto (em formato JSON) e descritas apenas no arquivo de documentação do respectivo projeto (`implementation_plan.md`).

---

## 3. Versionamento Git e Commits

- **Regra de Frequência de Commits:**
  - **Alterações na Skill:** Podem ser comitadas automaticamente para manter o controle das melhorias do assistente.
  - **Alterações no Projeto (Cruzeiro / src / raiz):** **NÃO** comitar automaticamente. Os arquivos devem ser apenas criados/salvos localmente. O Copilot deve aguardar o usuário solicitar explicitamente o commit antes de realizá-lo.
- **Formato da Mensagem de Commit:** Todo commit no Git deve seguir a marcação padrão acordada com o cliente:
  `- [Nome da alteração] + data (YYYY-MM-DD) + horário do commit`
- **Autorização:** Nenhuma alteração estrutural deve ser feita sem autorização expressa do usuário.

---

## 4. Diretrizes de Tomada de Decisão (Evitar Suposições)

- Conforme estabelecido nas **Diretrizes Globais** da Skill (ver [SKILL.md](../../SKILL.md)), o assistente não deve presumir regras de negócio locais não especificadas. 
- Em caso de dúvidas sobre mapeamentos, formatações de telefone ou novos requisitos do projeto Cemig SIM, o assistente deve paralisar a geração de código e formular perguntas diretas em português brasileiro para alinhamento prévio.

---

## 5. Diretrizes de Desenvolvimento de E-mails (HTML e AMPscript)

- **Proibição de Conteúdo Dinâmico Complexo:** Os e-mails não devem conter lógicas dinâmicas complexas de AMPscript para chaveamento de conteúdo inteiro (como `IF/ELSE` para carregar blocos, banners, imagens ou textos com base em parcerias).
- **Abordagem de Conteúdo Fixo:** As peças devem ser construídas no padrão normal de e-mail HTML estático, com todo o conteúdo e links de imagem inseridos diretamente no código-fonte.
- **Uso Limitado de AMPscript:** O uso de AMPscript fica restrito estritamente a variáveis de personalização individuais dos contatos, como a extração e tratamento do nome (`NomeCompleto`) e o link dinâmico de destino do CTA (`LinkAssinatura`).
- **Nomenclatura Padrão de Arquivos de E-mail:** Todos os arquivos HTML de e-mail criados no repositório devem seguir obrigatoriamente a estrutura descritiva do contexto do disparo:
  `{campanha}_{etapa_jornada}_{segmentacao_publico}_{regua_dia}_{tipo_peca}.html`
  - *Exemplo:* `aquecimento_cruzeiro_prospeccao_socio5estrelas_d05_oferta.html`
- **Hierarquia de Títulos (H3 Centralizado):** Todo e-mail contendo título de reforço ou fechamento antes do CTA principal deve usar a tag `<h3>` com tamanho de `20px` (negrito), cor verde Cemig SIM (`#00aa9a`), line-height de `1.4` e alinhamento **centralizado** (no estilo do H3 e no alinhamento da `<td>` que o envolve com `align="center"`).
- **Cards de Benefícios e Responsividade:** Peças com mais de uma oferta de destaque devem adotar o layout de cartões paralelos em desktop (largura de `250px`, fundo `#f0f8f7`, borda superior `#00aa9a` de 4px) com empilhamento vertical centralizado no mobile usando a classe responsiva `.mobile-card` (`280px` de largura).
- **Consistência em Réguas de Disparo:** Toda alteração de design de bloco (como os cartões de benefícios ou alinhamento de reforço) deve ser replicada de forma uniforme em todas as peças que compartilham essa seção na régua de disparos da mesma campanha.
- **Uso de Emojis em Ícones:** Utilizar emojis de sistema (ex: ⚡, 💳, 🎁, ✅) nos cards de benefícios e tópicos de vantagens em vez de imagens hospedadas externas, prevenindo que o layout perca legibilidade se o cliente de e-mail bloquear imagens externas.

