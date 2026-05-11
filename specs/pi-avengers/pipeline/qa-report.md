# QA Report — Fase 7: Pi Avengers

**Revisor:** QA Engineer (Claude Code)
**Data da revisão:** 2026-05-11
**Arquivo analisado:** `index.html` (linhas 196–712)
**Referência:** `po-brief.md` + `dev-notes.md`

---

## Resultado Geral

> **APROVADO COM RESSALVAS**

Ambas as tasks foram implementadas e cobrem a grande maioria dos critérios de aceitação. Há três pontos que precisam atenção antes do deploy, sendo um deles de risco médio (race condition no visibilitychange) e dois de risco baixo (retrocesso da Seção 3 e edge case no avanço).

---

## Task 7.1 — Sons Contínuos: Hum do Reator Arc

| Critério | Status | Observação |
|---|---|---|
| Hum inicia ao clicar "SOM" (muted → unmuted), após o beep de confirmação | ✅ PASS | `toggle()` chama `_play()` (beep) e em seguida `_startHum()` dentro do `.then()` do `ctx.resume()`. Ordem correta. |
| Oscilador de baixa frequência (60–90 Hz, sine ou triangle), ganho ≤ 0.07 | ✅ PASS | 72 Hz, tipo `triangle`, ganho 0.055. Dentro das restrições do brief. |
| LFO de amplitude sutil (~0.3–0.8 Hz) modulando frequência ou ganho | ✅ PASS | LFO a 0.5 Hz (sine), amplitude ±4 Hz na frequência do hum. Dentro do range pedido. |
| Hum para instantaneamente ao silenciar (unmuted → muted) | ✅ PASS | `toggle()` chama `_stopHum()` antes do `return` quando `muted` é setado como `true`. |
| Hum para quando aba perde foco (`visibilitychange → hidden`) e retoma ao voltar | ⚠️ PARCIAL | Implementado (linha 268–275), mas há race condition: se o usuário trocar de aba muito rápido (hidden → visible antes do `.then()` resolver), `_startHum()` pode ser chamado duas vezes. O guard `if(this._humOsc)return` previne duplicatas, mas o segundo `resume().then()` fica pendente e pode chamar `_startHum()` depois de uma outra `_stopHum()`, deixando o hum ativo mesmo com a aba oculta. Risco baixo-médio. |
| Sons de eventos continuam funcionando normalmente sobre o hum | ✅ PASS | `_humGain` é um `GainNode` separado, completamente isolado do `_play()` interno. Sem interferência. |
| Em mobile, hum só começa após interação do usuário | ✅ PASS | O `AudioContext` só é criado/retomado dentro de `Sound.toggle()`, que é acionado por clique. Requisito do browser satisfeito. |
| Hum não cria loop de feedback (não chama `restart*` nem interfere com canvas) | ✅ PASS | `_startHum()` e `_stopHum()` não tocam nas variáveis de animação nem nos `requestAnimationFrame`. |
| Ao religar o som após silenciar, o hum retoma junto com o beep | ✅ PASS | Mesmo fluxo do primeiro acionamento: `toggle()` chama `_play()` + `_startHum()` dentro do `.then()`. |
| Usa `Sound.ctx` já existente — não cria segundo `AudioContext` | ✅ PASS | `_startHum()` usa `this.ctx`. Novo contexto só criado se `this.ctx` for `null` (linha 238). |
| Oscilador criado como nó persistente, conectado ao `ctx.destination` | ✅ PASS | Osciladores criados em `_startHum()` e armazenados em `_humOsc`/`_lfoOsc`. Não recriados a cada frame. |
| Volume do hum controlado via `GainNode` separado | ✅ PASS | `_humGain` separado, sem relação com o `GainNode` dos sons de evento. |
| Estrutura do objeto `Sound` mantida — sem alterar assinatura pública | ✅ PASS | Novos métodos prefixados com `_` (convenção de privado). `toggle()`, `tone()`, `boot()`, `segment()`, `barTone()`, `complete()`, `mystical()` inalterados. |

**Placar Task 7.1: 12 ✅ PASS / 1 ⚠️ PARCIAL / 0 ❌ FAIL**

---

## Task 7.2 — Modo Apresentação: Navegação por Teclado

| Critério | Status | Observação |
|---|---|---|
| `Espaço` ou `ArrowRight` avança para a próxima fase | ✅ PASS | Implementado via `isForward` → `_advanceSection()`. |
| Ordem sequencial entre seções: S1 toda → S2 toda → S3 toda | ✅ PASS | Lógica `if(!done1)` → `else if(!done2)` → `else if(!done3)` garante sequência. |
| Avançar fase pula diretamente (sem esperar temporizador natural) | ✅ PASS | `_advanceSection()` cancela o RAF ativo, incrementa `ph*`, zera `t_*` e inicia novo RAF. |
| Avanço redefine `t_X = 0` e `ph_X++` | ✅ PASS | Linha 673: `ph1++; t_1=0;` (idem para sec 2 e 3). |
| Se seção corrente já completou (`done* = true`), foco passa para a próxima seção | ⚠️ PARCIAL | Funciona para S1→S2→S3, mas há um edge case: quando `done1=true` e `done2=false`, o listener corretamente avança S2. Porém se o usuário pressionar a tecla exatamente no frame em que `ph1` chegou a 4 via tick natural (antes do `done1` ser setado pelo `tick1`), `_advanceSection(1)` pode ser chamado com `ph1=4` mas `done1=false`, executando `ph1++` → `ph1=5` e em seguida não acionando `done1=true` porque a condição é `if(ph1===4)`. Isso deixa `ph1=5` sem `done1`, quebrando a animação da Seção 1. Risco baixo, mas reproduzível se o usuário apertar tecla muito rápido no fim da fase 4. |
| Se todas as 3 seções estiverem completas, pressionar não faz nada | ✅ PASS | Após o `else if(!done3)`, não há mais condição — cai fora sem ação. |
| `ArrowLeft` / `Backspace` volta uma fase | ✅ PASS | Implementado via `_rewindSection()`. DEV entregou o retrocesso conforme a nota opcional do brief. |
| Retroceder: se já for fase 0, volta para seção anterior no estado de conclusão | ⚠️ PARCIAL | O handler de retrocesso não trata o caso `ph3===0 && !done3`: nenhuma das condições do bloco `else` é satisfeita, então a tecla não faz nada quando ph3=0 e done3=false. O esperado pelo brief seria passar o foco para S2. Comportamento inofensivo (tecla ignorada), mas diverge do spec. |
| Evento de teclado não dispara quando `activeElement` é `INPUT`, `TEXTAREA` ou `BUTTON` | ✅ PASS | Guard nas linhas 627–628. |
| `Espaço` não rola a página (`event.preventDefault()`) | ✅ PASS | `e.preventDefault()` chamado para qualquer tecla capturada (linha 635), antes do processamento. |
| Modo apresentação funciona junto com toggle de idioma e botão de som | ✅ PASS | O listener de teclado não interfere com `toggleLang()` nem `Sound.toggle()`. Guard exclui `BUTTON`, evitando captura acidental ao pressionar Enter/Espaço em botão com foco. |
| Sons de avanço de fase tocam normalmente ao pular | ⚠️ PARCIAL | O dev optou por deixar o próximo `tick*` acionar os sons naturalmente. Isso funciona para a maioria dos casos, mas ao pular para `ph=4` (fase final), `Sound.complete()` é chamado em `_advanceSection()` (linha 674). Para as fases intermediárias, o som do segmento só toca se o `tick*` renderizar um frame que aciona `drawSegs1` com `drawn >= w*.5` — o que pode não ocorrer se o avanço for rápido demais e `t_*` for zerado antes disso. Na prática é improvável, mas não garantido. |
| Único listener global — sem duplicatas | ✅ PASS | Um único `document.addEventListener('keydown',...)` em linha 625. Não há outros listeners de teclado no arquivo. |
| Sem `eval()`, sem variáveis globais desnecessárias | ✅ PASS | Sem `eval()`. `_advanceSection` e `_rewindSection` são declaradas como `function` no escopo global, mas são o único acréscimo — dentro do que o dev justificou nas notas. |
| Respeita `cancelAnimationFrame` e `requestAnimationFrame` existentes — sem frames duplicados | ✅ PASS | Toda `_advanceSection()` e `_rewindSection()` começa com `cancelAnimationFrame(id*)` antes de criar novo RAF. |
| Compatibilidade mobile (funcionalidade exclusiva de teclado) | ✅ PASS | Nenhuma alteração no HTML/CSS. Em mobile, o comportamento é idêntico ao anterior (sem impacto). |

**Placar Task 7.2: 11 ✅ PASS / 3 ⚠️ PARCIAL / 0 ❌ FAIL**

---

## Riscos Identificados

### Risco 1 — Race condition no `visibilitychange` (Médio)
**Onde:** linhas 268–275
**Cenário:** usuário troca de aba muito rapidamente (hidden → visible em menos de ~10ms). O `ctx.resume().then(_startHum)` do evento `visible` pode resolver _após_ uma `_stopHum()` disparada por um segundo evento `hidden`, deixando o hum ativo com a aba em background.
**Mitigação sugerida:** checar `!document.hidden` dentro do `.then()` antes de chamar `_startHum()`:
```js
Sound.ctx.resume().then(()=>{ if(!document.hidden) Sound._startHum(); });
```

### Risco 2 — Overflow de `ph1` para 5 no edge case de avanço (Baixo)
**Onde:** `_advanceSection()` linhas 672–673
**Cenário:** usuário pressiona a tecla exatamente quando `ph1=4` mas `done1` ainda não foi setado pelo ciclo natural do `tick1`. `ph1` vai para 5 e a animação entra em estado indefinido.
**Mitigação sugerida:** adicionar `if(ph1>=4)` antes do `ph1++` ou unificar com a condição de `done`:
```js
if(ph1<4){ ph1++; t_1=0; }
if(ph1>=4){ ph1=4; done1=true; Sound.complete(); }
```
(Padrão idêntico ao que já existe, mas com `>=4` no segundo `if`.)

### Risco 3 — `barsDone` não resetado ao retroceder Seção 3 (Baixo, comportamento intencional declarado)
**Onde:** `_rewindSection(3)`, linha 707
**Cenário:** ao retroceder para a fase 2 (barras) depois que barsDone=15, as barras aparecem silenciosas (sem som de `barTone`) porque `k < barsDone` para todos os k. O DEV documentou como decisão intencional.
**Recomendação:** aceitar para o deploy, mas registrar como Fase 8.

---

## Regressões

Verificação dos itens preexistentes nas Fases 1–6:

| Item | Status | Observação |
|---|---|---|
| Toggle PT/EN (`toggleLang()`) | ✅ Sem regressão | Função intacta nas linhas 189. `applyLang()` inalterada. |
| Botão SOM (`Sound.toggle()`) | ✅ Sem regressão | Método modificado apenas com adição de `_startHum()` / `_stopHum()`. Lógica original preservada. |
| Botões "Repetir" (`restart1`, `restart2`, `restart3`) | ✅ Sem regressão | Funções não foram alteradas. |
| Animações canvas Seção 1 (circunferência) | ✅ Sem regressão | `frame1`, `tick1`, `loop1` inalterados. |
| Animações canvas Seção 2 (área) | ✅ Sem regressão | `frame2`, `tick2`, `loop2` inalterados. |
| Animações canvas Seção 3 (Leibniz) | ✅ Sem regressão | `frame3`, `tick3`, `loop3` inalterados. |
| Sons de evento (`boot`, `segment`, `barTone`, `complete`, `mystical`) | ✅ Sem regressão | Métodos inalterados. GainNode separado garante isolamento do hum. |
| Badges (b0–b3, a0–a3, s0–s2) | ✅ Sem regressão | Lógica de ativação nos `drawSegs*` inalterada. |
| Background canvas animado | ✅ Sem regressão | IIFE independente, não tocada. |

**Nenhuma regressão detectada.**

---

## Recomendação Final

**Deploy pode ser realizado**, com a ressalva de corrigir o Risco 1 (race condition no `visibilitychange`) antes ou logo após o deploy, pois ele pode causar comportamento de áudio inesperado em dispositivos com troca rápida de abas (comum em mobile com gestos de swipe).

O Risco 2 (overflow de `ph1`) é de baixíssima probabilidade em uso real e pode ir para Fase 8.

**Resumo de itens a corrigir antes ou após deploy:**

| Prioridade | Item | Ação |
|---|---|---|
| Alta | Race condition `visibilitychange` (Risco 1) | Adicionar `if(!document.hidden)` no `.then()` |
| Baixa | Edge case `ph1=5` (Risco 2) | Trocar `if(ph1===4)` por `if(ph1>=4)` em `_advanceSection` |
| Backlog | `barsDone` não resetado ao retroceder S3 (Risco 3) | Fase 8 |
| Backlog | Som de segmento pode não tocar ao pular fase rápido (Task 7.2) | Fase 8 |
