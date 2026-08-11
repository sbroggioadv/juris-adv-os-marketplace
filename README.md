# Juris Adv-OS — Marketplace

> ## ⚖️ Este repositório NÃO é software livre
>
> O código fica visível para viabilizar a instalação no Claude/Cowork — não porque seja gratuito.
>
> **JURIS ADV-OS — R$ 29,80, pagamento único** (sem assinatura, sem recorrência)
> 👉 **[Adquirir a licença](https://pay.kirvano.com/bf79f472-0a7a-41ba-b54c-b271a5f7cbeb)**
>
> **Ao forkar ou clonar este repositório você adere à [licença de uso](LICENSE)**, devendo efetuar o
> pagamento no link acima e enviar o comprovante para **luis@sbroggio.com.br**.
>
> Os forks são públicos no GitHub e são registrados pelo titular (data, conta e repositório).
>
> **Já comprou?** Nada a fazer — sua licença cobre o uso e o fork para instalação. Este aviso vale de
> 11/08/2026 em diante, para quem chega ao repositório sem ter adquirido.


Marketplace oficial do plugin **Juris-Adv-OS** para Claude Code / Cowork.

> Buscador e validador de jurisprudencia, com auditoria de nexo causal
> antes do uso em peca. Anti-halucinacao por design.

---

## O que tem aqui

- `.claude-plugin/marketplace.json` — manifesto do marketplace
- `juris-adv-os/` — codigo-fonte completo do plugin
- `LICENSE` — licenca de uso proprietaria

---

## Como instalar (Cowork)

1. Cowork -> Settings -> Plugins -> Pessoal -> "+" -> Adicionar marketplace
2. Cola:
   ```
   https://github.com/sbroggioadv/juris-adv-os-marketplace
   ```
3. Sincronizar -> Instalar
4. No Claude Code, rode `/juris <sua tese>` pra testar

## Como instalar (CLI)

```bash
claude plugin marketplace add https://github.com/sbroggioadv/juris-adv-os-marketplace
claude plugin install juris-adv-os@juris-adv-os-marketplace
```

---

## O que o plugin entrega

- **`/juris <tese>`** — dispara a cadeia completa: busca -> validacao -> oferta de auditoria -> oferta de formatacao
- **4 skills:**
  - `buscar-jurisprudencia` — portais oficiais (STF, STJ, TST, TRFs, TJs) + agregadores
  - `validar-jurisprudencia` — WebFetch real em cada URL, status ✅/⚠️/🔴
  - `auditoria-pre-envio` — checa nexo entre cada jurisprudencia e a tese do usuario
  - `formatar-citacao` — ABNT (NBR 6023/10520) + bloco pronto-pra-peca

---

## Filosofia

**Stock-first:** funciona com `WebSearch` + `WebFetch` nativos do Claude
Code. Se voce tiver `firecrawl` ou `perplexity` MCPs, sao usados como
fallback automatico (opcional, nunca obrigatorio).

**Sem orquestrador, sem onboarding:** instalou, rodou `/juris`,
funcionou. Cada skill se basta.

**Anti-halucinacao por design:** nenhuma citacao recebe ✅ sem que o
WebFetch confirme numero do processo + ementa na pagina.

---

## ⚠️ Aviso legal

Validacao automatica e **assistida por IA**. A conferencia humana final
e responsabilidade exclusiva do advogado antes de protocolar.

---

## Licenca

Uso licenciado mediante aquisição — ver [`LICENSE`](./LICENSE). As cópias obtidas até 11/08/2026 permanecem sob MIT; a partir dessa data o código é proprietário.

---

## Familia Adv-OS

[ia-combativa-adv-os](https://github.com/sbroggioadv/ia-combativa-adv-os-marketplace) ·
[marketing-adv-os](https://github.com/sbroggioadv/marketing-adv-os-marketplace) ·
[previdenciario-adv-os](https://github.com/sbroggioadv/previdenciario-adv-os-marketplace) ·
[trabalhista-adv-os](https://github.com/sbroggioadv/trabalhista-adv-os-marketplace) ·
[tributario-societario-adv-os](https://github.com/sbroggioadv/tributario-societario-adv-os-marketplace) ·
[auditoria-contabil-os](https://github.com/sbroggioadv/auditoria-contabil-os-marketplace) ·
[licitacoes-adv-os](https://github.com/sbroggioadv/licitacoes-adv-os-marketplace) ·
[direito-medico-adv-os](https://github.com/sbroggioadv/direito-medico-adv-os-marketplace)

---

**Criado por Sbroggio Advocacia & IA Combativa**
