---
name: formatar-citacao
description: >
  FORMATAR-CITACAO — Formata jurisprudencia ja validada em dois formatos
  prontos: (1) ABNT NBR 6023/10520 para referencias e citacoes formais e
  (2) bloco "pronto pra peca" com ementa destacada + nota de rodape
  estruturada. Aceita uma ou varias citacoes. Use quando o usuario disser
  "formata pra peca", "monta citacao ABNT", "deixa pronto pra colar",
  "gera referencia", "como cito esse acordao".
---

# FORMATAR-CITACAO — Saida Limpa para Peca e Referencia

## 1. INPUT

Lista de citacoes ja validadas (ou trazidas pelo usuario), cada uma com:
- Tribunal, orgao julgador, relator, numero, data, publicacao
- Ementa (trecho)
- URL da fonte

Se o usuario tiver passado pela cadeia `buscar` -> `validar` -> `auditoria`,
use as citacoes APROVADAS / REVISAR de la (nao formate BLOQUEADAS).

---

## 2. DOIS FORMATOS DE SAIDA

### FORMATO A — ABNT (NBR 6023 para referencia / NBR 10520 para citacao)

#### Referencia (vai na bibliografia ou nas referencias da peca)

```
[PAIS]. [Tribunal]. [Tipo de processo: numero]. [Relator: Min./Des. Nome].
[Orgao julgador], [data de julgamento], [publicacao]. Disponivel em:
<[URL]>. Acesso em: [DD mes YYYY].
```

**Exemplo concreto:**
```
BRASIL. Superior Tribunal de Justica. Agravo Interno no Recurso Especial
2.123.456/SP. Relator: Min. Fulano de Tal. Quarta Turma, julgado em
14/05/2024, DJe 21/05/2024. Disponivel em:
<https://scon.stj.jus.br/SCON/jurisprudencia/toc.jsp?id=12345>.
Acesso em: 25 mai. 2026.
```

#### Citacao direta no corpo do texto (NBR 10520)

```
Conforme decidiu o [tribunal] no julgamento do [processo], de relatoria
do [relator]: "[trecho literal da ementa entre aspas]" ([REFERENCIA
ABREVIADA], YYYY).
```

### FORMATO B — Bloco pronto pra peca (estilo padrao forense)

```markdown
> **[TRIBUNAL] — [Tipo de processo numero]**
> Relator: [nome] · [Orgao] · julgado em [data] · publicado em [data]
>
> "[ementa transcrita literalmente, com aspas]"
>
> Disponivel em: [URL]

[1-2 paragrafos do advogado-usuario integrando a tese da jurisprudencia
ao caso concreto — isso voce NAO escreve sozinho, voce sugere onde o
usuario deve preencher. Use placeholder:]

> [INSERIR AQUI: aplicacao da tese ao caso concreto — em que medida o
> precedente acima sustenta o pedido X formulado pelo autor/reu.]
```

---

## 3. WORKFLOW

1. Receber lista de citacoes (ou pegar das skills anteriores na sessao).
2. Perguntar ao usuario: **"Formato ABNT, bloco pra peca, ou ambos?"**
   (Default se nao responder em 1 turno: **ambos**.)
3. Para cada citacao, montar a saida no(s) formato(s) escolhido(s).
4. Apresentar tudo em um bloco unico, separado por citacao, com
   `---` entre cada uma.

---

## 4. REGRAS DE ESTILO

- Aspas portuguesas (" "), nao retas, na ementa.
- Numeros de processo no formato CNJ se disponivel (`NNNNNNN-DD.AAAA.J.TR.OOOO`),
  caso contrario formato historico do tribunal.
- Datas em PT-BR: DD/MM/YYYY no corpo, DD mes YYYY (mes abreviado) na
  data de acesso ABNT.
- Tribunal por extenso (Superior Tribunal de Justica) e abreviacao
  entre parenteses (STJ) na primeira mencao.
- Cargo do relator: Ministro/a -> Min., Desembargador/a -> Des.,
  Juiz/a Federal -> Juiz Fed., etc.

---

## 5. EXEMPLO COMPLETO DE OUTPUT

```markdown
## Citacoes formatadas — 3 julgados

---

### 1) STJ · AgInt no AREsp 2.123.456/SP

**📚 ABNT (referencia):**
```
BRASIL. Superior Tribunal de Justica. Agravo Interno no Agravo em
Recurso Especial 2.123.456/SP. Relator: Min. Fulano de Tal. Quarta
Turma, julgado em 14/05/2024, DJe 21/05/2024. Disponivel em:
<https://scon.stj.jus.br/SCON/...>. Acesso em: 25 mai. 2026.
```

**📚 ABNT (citacao direta):**
```
Conforme decidiu o STJ no julgamento do AgInt no AREsp 2.123.456/SP:
"[...trecho da ementa...]" (BRASIL, 2024).
```

**📄 Bloco pra peca:**
> **STJ — AgInt no AREsp 2.123.456/SP**
> Relator: Min. Fulano de Tal · Quarta Turma · julgado em 14/05/2024 ·
> DJe 21/05/2024
>
> "[ementa transcrita literalmente]"
>
> Disponivel em: https://scon.stj.jus.br/SCON/...

> [INSERIR AQUI: aplicacao da tese ao caso concreto]

---

### 2) [proxima citacao] ...
```

---

## 6. PROIBICOES

1. NAO formatar citacao que veio com veredito 🚫 BLOQUEADA da auditoria.
2. NAO inventar campos faltantes (data, relator, publicacao). Se faltar,
   marca `[CAMPO INDISPONIVEL — CONFERIR NO TRIBUNAL]`.
3. NAO parafrasear ementa — sempre literal entre aspas.
4. NAO omitir URL — sempre incluir.
