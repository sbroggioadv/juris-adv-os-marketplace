# CLAUDE.md — plugin-juris-adv (interno do source)

> Regras internas do source `plugin-juris-adv/`. NAO copia pro marketplace
> publico (marketplace usa README.md). Plugin alvo: `juris-adv-os`.

---

## Identidade

- **Plugin name (slug):** `juris-adv-os`
- **Marketplace publico:** `https://github.com/sbroggioadv/juris-adv-os-marketplace`
- **Source privado:** `https://github.com/sbroggioadv/juris-adv-os`
- **Familia:** Adv-OS
- **Modelo comercial:** ORDER BUMP Kirvano — R$ 29,80
- **Audience:** advogados que querem buscar + validar jurisprudencia com
  rastreabilidade real (anti-halucinacao por design).

---

## Filosofia do plugin

**Anti-halucinacao por design.** O diferencial deste plugin nao e
"buscar mais rapido" — e **nao mentir**. Nenhuma citacao recebe selo
✅ VALIDADA sem que o WebFetch na URL declarada retorne sucesso E o
trecho da ementa transcrita apareca na pagina.

Se voce, futuro Claude editando este plugin, sentir tentacao de "facilitar"
emitindo ✅ sem fetch real, **PARE.** Isso destruiria o valor do plugin
inteiro. A promessa comercial e justamente: cada citacao validada tem
link conferido por fetch.

---

## Skills (4 — sem orquestrador)

| Skill | Funcao | Auto-chain? |
|-------|--------|-------------|
| `buscar-jurisprudencia` | Busca em portais oficiais + agregadores | dispara `validar` ao final |
| `validar-jurisprudencia` | WebFetch real em cada URL, status ✅/⚠️/🔴 | auto-disparada |
| `auditoria-pre-envio` | 3D: inteiro teor + link + nexo a tese | manual (pede contexto da peca) |
| `formatar-citacao` | ABNT + bloco-pra-peca | manual |

Sem onboarding. Sem firm-master. Sem orquestrador. **Instalou, rodou
`/juris`, funcionou.**

---

## Commands

- `/juris <tese>` — entry point. Dispara a cadeia.

---

## Hooks

- `SessionStart` — apenas anuncia disponibilidade (echo). Sem injecao de
  contexto. Mantem o plugin enxuto.

---

## Padroes de codigo (deste plugin)

1. **Stock first.** WebSearch + WebFetch nativos sao o backbone.
   Firecrawl/Perplexity MCPs sao FALLBACK opcional, nunca dependency.
2. **Sem orquestracao.** Cada skill se basta. Auto-chain so onde faz
   sentido (busca -> valida).
3. **Saida estruturada.** Toda skill produz saida em markdown com tabelas
   ou blocos claros. Nada de "achei que..." vibe.
4. **Aviso legal sempre presente** em `auditoria-pre-envio` e
   `validar-jurisprudencia` — "validacao assistida por IA, conferencia
   humana antes de protocolar e obrigatoria".

---

## Constantes operacionais

| Constante | Valor |
|-----------|-------|
| Max citacoes por busca | 10 |
| Max queries WebSearch por busca | 6 |
| Confianca pra ✅ | fetch 200 + numero processo + trecho ementa, todos presentes |
| Rebaixa pra ⚠️ | qualquer divergencia parcial |
| Bloqueia (🔴) | fetch falhou OU pagina nao tem o numero do processo |

---

## NAO duplicar com plugin-mae

A skill `jurisprudencia-estrategica` do plugin-mae (`ia-combativa-adv-os`)
trata de **INTEGRACAO** estrategica da juris na peca (hierarquizar,
contextualizar, argumentar). Este plugin trata de **BUSCA + VALIDACAO**
da front-line da pesquisa. Sao complementares — nao duplicar funcoes.

---

## Roadmap (pos-v0.1.0, se demanda surgir)

- `monitorar-jurisprudencia` — schedule semanal pra checar julgados novos
  sobre temas que o advogado segue
- `comparar-julgados` — diff entre duas decisoes pro mesmo tema
- `gerar-quadro-comparativo` — tabela de jurisprudencia pro/contra

Nao implementar sem demanda real. Manter enxuto.
