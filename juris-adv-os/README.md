# Juris Adv-OS

Plugin Claude Code / Cowork para **buscar + validar + auditar
jurisprudencia** com rastreabilidade real.

> Anti-halucinacao por design: nenhuma citacao recebe selo de validada
> sem que o link da fonte seja conferido por fetch real.

---

## O que faz

- 🔎 **Busca** jurisprudencia em portais oficiais (STF, STJ, TST, TRFs,
  TJs) e agregadores (JusBrasil, Escavador), priorizando fontes oficiais.
- ✅ **Valida** automaticamente cada citacao via `WebFetch` na URL
  declarada — confirma se o numero do processo e o trecho da ementa
  efetivamente aparecem na pagina. Status por citacao:
  ✅ VALIDADA / ⚠️ PARCIAL / 🔴 NAO VALIDADA.
- 🎯 **Audita** o nexo causal entre cada jurisprudencia e a sua tese
  antes de citar em peca. Saida estruturada: alinhamento PRO / CONTRA /
  NEUTRO / DEPENDE, risco, distincoes relevantes.
- 📄 **Formata** citacoes aprovadas em ABNT (NBR 6023/10520) e em bloco
  pronto-pra-peca.

---

## Como usar

Apos instalar:

```
/juris responsabilidade civil do banco por fraude no PIX nao autorizado
```

O Claude vai:
1. Buscar em STJ, TJs, agregadores
2. Validar cada link automaticamente
3. Oferecer auditoria pre-envio (nexo a sua tese)
4. Formatar as aprovadas em ABNT + bloco pra peca

---

## Como instalar

### Via UI Cowork (recomendado)

1. Cowork -> Settings -> Plugins -> Pessoal -> "+" -> Adicionar marketplace
2. Cola a URL deste repositorio:
   ```
   https://github.com/sbroggioadv/juris-adv-os-marketplace
   ```
3. Sincronizar -> Instalar -> rodar `/juris` no Claude Code.

### Via CLI

```bash
claude plugin marketplace add https://github.com/sbroggioadv/juris-adv-os-marketplace
claude plugin install juris-adv-os@juris-adv-os-marketplace
```

---

## Skills disponiveis (4)

| Skill | Funcao |
|-------|--------|
| `buscar-jurisprudencia` | Busca em portais oficiais + agregadores |
| `validar-jurisprudencia` | WebFetch real em cada URL declarada |
| `auditoria-pre-envio` | Checa nexo entre jurisprudencia e tese do usuario |
| `formatar-citacao` | ABNT + bloco pronto-pra-peca |

---

## Filosofia

Plugin enxuto, instalou-e-usou. Sem orquestrador, sem onboarding.

Funciona com **stock Claude Code** (`WebSearch` + `WebFetch` nativos).
Se voce tiver `firecrawl` ou `perplexity` MCPs configurados, o plugin
os usa como fallback automatico — mas eles sao **opcionais**, nunca
obrigatorios.

---

## ⚠️ Aviso legal

Validacao automatica e **assistida por IA**. A conferencia humana
final, especialmente do inteiro teor e da pertinencia ao caso concreto,
e responsabilidade exclusiva do advogado antes de protocolar.

---

## Licenca

MIT — ver [LICENSE](./LICENSE).

---

**Familia Adv-OS** — plugins juridicos modulares para Claude Code / Cowork.

Outros plugins da familia (instalaveis separadamente):
[ia-combativa-adv-os](https://github.com/sbroggioadv/ia-combativa-adv-os-marketplace) ·
[marketing-adv-os](https://github.com/sbroggioadv/marketing-adv-os-marketplace) ·
[previdenciario-adv-os](https://github.com/sbroggioadv/previdenciario-adv-os-marketplace) ·
[trabalhista-adv-os](https://github.com/sbroggioadv/trabalhista-adv-os-marketplace) ·
[tributario-societario-adv-os](https://github.com/sbroggioadv/tributario-societario-adv-os-marketplace) ·
[auditoria-contabil-os](https://github.com/sbroggioadv/auditoria-contabil-os-marketplace) ·
[licitacoes-adv-os](https://github.com/sbroggioadv/licitacoes-adv-os-marketplace) ·
[direito-medico-adv-os](https://github.com/sbroggioadv/direito-medico-adv-os-marketplace)
