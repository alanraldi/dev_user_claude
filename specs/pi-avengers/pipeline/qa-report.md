# QA Report — Task 7.3: Fonte Científica da Variável "pi"

**Revisor:** QA Engineer (Claude Code)
**Data da revisão:** 2026-05-12
**Arquivo analisado:** index.html (linhas 63–67 CSS, 131–135 HTML, 160–163 T.pt, 180–183 T.en, 203 applyLang)
**Referência:** po-brief.md + dev-notes.md

---

## Resultado Geral

> **APROVADO COM RESSALVAS**

A implementação da seção de referências está correta em todos os aspectos funcionais: HTML estruturado, CSS aplicado, strings internacionalizadas, links com atributos de segurança, integração com `applyLang()` e nenhuma regressão detectada. A única ressalva é um desvio de spec de baixo impacto: o brief especifica `<div id="refs">` no critério 1, mas o elemento HTML foi implementado sem esse `id` (apenas com `class="refs"`), contrariando a especificação literal — embora sem consequência funcional, pois o `id` não é referenciado em JS nem em CSS.

---

## Tarefa 7.3 — Incluir Fonte Científica da Variável "pi"

| Critério | Status | Observação |
|---|---|---|
| `<div id="refs">` aparece abaixo de `.digits` | ⚠️ PARCIAL | O elemento existe como `<div class="refs">` (linha 132), posicionado corretamente após `.digits` (linha 131). Porém o `id="refs"` especificado no critério está ausente — o elemento tem apenas `class="refs"`. O brief conflita internamente (critério 1 pede `id="refs"`; a "Nota ao dev" na seção HTML diz usar `refs-title` e `refs-body` nos filhos, sem mencionar `id="refs"` no pai). O DEV resolveu o conflito priorizando a nota, mas o critério literal não é satisfeito. Sem impacto funcional. |
| Título "REFERÊNCIAS" (PT) / "REFERENCES" (EN) em estilo HUD | ✅ PASS | `T.pt.refTitle: 'REFERÊNCIAS'` (linha 160) e `T.en.refTitle: 'REFERENCES'` (linha 181). Aplicados por `setText('refs-title', l.refTitle)` (linha 203). CSS `.refs-title` com `letter-spacing:3px`, `text-transform:uppercase`, cor HUD `rgba(229,57,53,.5)`. |
| Duas referências numeradas `[1]` e `[2]` em `font-family:Courier New`, tamanho `clamp(.62rem,1.8vw,.72rem)`, cor `#7a8eaa` | ✅ PASS | CSS `.refs-body` (linha 65): `font-size:clamp(.62rem,1.8vw,.72rem);color:#7a8eaa;line-height:1.8`. `font-family` herdado do `body` (`Courier New`). Referências populadas como `[1]...` e `[2]...` via `setHTML`. |
| Referência `[1]` aponta para Wolfram MathWorld Pi com `target="_blank" rel="noopener"` | ✅ PASS | `T.pt.ref1` e `T.en.ref1` (linhas 161, 182): `href="https://mathworld.wolfram.com/Pi.html" target="_blank" rel="noopener"`. URL, texto âncora, atributos corretos em ambos os idiomas. |
| Referência `[2]` aponta para Wikipedia PT em PT e Wikipedia EN em EN, com `target="_blank" rel="noopener"` | ✅ PASS | PT: `href="https://pt.wikipedia.org/wiki/Pi"` (linha 162). EN: `href="https://en.wikipedia.org/wiki/Pi"` (linha 183). Ambos com `target="_blank" rel="noopener"`. |
| Textos definidos no objeto `T`, aplicados por `applyLang()` via `setHTML()` — nenhum texto hardcoded em HTML | ✅ PASS | HTML dos `<div id="refs-title">` e `<div id="refs-body">` iniciam vazios (linhas 133–134). Populados exclusivamente por `applyLang()`: `setText('refs-title',l.refTitle); setHTML('refs-body',l.ref1+'<br>'+l.ref2)` (linha 203). |
| Separador visual acima da seção (linha fina, 100% do container) | ✅ PASS | CSS `.refs` (linha 63): `border-top:1px solid rgba(229,57,53,.18)` e `padding-top:10px`. Linha sutil de 100% da largura do container `.wrap`. |
| Links com cor dourada `#f5a623` em estado normal e `#e53935` em hover | ✅ PASS | CSS `.refs a{color:#f5a623;text-decoration:none}` (linha 66) e `.refs a:hover{color:#e53935}` (linha 67). |
| Visível e legível em mobile (375px), sem transbordo horizontal | ✅ PASS | `.refs{width:100%;text-align:center}`. Herda `max-width:780px` do `.wrap`. `font-size:clamp(.62rem,1.8vw,.72rem)` e `line-height:1.8` permitem quebra natural. Sem `white-space:nowrap` nem largura fixa. |
| Ao trocar idioma, textos e links das referências atualizam corretamente | ✅ PASS | `toggleLang()` chama `applyLang()` (linha 205), que re-executa `setText('refs-title',l.refTitle); setHTML('refs-body',l.ref1+'<br>'+l.ref2)` com o novo idioma. Link da Wikipedia muda de `pt.wikipedia.org` para `en.wikipedia.org`. |
| Nenhuma animação canvas, som ou botão existente é afetado | ✅ PASS | Adição é puramente HTML/CSS estático + duas linhas em `applyLang()`. Nenhuma variável de animação, nenhum RAF, nenhuma Web Audio API tocada. Confirmado na verificação de regressões abaixo. |

**Placar: 10 ✅ PASS / 1 ⚠️ PARCIAL / 0 ❌ FAIL**

---

## Riscos Identificados

### Risco 1 — Ausência de `id="refs"` no container (Backlog)
**Onde:** index.html, linha 132
**Cenário:** o critério 1 do brief especifica `<div id="refs">`. O elemento entregue é `<div class="refs">` sem o `id`. Como o `id` não é referenciado por nenhum seletor CSS nem por JavaScript, não há impacto funcional atual. O risco seria um código futuro que tentasse fazer `document.getElementById('refs')` e obtivesse `null`.
**Mitigação sugerida:**
```html
<div class="refs" id="refs">
```
Adição trivial, sem quebra de nada existente.

---

## Regressões

| Item | Status | Observação |
|---|---|---|
| `toggleLang()` | ✅ Sem regressão | Função presente na linha 205, altera `lang`, chama `applyLang()`. Inalterada. |
| `applyLang()` | ✅ Sem regressão | Linhas 191–204. Todas as chamadas anteriores (linhas 193–202) preservadas; as duas novas linhas foram adicionadas no final do bloco (linha 203), conforme o brief determinava. |
| `Sound.toggle()` | ✅ Sem regressão | Alterna `muted`, cria/retoma AudioContext, toca beep. Não foi alterado pela Task 7.3. |
| `Sound.tone()` | ✅ Sem regressão | Verifica `muted` antes de tocar (linha 273). Inalterado. |
| `restart1`, `restart2`, `restart3` | ✅ Sem regressão | Funções presentes nas linhas 457, 497, 628. Não foram alteradas. |
| `tick1`, `tick2`, `tick3` | ✅ Sem regressão | Funções de animação presentes nas linhas 455, 495, 626. Não foram alteradas. |
| `loop1`, `loop2`, `loop3` | ✅ Sem regressão | Presentes nas linhas 456, 496, 627. Não foram alteradas. |
| Badges `b0–b3`, `a0–a3`, `s0–s2` | ✅ Sem regressão | Lógica de ativação nos `drawSegs*` verificada: `classList.add('on')` e `classList.remove('on')` presentes e inalterados. |
| Background canvas | ✅ Sem regressão | IIFE independente presente nas linhas 296–324. Não foi tocada. |

**Nenhuma regressão detectada.**

---

## Recomendação Final

**Deploy pode ser realizado.** A Task 7.3 entrega todos os critérios funcionais corretamente. A única divergência (ausência de `id="refs"` no container) é inofensiva para o estado atual da aplicação e pode ser corrigida em qualquer momento.

| Prioridade | Item | Ação |
|---|---|---|
| Backlog | `id="refs"` ausente no container da seção | Adicionar `id="refs"` ao `<div class="refs">` (linha 132) |
