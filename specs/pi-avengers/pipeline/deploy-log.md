# Deploy Log — Fase 7: Pi Avengers

**Agente:** Deploy (Claude Code)
**Data:** 2026-05-11
**Commit:** c2bc3cd
**Branch:** main → origin/main
**Destino:** https://alanraldi.github.io/dev_user_claude/

---

## Pré-deploy

Lido o `qa-report.md`. Resultado: **APROVADO COM RESSALVAS**.

Antes do commit foram aplicados os dois fixes recomendados pelo QA:

| Fix | Arquivo | Linha | Alteração |
|---|---|---|---|
| Race condition `visibilitychange` (Risco 1 — Alta) | `index.html` | 273 | Adicionado `if(!document.hidden)` dentro do `.then()` |
| Overflow `ph>=4` em `_advanceSection` (Risco 2 — Baixa) | `index.html` | 674, 681, 688 | Substituído `===4` por `>=4` nas seções 1, 2 e 3 |

---

## Arquivos commitados

| Arquivo | Tipo |
|---|---|
| `index.html` | modificado — Fase 7 + fixes QA |
| `specs/pi-avengers/pipeline/po-brief.md` | novo — artefato PO |
| `specs/pi-avengers/pipeline/dev-notes.md` | novo — artefato DEV |
| `specs/pi-avengers/pipeline/qa-report.md` | novo — artefato QA |

---

## Resultado

| Etapa | Status |
|---|---|
| `git add` | ✅ OK |
| `git commit` | ✅ OK — hash `c2bc3cd` |
| `git push origin main` | ✅ OK (1ª tentativa: erro 500 GitHub; 2ª tentativa: sucesso) |
| GitHub Pages deploy | ⏳ Aguardando propagação (~1 min) |

---

## Pendências (Fase 8)

| Item | Origem |
|---|---|
| `barsDone` não resetado ao retroceder Seção 3 | QA Risco 3 |
| Som de segmento pode não tocar ao pular fase rápido | QA Task 7.2 |
