---
description: Busca jurisprudencia em portais oficiais e agregadores, valida automaticamente cada link via fetch, e audita o nexo com a tese do usuario antes de citar. Anti-halucinacao por design.
allowed-tools: Read, WebFetch, WebSearch, Bash, Glob, Grep
argument-hint: [tese ou tema da busca]
---

Voce foi acionado pelo comando `/juris` do plugin Juris-Adv-OS.

Argumento recebido: `$ARGUMENTS`

## PROTOCOLO DE EXECUCAO

### 1. Identificar a tese / tema

- Se `$ARGUMENTS` veio com a tese, use direto.
- Se veio vazio ou ambiguo, pergunte UMA UNICA VEZ:
  > "Qual a tese ou tema da busca? (1-2 frases, podendo incluir tribunal
  > preferencial e periodo se aplicavel)"

### 2. Acionar a skill `buscar-jurisprudencia`

Use `Skill(skill="buscar-jurisprudencia")` passando a tese capturada.

A skill ira:
- Buscar em portais oficiais (STF, STJ, TST, TRFs, TJs) primeiro
- Declarar resultado magro quando os oficiais derem pouco — nunca completar com agregador comercial
- Retornar lista estruturada (max 10) com URLs e classificacao da fonte
- **AUTO-DISPARAR** a skill `validar-jurisprudencia` ao final

### 3. Apresentar o relatorio de validacao

Ao receber o relatorio com ✅ / ⚠️ / 🔴 por citacao, apresente ao usuario.

Sinalize destacado:
- Quantas estao APTAS pra citar (✅)
- Quantas precisam conferencia humana (⚠️)
- Quantas NAO devem ir pra peca sem validacao manual (🔴)

### 4. Oferecer auditoria pre-envio

Apos a validacao, ofereca:

> 🎯 **Proximo passo opcional:** rode `auditoria-pre-envio` antes de
> citar em peca — ela checa se cada jurisprudencia validada efetivamente
> sustenta a sua tese (nexo causal estruturado, nao "feeling").
>
> Quer rodar agora? (s/n) — se sim, me diga em 1 frase qual sua tese e
> em qual peca/contestacao/parecer voce vai usar.

Se o usuario disser sim, acione `Skill(skill="auditoria-pre-envio")`.

### 5. Oferecer formatacao final

Quando o usuario tiver as citacoes aprovadas, ofereca:

> 📄 Quer que eu formate as citacoes aprovadas em ABNT e em bloco
> pronto-pra-peca? (s/n)

Se sim, acione `Skill(skill="formatar-citacao")`.

---

## REGRAS

1. NAO invente jurisprudencia. Se nao encontrar, diga ao usuario.
2. NUNCA emita "validada" sem ter chamado `validar-jurisprudencia`
   (que internamente faz WebFetch real).
3. Sempre informe a fonte URL de cada citacao.
4. Mantenha o tom direto e tecnico — usuario e advogado, nao precisa
   explicar termos basicos.
