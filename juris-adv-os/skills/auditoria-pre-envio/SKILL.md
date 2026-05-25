---
name: auditoria-pre-envio
description: >
  AUDITORIA-PRE-ENVIO — Auditoria 3D obrigatoria antes do advogado citar
  jurisprudencia em qualquer peca, contestacao, parecer ou e-mail.
  Verifica (1) se o inteiro teor foi efetivamente analisado, (2) se cada
  link da fonte foi confirmado por fetch real, e (3) se o entendimento
  da jurisprudencia tem nexo com a tese/pedido do usuario. Emite veredito
  por citacao: APROVADA / REVISAR / BLOQUEADA. Use SEMPRE antes de
  inserir jurisprudencia em peca final.
---

# AUDITORIA-PRE-ENVIO — Tres Camadas Anti-Erro

## 1. POR QUE EXISTE

A `validar-jurisprudencia` confirma que **o link e a citacao sao reais**.

Esta skill vai alem: confirma que **a jurisprudencia citada realmente
sustenta a tese do usuario** — porque jurisprudencia validada mas que joga
*contra* a tese e tao perigosa quanto inventada.

E sua linha de defesa final antes do "protocolar".

---

## 2. INPUT NECESSARIO

Para rodar a auditoria voce precisa de:

1. **Tese / pedido do usuario** — o que ele quer sustentar (1-2 frases).
2. **Lista de citacoes que ele pretende usar** — com URLs e ementas.
3. **(Opcional) Peca em rascunho** — para conferir onde cada citacao vai entrar.

Se faltar (1) ou (2), pergunte UMA vez. Nao prossiga sem ter os dois.

---

## 3. WORKFLOW — TRES CAMADAS

Para CADA citacao da lista:

### CAMADA 1 — Inteiro teor foi lido?

**Pergunta:** voce, Claude, analisou o conteudo da pagina linkada (nao
so a ementa de 5 linhas)?

**Acao:**
- Faca um WebFetch fresco na URL.
- Capture pelo menos 15-30 paragrafos do voto/acordao se disponivel.
- Identifique:
  - **Fato base do caso** (em 1 frase)
  - **Fundamento legal central** (qual lei/artigo/sumula)
  - **Razao de decidir** (ratio decidendi, em 1-2 frases)
  - **Distincoes/excecoes** que o tribunal apontou

Se a pagina so retornou ementa (sem inteiro teor), marque
`📄 INTEIRO TEOR NAO DISPONIVEL — analise restrita a ementa` e prossiga
com a Camada 2 com essa ressalva.

### CAMADA 2 — Link confirmado por fetch real?

**Pergunta:** o status de validacao da skill `validar-jurisprudencia` foi
✅, ⚠️ ou 🔴?

Se nao houver registro de validacao previa, **dispare WebFetch agora**
e classifique conforme a regra da skill `validar-jurisprudencia`
(secao 2 daquela skill).

| Status | Tratamento na auditoria |
|--------|-------------------------|
| ✅ | Pode prosseguir pra Camada 3 |
| ⚠️ | Pode prosseguir mas sinaliza ressalva no veredito |
| 🔴 | **BLOQUEAR** imediatamente. Nao prossegue pra Camada 3. |

### CAMADA 3 — Nexo com a tese do usuario?

Esta e a camada mais delicada. **Estruture explicitamente** (nao
"feeling"):

```markdown
**Tese do usuario (1 frase):**
> [transcricao da tese que ele defende]

**Tese central da jurisprudencia (1 frase):**
> [o que o tribunal decidiu, em sintese]

**Alinhamento:**
- [ ] PRO     — jurisprudencia sustenta diretamente a tese do usuario
- [ ] CONTRA  — jurisprudencia decide em sentido oposto
- [ ] NEUTRO  — jurisprudencia trata de tema parecido mas nao decide a questao em jogo
- [ ] DEPENDE — alinhamento depende de fato X estar presente no caso do usuario

**Distincao relevante:**
[se algum fato do caso julgado diverge do caso do usuario de forma
que pode comprometer a aplicacao analogica, descrever aqui em 1-3 linhas]

**Risco se citado:**
[1 frase do que pode acontecer se o advogado citar esta jurisprudencia.
Ex: "baixo" / "medio — pode ser distinguishing pelo juiz" / "alto —
jurisprudencia evoluiu apos esta decisao"]
```

### Veredito por citacao

Combinando as 3 camadas:

| Veredito | Quando emitir |
|----------|---------------|
| ✅ **APROVADA** | Camada 1 (inteiro teor lido) + Camada 2 (✅) + Camada 3 (PRO, baixo risco) |
| 📝 **REVISAR** | 1 ou 2 ressalvas: ementa-only / status ⚠️ / nexo DEPENDE / risco medio |
| 🚫 **BLOQUEADA** | Camada 2 = 🔴 OU Camada 3 = CONTRA OU risco alto |

---

## 4. OUTPUT FINAL — TEMPLATE

```markdown
## Auditoria pre-envio — [N] citacoes

**Tese do usuario:** [transcricao]
**Data da auditoria:** [DD/MM/YYYY HH:MM]

---

### Citacao 1 — [Tribunal · numero · relator]

**🔍 Camada 1 — Inteiro teor:** [analisado / ementa-only]
- Fato base: [...]
- Fundamento central: [...]
- Razao de decidir: [...]

**🔗 Camada 2 — Link:** ✅ / ⚠️ / 🔴 (fonte: <URL>)

**⚖️ Camada 3 — Nexo:**
- Tese do usuario: [transcricao]
- Tese da juris: [sintese]
- Alinhamento: PRO / CONTRA / NEUTRO / DEPENDE
- Distincao: [se houver]
- Risco: [baixo/medio/alto]

**VEREDITO:** ✅ APROVADA | 📝 REVISAR | 🚫 BLOQUEADA

**Recomendacao de uso:** [como integrar ou por que nao integrar]

---

[repete para cada citacao]

---

## Resumo final

| # | Tribunal | Veredito | Acao |
|---|----------|----------|------|
| 1 | STJ | ✅ | Pode citar diretamente — fundamento principal |
| 2 | TJSP | 📝 | Citar com ressalva — fato divergente, usar como suporte secundario |
| 3 | TRF3 | 🚫 | NAO citar — jurisprudencia contraria a tese |

**Aprovadas:** [N] · **Para revisar:** [N] · **Bloqueadas:** [N]

---

⚠️ **AVISO LEGAL:** Esta auditoria e assistida por IA. A conferencia
humana final, especialmente do inteiro teor e da pertinencia ao caso
concreto, e responsabilidade exclusiva do advogado antes de protocolar.
```

---

## 5. PROIBICOES ABSOLUTAS

1. Nao emitir ✅ APROVADA sem ter rodado WebFetch fresco na URL.
2. Nao avaliar nexo "no olho" — sempre transcrever a tese do usuario e a
   tese da jurisprudencia LITERAIS e comparar estruturadamente.
3. Nao misturar citacoes — auditar UMA POR UMA com saida estruturada.
4. Nao ocultar BLOQUEADAS — destacar no resumo final.
5. NUNCA omitir o aviso legal final.
