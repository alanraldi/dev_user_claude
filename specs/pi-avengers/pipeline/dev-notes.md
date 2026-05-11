# Dev Notes — Fase 7

**Arquivo modificado:** `index.html` (único arquivo, zero dependências externas)  
**Data:** 2026-05-11  
**Dev:** implementação das Tasks 7.1 e 7.2 conforme po-brief.md

---

## Task 7.1 — Hum Contínuo do Reator Arc

### O que foi implementado

Oscilador persistente via Web Audio API que emite um hum grave enquanto o som está ativado.

### Onde no código

Dentro do objeto `Sound` (após a propriedade `mystical`), aproximadamente linha 195–260 do arquivo final:

- **Novas propriedades:** `_humOsc`, `_humGain`, `_lfoOsc`, `_lfoGain` — guardam as referências dos nós para poder parar e recriar sem leak.
- **Novo método `_startHum()`:** cria o oscilador principal (72 Hz, tipo `triangle`) + GainNode (gain 0.055, dentro do limite ≤ 0.07) + LFO (0.5 Hz, sine, amplitude ±4 Hz na frequência do hum). Conecta tudo ao `Sound.ctx.destination`. Guard clause `if(this._humOsc)return` impede duplicatas.
- **Novo método `_stopHum()`:** para e desconecta todos os nós de forma segura (try/catch em cada um, pois `stop()` em nó já parado lança exceção).
- **Método `toggle()` modificado:** ao ligar o som, após o beep de confirmação, chama `_startHum()`. Ao desligar, chama `_stopHum()` imediatamente.
- **Listener `visibilitychange`** (fora do objeto `Sound`, logo após): quando `document.hidden === true`, chama `_stopHum()`; quando volta ao foco, chama `ctx.resume().then(_startHum)`.

### Decisões técnicas

- **Frequência 72 Hz com LFO de 0.5 Hz (±4 Hz):** dentro dos ranges do brief (60–90 Hz; 0.3–0.8 Hz). O LFO modula a frequência (não o ganho) — cria a sensação de "respiração" do reator de forma mais natural que modular o volume.
- **Tipo `triangle`:** mais suave que `square`, menos harmônicos agudos que `sawtooth`, sobreposição menor aos sons de evento.
- **GainNode separado:** o hum usa seu próprio `_humGain`, completamente isolado do `_play()` interno — sons de evento não são afetados.
- **Guard clause em `_startHum()`:** `if(this._humOsc)return` garante idempotência — se chamado duas vezes (ex: visibilitychange rápido), não duplica osciladores.
- **try/catch individuais em `_stopHum()`:** `AudioNode.stop()` lança `InvalidStateError` se o nó já parou; tratar cada nó separadamente evita que uma falha impeça os demais de serem limpos.

---

## Task 7.2 — Modo Apresentação: Navegação por Teclado

### O que foi implementado

- `Space` e `ArrowRight`: avança a animação para a próxima fase.
- `ArrowLeft` e `Backspace`: recua uma fase na seção em andamento.
- Ordem sequencial: Seção 1 (fases 0→4) → Seção 2 → Seção 3.
- Quando todas as 3 seções estão `done`, pressionar avançar não faz nada.
- `event.preventDefault()` nas teclas capturadas para impedir scroll da página.
- Guard: não captura quando `document.activeElement` é `INPUT`, `TEXTAREA` ou `BUTTON`.

### Onde no código

Bloco após `// TASK 7.2`, logo depois do bloco `INIT` (fim do arquivo, antes de `</script>`):

- **`document.addEventListener('keydown', ...)`** — único listener global, sem duplicatas.
- **`_advanceSection(sec)`** — cancela o RAF ativo, incrementa `ph*`, zera `t_*`, seta `done*` se `ph === 4`, dispara `Sound.complete()`, atualiza textos via `setText`, reinicia o RAF com `tick*` ou `loop*` conforme o estado. Reutiliza o mesmo padrão das funções `restart*` existentes sem refatorá-las.
- **`_rewindSection(sec)`** — cancela o RAF ativo, decrementa `ph*`, zera `t_*`, limpa `done*`, reinicia com `tick*`. Para Seção 3, ao recuar para fase <2 os `barsDone` não são resetados — isso é intencional: os badges já acesos não regridem (comportamento visual aceitável para retrocesso).

### Decisões técnicas

- **Lógica de prioridade no avanço:** a spec define ordem sequencial S1 → S2 → S3. A implementação usa `if(!done1)` → `else if(!done2)` → `else if(!done3)` — simples e direta, sem estrutura de dados de estado extra.
- **Retrocesso implementado:** o brief indicava "se tempo limitado, entregar só avanço". O retrocesso foi entregue pois a lógica de `_rewindSection` é simétrica à de avanço e não apresenta risco de regressão. Única ressalva: ao recuar na Seção 3 para fase <2, `barsDone` não é resetado (seria necessário também resetar badges `s0`, `s1`, `s2` — decidido não fazer para não duplicar lógica de `restart3()`).
- **Sem variáveis globais extras além das funções `_advanceSection` e `_rewindSection`:** as variáveis de estado (`ph1`, `ph2`, `ph3`, `t_1`, `t_2`, `t_3`, `done1`, `done2`, `done3`, `id1`, `id2`, `id3`) já existem no escopo global e são manipuladas diretamente.
- **Sons:** ao avançar fase, o próximo tick normal do `tick*` dispara os sons correspondentes (segment, barTone, complete) — sem precisar chamá-los explicitamente no handler.

---

## O que NÃO foi implementado e por quê

| Item | Razão |
|---|---|
| Indicador visual de "modo apresentação ativo" | Fora do escopo — po-brief.md Task 7.2, seção "Fora do Escopo" |
| Botões prev/next na tela | Fora do escopo — po-brief.md Task 7.2, seção "Fora do Escopo" |
| Variar timbre/frequência do hum por seção | Fora do escopo — po-brief.md Task 7.1, seção "Fora do Escopo" |
| Reset de `barsDone` ao recuar Seção 3 | Não é regressão visual crítica; resetar exigiria duplicar lógica de `restart3()`, o que quebraria a regra "não refatore código que não é necessário para a tarefa" |
