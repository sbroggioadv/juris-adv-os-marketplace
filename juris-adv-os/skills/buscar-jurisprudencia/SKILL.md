---
name: buscar-jurisprudencia
description: >
  BUSCAR-JURISPRUDENCIA — Pesquisa jurisprudencia em portais oficiais (STF,
  STJ, TST, TRFs, TRTs, TJs) e agregadores (JusBrasil, Escavador) usando
  WebSearch + WebFetch nativos. Retorna cada citacao com URL da fonte e
  classificacao da origem (oficial vs agregador). Ao final, AUTO-INVOCA a
  skill validar-jurisprudencia para confirmar cada link. Use quando o
  usuario pedir "buscar jurisprudencia", "preciso de precedente sobre X",
  "tem julgado de Y", "pesquisar acordao", "encontrar sumula", "ver decisao
  do STJ sobre Z", ou disparar /juris.
---

# BUSCAR-JURISPRUDENCIA — Pesquisa de Precedentes com Rastreabilidade

## 1. REGRA DE OURO ANTI-HALUCINACAO

**Nao invente julgado.** Toda jurisprudencia retornada DEVE ter:
- URL real da fonte (obtida via WebSearch ou conhecida do tribunal)
- Classificacao da fonte: `OFICIAL` (jurisprudencia.X.jus.br) ou `AGREGADOR` (JusBrasil/Escavador)
- Trecho da ementa transcrito (nao parafrase a primeira vez)

Se WebSearch retornar zero resultados confiaveis, voce DEVE dizer ao usuario:
> "Nao encontrei jurisprudencia consolidada sobre essa tese especifica nos portais oficiais consultados. Sugestoes: [...]"

Nao preencher o vazio com julgado inventado. **NUNCA.**

---

## 2. WORKFLOW

### Passo 1 — Capturar a tese

Pergunte ou extraia do prompt:
- **Tema/tese exata** (1-2 frases). Ex: "responsabilidade civil do banco por fraude em PIX nao autorizado"
- **Tribunal preferencial** (opcional): STF, STJ, TST, TRF1-6, TJ-X, TST etc.
- **Esfera/area:** civel, penal, trabalhista, tributaria, administrativa, etc.
- **Recencia:** ultimos 12 meses / 5 anos / qualquer

Se faltar info critica, pergunte UMA vez. Nao mais que isso.

### Passo 2 — Priorizar fontes oficiais

Sempre **primeiro** as fontes oficiais (mais confiaveis, sem anti-bot):

| Tribunal | URL base de busca |
|----------|-------------------|
| STF | `https://jurisprudencia.stf.jus.br/pages/search` |
| STJ | `https://scon.stj.jus.br/SCON/pesquisar.jsp` |
| TST | `https://jurisprudencia.tst.jus.br/` |
| TRF1 | `https://jurisprudencia.trf1.jus.br/` |
| TRF2 | `https://www10.trf2.jus.br/consultas/?entqr=3&ie=UTF-8` |
| TRF3 | `https://web.trf3.jus.br/base-textual` |
| TRF4 | `https://jurisprudencia.trf4.jus.br/pesquisa/pesquisa.php` |
| TRF5 | `https://julia-pesquisa.trf5.jus.br/julia-pesquisa/` |
| TRF6 | `https://jurisprudencia.trf6.jus.br/` |
| TJSP | `https://esaj.tjsp.jus.br/cjsg/consultaCompleta.do` |
| TJRJ | `https://www3.tjrj.jus.br/ejud/ConsultaJurisprudencia.aspx` |
| TJMG | `https://www5.tjmg.jus.br/jurisprudencia/` |
| TJRS | `https://www.tjrs.jus.br/buscas/jurisprudencia/` |
| TST sumulas/OJs | `https://www3.tst.jus.br/jurisprudencia/Sumulas_com_indice/` |

(Lista nao exaustiva — consulte outros tribunais conforme estado.)

### Passo 3 — Buscar via WebSearch

Use queries especificas combinando:
- Termos juridicos exatos (`"responsabilidade civil"` aspeado)
- Numero de lei/artigo (`art. 14 CDC`)
- Numero de tema repetitivo (`Tema 1051 STJ`)
- Site oficial (`site:scon.stj.jus.br`)

Exemplo:
```
WebSearch: "responsabilidade banco fraude pix" site:scon.stj.jus.br
WebSearch: "fraude eletronica" "consumidor" julgado STJ 2024
```

**Limite: 6 buscas no maximo por tese.** Se nao achou, e porque o tema e novo ou nao consolidado — informe ao usuario, nao force.

### Passo 4 — Buscar em agregadores APENAS como complemento

Se as fontes oficiais derem pouco resultado, complemente com:
- **JusBrasil** (`site:jusbrasil.com.br`)
- **Escavador** (`site:escavador.com`)

**MARQUE SEMPRE** essas citacoes como `AGREGADOR — conferir no tribunal`.

### Passo 5 — Estruturar cada citacao

Para cada julgado encontrado, monte:

```markdown
### [N] [Tribunal] — [Sumario em 1 linha]

- **Numero dos autos:** [completo conforme aparece]
- **Orgao julgador:** [Turma / Camara / Plenario / Secao]
- **Relator(a):** [nome]
- **Data de julgamento:** [DD/MM/YYYY]
- **Publicacao:** [DJe / data]
- **Fonte:** [URL completa]
- **Origem:** OFICIAL | AGREGADOR
- **Ementa (trecho relevante):**
  > [transcricao literal de 3-8 linhas, sem parafrase]
- **Tese central:** [1 frase que sintetiza o entendimento — sua sintese]
- **Relevancia para o caso do usuario:** [1-2 frases conectando a tese da busca]
```

Numere sequencialmente. Maximo 10 citacoes por busca — qualidade > quantidade.

### Passo 6 — AUTO-INVOCAR validacao

**OBRIGATORIO.** Apos montar a lista, IMEDIATAMENTE invoque a skill `validar-jurisprudencia` passando a lista:

```
Acionando validar-jurisprudencia para confirmar cada citacao via fetch...
```

Use a Skill tool com `skill="validar-jurisprudencia"`. NAO PULE este passo — e o coracao da promessa do plugin (sem validacao automatica + link, nao ha valor de mercado).

---

## 3. CASOS ESPECIAIS

### Sumula / Tese vinculante

Se o tema tiver sumula ou tese fixada (repetitivo / repercussao geral / IAC / IRDR), priorize:
- Cite a sumula/tese ANTES dos acordaos.
- Marque como `VINCULANTE` no campo Origem.
- Link para a publicacao oficial do tribunal.

### Tema novo / divergencia

Se a jurisprudencia ainda nao esta consolidada:
- Apresente as **duas correntes** com julgados representativos de cada.
- Sinalize: `⚠️ TEMA NAO CONSOLIDADO — duas correntes ativas`.

### Inteiro teor solicitado

Se o usuario pedir explicitamente "traz o inteiro teor de X", faca WebFetch direto na URL do tribunal e devolva o texto integral relevante (10-30 paragrafos centrais), nao so a ementa.

---

## 4. OUTPUT FINAL — TEMPLATE

```markdown
## Jurisprudencia encontrada — [Tese resumida]

**Busca realizada em:** [DD/MM/YYYY HH:MM]
**Tribunais consultados:** [lista]
**Total de citacoes:** [N] · oficiais: [N] · agregadores: [N]

---

[Lista das citacoes estruturadas, numeradas 1, 2, 3...]

---

🔍 **Proximo passo automatico:** acionando `validar-jurisprudencia` para
confirmar cada URL via fetch real. Aguarde o relatorio de validacao
antes de citar em peca.
```

---

## 5. PROIBICOES ABSOLUTAS

1. **NAO inventar numero de processo.** Se nao tem certeza, omite e diz "numero indisponivel — confira no tribunal".
2. **NAO parafrasear ementa na primeira citacao.** Transcreva literal (uso de aspas).
3. **NAO atribuir a tribunal errado.** Em duvida, marca `⚠️ origem incerta`.
4. **NAO afirmar "validada" sem ter chamado a skill `validar-jurisprudencia`.**
5. **NAO retornar mais de 10 citacoes.** Foco em qualidade, nao quantidade.
6. **NAO usar so JusBrasil/Escavador.** Sempre tenta tribunais oficiais primeiro.
