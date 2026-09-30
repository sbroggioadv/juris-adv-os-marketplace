---
name: buscar-jurisprudencia
description: >
  BUSCAR-JURISPRUDENCIA — Pesquisa jurisprudencia em portais oficiais (STF,
  STJ, TST, TRFs, TRTs, TJs) usando WebSearch + WebFetch nativos. Nao usa
  agregadores comerciais. Retorna cada citacao com URL da fonte e
  classificacao da origem (inteiro teor vs ementa). Ao final, AUTO-INVOCA a
  skill validar-jurisprudencia para confirmar cada link. Use quando o
  usuario pedir "buscar jurisprudencia", "preciso de precedente sobre X",
  "tem julgado de Y", "pesquisar acordao", "encontrar sumula", "ver decisao
  do STJ sobre Z", ou disparar /juris.
---

# BUSCAR-JURISPRUDENCIA — Pesquisa de Precedentes com Rastreabilidade

## 1. REGRA DE OURO ANTI-HALUCINACAO

**Nao invente julgado.** Toda jurisprudencia retornada DEVE ter:
- URL real da fonte (obtida via WebSearch ou conhecida do tribunal)
- Classificacao da fonte: `OFICIAL — INTEIRO TEOR` (pagina do tribunal com o acordao completo) ou
  `OFICIAL — EMENTA` (pagina do tribunal com ementa apenas). Fonte nao-oficial nao entra.
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

Sempre **primeiro** as fontes oficiais (mais confiaveis — mas leia a nota de anti-bot abaixo):

| Tribunal | URL base de busca |
|----------|-------------------|
| STF | `https://jurisprudencia.stf.jus.br/pages/search` |
| STJ | `https://scon.stj.jus.br/SCON/pesquisar.jsp` |
| TST | `https://jurisprudencia.tst.jus.br/` |
| TST sumulas/OJs | `https://www.tst.jus.br/sumulas` |
| TRF1 | `https://jurisprudencia.cjf.jus.br/trf1` |
| TRF2 | `https://www10.trf2.jus.br/consultas/?entqr=3&ie=UTF-8` |
| TRF3 | `https://web.trf3.jus.br/jurisprudencia` |
| TRF4 | `https://jurisprudencia.trf4.jus.br/pesquisa/pesquisa.php` |
| TRF5 | `https://juliapesquisa.trf5.jus.br/julia-pesquisa` |
| TRF6 | `https://eproc-jur.trf6.jus.br/` |
| TJSP | `https://esaj.tjsp.jus.br/cjsg/consultaCompleta.do` |
| TJRJ | `https://www3.tjrj.jus.br/ejuris/ConsultarJurisprudencia.aspx` |
| TJMG | `https://www5.tjmg.jus.br/jurisprudencia/` |
| TJRS | `https://www.tjrs.jus.br/buscas/jurisprudencia/` |
| Justica Federal unificada (STF · STJ · TNU · TRF1-6) | `https://jurisprudencia.cjf.jus.br/unificada` |

(Lista nao exaustiva — consulte outros tribunais conforme estado.)

URLs verificadas em 30/09/2026 com `curl -sL -o /dev/null -m 15 -w '%{http_code}'`.
Enderecos mortos foram substituidos pelo endereco oficial vivo do proprio tribunal ou do CJF.
Nas rotas do CJF a barra final importa: `/trf1` responde 200, `/trf1/` responde 404 — use sem
barra. Revalide antes de citar se passar muito tempo: tribunal troca de host sem aviso.

**Anti-bot em fonte oficial — medido, nao suposto.** Nem toda fonte oficial aceita fetch
automatico. Medido em 30/09/2026: STF devolve `202` com header `x-amzn-waf-action: challenge`;
STJ devolve `403` com `cf-mitigated: challenge`; o CJF carrega reCAPTCHA; o TRF3
(`https://web.trf3.jus.br/jurisprudencia`) devolveu `000` no curl local em 30/09/2026 (stream
HTTP/2, exit 92), mas a pagina oficial abre por navegador/WebFetch — mesma conduta do STF/STJ.
O endereco e o oficial correto, mas o fetch automatizado pode falhar. Nesse caso:
- use WebSearch restrito ao dominio oficial (`site:scon.stj.jus.br`) para achar o julgado;
- declare na citacao que a confirmacao por fetch nao foi possivel (desafio anti-bot);
- **nunca** leia `202`/`403` como "pagina encontrada" e nunca complete por agregador.

**Fonte que nao responde.** Se a URL oficial de um tribunal nao responder (`000`, timeout ou
`404`), tente primeiro a busca unificada do CJF para aquele tribunal. Se tambem falhar, marque
no relatorio, literalmente:

> `fonte oficial indisponivel nesta verificacao — [tribunal] — [codigo HTTP medido]`

e siga com os tribunais que responderam. Nao invente endereco novo nem troque por agregador
para tapar o buraco.

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

### Passo 4 — Quando as fontes oficiais derem pouco resultado

**NAO recorra a agregadores comerciais.** JusBrasil e Escavador vedam expressamente acesso
automatizado nos termos de uso (robos, spiders, crawlers, scrapers), salvo permissao escrita.
Buscar neles por este produto e violacao contratual, independente de marcar a origem.

Se as fontes oficiais derem pouco resultado, o comportamento correto e **declarar o resultado
magro**, nunca preenche-lo por outra via:
- diga em quais tribunais buscou e com quais termos;
- diga quantos resultados vieram;
- sugira variacao de termo, de periodo ou de tribunal, e pergunte se deve tentar.

Resultado pequeno e um fato sobre a busca. Completar com fonte proibida transforma um fato
verificavel em risco contratual.

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
- **Origem:** OFICIAL — INTEIRO TEOR | OFICIAL — EMENTA | VINCULANTE
- **Confirmacao por fetch:** OK [codigo HTTP] | FALHOU — [desafio anti-bot / fonte indisponivel]
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
**Total de citacoes:** [N] · todas oficiais · confirmadas por fetch: [N]
**Fontes oficiais indisponiveis nesta verificacao:** [tribunal — codigo HTTP] ou [nenhuma]

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
6. **NAO buscar em JusBrasil nem Escavador.** Os termos de uso dos dois vedam acesso
   automatizado; marcar a origem como `AGREGADOR` nao sana a vedacao. Fonte oficial ou
   resultado declarado como magro — nao ha terceira via.
