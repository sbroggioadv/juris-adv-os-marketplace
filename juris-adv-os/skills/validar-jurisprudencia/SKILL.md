---
name: validar-jurisprudencia
description: >
  VALIDAR-JURISPRUDENCIA — Confirma cada citacao retornada por
  buscar-jurisprudencia via WebFetch real na URL declarada. Verifica se a
  pagina existe, se contem o numero do processo, e se a ementa transcrita
  efetivamente aparece no texto. Emite status ✅ VALIDADA | ⚠️ PARCIAL |
  🔴 NAO VALIDADA. NUNCA emite ✅ sem fetch bem-sucedido. Auto-disparada
  apos buscar-jurisprudencia. Use tambem manualmente para validar
  citacoes que o usuario trouxe de outras fontes ou pediu para checar.
---

# VALIDAR-JURISPRUDENCIA — Confirmacao Anti-Halucinacao via Fetch Real

## 1. PRINCIPIO INVIOLAVEL

**Nenhuma citacao recebe ✅ sem que o WebFetch na URL retorne sucesso E o
trecho de ementa transcrito apareca de fato na pagina.**

Este e o coracao do plugin. Se voce burlar isso, voce destrui a
proposta de valor inteira e a confianca do advogado-usuario. Nunca
fingir validacao.

---

## 2. WORKFLOW DE VALIDACAO

Para CADA citacao recebida da lista da `buscar-jurisprudencia` (ou da
lista que o usuario pediu para validar):

### Passo 1 — WebFetch da URL declarada

```
WebFetch(url="<URL_DA_CITACAO>")
```

Capture:
- HTTP status (200 / 404 / 403 / outros)
- Conteudo retornado (texto da pagina)
- Eventuais redirecionamentos

### Passo 2 — Checar 3 evidencias na pagina

| Evidencia | Verificacao |
|-----------|-------------|
| **(a) Numero do processo** | O numero declarado aparece literal no texto retornado? |
| **(b) Ementa transcrita** | O trecho de ementa transcrito (3-8 linhas) aparece literal ou com diferencas minimas (acentos/quebra de linha)? |
| **(c) Metadados** | Orgao julgador, relator e data batem com o que esta na pagina? |

### Passo 3 — Atribuir status

| Status | Quando emitir |
|--------|---------------|
| ✅ **VALIDADA** | Fetch retornou 200 + numero do processo presente + trecho de ementa presente (literal ou ~95%) + metadados batem. |
| ⚠️ **PARCIAL** | Fetch retornou 200 mas: numero presente porem ementa parafraseada / metadados divergem em 1 campo / pagina e de agregador (JusBrasil/Escavador). |
| 🔴 **NAO VALIDADA** | Fetch falhou (404/403/timeout/etc) OU numero do processo nao aparece na pagina OU trecho de ementa nao aparece. |

**Regra de bloqueio:** **NUNCA** emita ✅ se algum dos 3 itens (a/b/c) falhar. Em duvida, rebaixa pra ⚠️.

### Passo 4 — Caso especial: agregador com link pro tribunal

Se a fonte original era AGREGADOR (JusBrasil/Escavador) e o agregador
tem link "Ver no [tribunal]" ou link pra inteiro teor oficial:
1. Faca um segundo WebFetch nesse link oficial.
2. Se confirmar a citacao na fonte oficial, emita ✅ + observacao
   `confirmado tambem na fonte oficial: <URL>`.
3. Se nao confirmar, emita ⚠️ `agregador divergente do oficial`.

### Passo 5 — Saida estruturada

Para cada citacao, produza:

```markdown
### [N] [Citacao original — Tribunal · numero]

- **Status:** ✅ VALIDADA | ⚠️ PARCIAL | 🔴 NAO VALIDADA
- **Fonte declarada:** <URL>
- **Fetch:** HTTP <status>
- **Numero do processo presente na pagina:** sim | nao
- **Ementa transcrita presente na pagina:** sim | parafraseada | nao
- **Metadados batem:** sim | divergencia em [campos]
- **Observacao:** [se houver — ex: "agregador, conferir no tribunal", "redirect para nova URL", "ementa atualizada apos publicacao", etc]
- **URL alternativa (se descoberta):** [se aplicavel]
```

### Passo 6 — Relatorio final

Apos validar TODAS as citacoes:

```markdown
## Relatorio de validacao — [N] citacoes

| # | Citacao | Status | Acao recomendada |
|---|---------|--------|------------------|
| 1 | [Tribunal · numero] | ✅ | Pronta para citar |
| 2 | [Tribunal · numero] | ⚠️ | Conferir manualmente em <URL alternativa> |
| 3 | [Tribunal · numero] | 🔴 | NAO CITAR sem validacao manual — extraido de <fonte original> |
| ... | | | |

**Resumo:** ✅ N | ⚠️ N | 🔴 N

**Bloqueios criticos:** [lista de citacoes 🔴 que NAO devem ir pra peca sem validacao humana]
```

---

## 3. PROIBICOES ABSOLUTAS

1. **NUNCA** emitir ✅ sem fetch bem-sucedido com numero + ementa confirmados.
2. **NUNCA** "completar" uma citacao com informacoes que voce nao viu na pagina.
3. **NUNCA** silenciar um 🔴 — sinalize destacado no relatorio.
4. **NUNCA** afirmar "validada via google" ou "validada via memoria do modelo" — so via WebFetch real.
5. Se o fetch retornar a pagina mas em formato impraticavel (JS-heavy, captcha), emita ⚠️ `pagina exige render JS — validacao manual recomendada` em vez de chutar ✅.

---

## 4. FALLBACK COM MCPs OPCIONAIS

Se o usuario tiver **firecrawl** MCP configurado, voce PODE usar como
ferramenta de fallback mais robusta:

```
mcp__firecrawl__firecrawl_scrape(url="<URL>", formats=["markdown"])
```

(Util quando WebFetch nativo falha por anti-bot ou JS-heavy.)

Se o usuario tiver **perplexity** MCP, voce PODE usar pra confirmar
citacao via second-opinion com citations.

**Estes sao OPCIONAIS** — o plugin funciona stock com WebFetch nativo.
Se MCPs nao estiverem disponiveis, NAO falhe — apenas use WebFetch.

---

## 5. CONEXAO COM A PROXIMA SKILL

Apos emitir o relatorio, sinalize ao usuario:

> 🔍 Para auditoria completa antes de citar em peca (inclui alinhamento
> a sua tese), rode a skill `auditoria-pre-envio`.

Nao auto-dispara — auditoria-pre-envio precisa do contexto da peca/tese
do usuario, que ele submete ativamente.
