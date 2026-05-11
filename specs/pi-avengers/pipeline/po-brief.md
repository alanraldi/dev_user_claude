# PO Brief — Fase 7: Pi Avengers

**Projeto:** Pi Avengers — página educativa sobre π, tema Marvel/Avengers  
**Arquivo único:** `index.html` (HTML + CSS + JS inline, sem build, sem dependências externas)  
**Publicado em:** https://alanraldi.github.io/dev_user_claude/  
**Data:** 2026-05-11  
**Status das fases anteriores:** 1–6 concluídas. Não há regressões conhecidas.

---

## Task 7.1 — Sons Contínuos: Hum do Reator Arc

### O que é

Enquanto a página estiver aberta e o som estiver ativado, o Reator Arc emite um hum grave e contínuo — como o reactor de energia do Tony Stark em operação. O som deve iniciar assim que o usuário ativar o áudio (botão "SOM") e persistir durante toda a sessão, independente de qual animação está rodando. Quando o usuário silenciar (mesmo botão), o hum para imediatamente.

### User Story

Como visitante da página, quero ouvir um hum grave e constante enquanto o Reator Arc está "ligado", para sentir a imersão no universo Avengers e reforçar que o reator está em operação contínua.

### Critérios de Aceitação

- [ ] O hum inicia quando o usuário clica em "SOM" (muted → unmuted), logo após o beep de confirmação já existente
- [ ] O hum é um oscilador de baixa frequência (recomendado: 60–90 Hz, tipo `sine` ou `triangle`) com ganho baixo (≤ 0.07) para não sobrepor os sons de eventos
- [ ] O hum aplica um LFO (Low Frequency Oscillator) de amplitude sutil na frequência ou no ganho, criando variação orgânica (pulsação lenta, ~0.3–0.8 Hz) — sem isso o som fica artificial demais
- [ ] O hum para instantaneamente quando o usuário clica em "SOM" para silenciar (unmuted → muted)
- [ ] O hum para quando a aba/página perde o foco (`visibilitychange` → hidden) e retoma quando volta ao foco
- [ ] Sons de eventos (boot, segment, barTone, complete, mystical) continuam funcionando normalmente sobre o hum — o hum não os abafa
- [ ] Em dispositivos móveis, o hum só começa após interação do usuário (requisito do browser, já satisfeito pelo fluxo de clique no botão SOM)
- [ ] O hum não cria loop de feedback (não chama `restart*` nem interfere com as animações canvas)
- [ ] Ao religar o som após silenciar, o hum retoma junto com o beep de confirmação

### Restrições Técnicas

- Deve usar a `Web Audio API` já inicializada em `Sound.ctx` — não criar um segundo `AudioContext`
- O oscilador do hum deve ser criado como nó persistente (não recriado a cada frame), conectado ao `Sound.ctx.destination`
- O `Sound.ctx` só pode ser criado/retomado dentro de um evento de usuário — o código atual já garante isso via `Sound.toggle()`; não quebrar esse fluxo
- Sem dependências externas; sem arquivos de áudio (`.mp3`, `.ogg`) — apenas Web Audio API
- O volume do hum deve ser controlado via `GainNode` separado para não interferir no ganho dos sons de evento
- Manter a estrutura do objeto `Sound` já existente — adicionar métodos/propriedades internamente sem alterar a assinatura pública

### Fora do Escopo

- Variar o timbre ou frequência do hum entre as 3 seções (circunferência, área, Leibniz)
- Adicionar camadas de som (ex: hum do portal da seção 3 ser diferente do reator) — se desejado, será Fase 8
- Equalização ou efeitos de reverb/delay
- Qualquer controle de volume além do botão mute/unmute já existente

### Estimativa de Complexidade

**Baixa.** A Web Audio API já está inicializada. São ~25–35 linhas de JS adicionadas dentro do objeto `Sound`.

---

## Task 7.2 — Modo Apresentação: Navegação por Teclado

### O que é

O usuário consegue avançar as fases de cada animação pressionando `Espaço` ou `→` (seta direita) no teclado — como se estivesse em modo de apresentação (semelhante ao PowerPoint). Cada tecla avança para a próxima fase disponível, seguindo a ordem: Seção 1 fase 1→2→3→4→5, depois Seção 2 fase 1→2→3→4→5, depois Seção 3 fase 1→2→3→4→5. Quando todas as fases de todas as seções já foram exibidas, pressionar novamente não faz nada (ou reinicia — ver critérios).

### User Story

Como professor ou apresentador, quero avançar as animações da página com a barra de espaço ou seta do teclado, para conduzir uma apresentação ao vivo sem precisar clicar com o mouse.

### Critérios de Aceita­ção

- [ ] Pressionar `Espaço` ou `ArrowRight` avança a animação ativa para a próxima fase
- [ ] A ordem de avanço é sequencial entre seções: todas as fases da Seção 1 primeiro, depois Seção 2, depois Seção 3
- [ ] "Avançar fase" significa pular diretamente para a fase seguinte (`ph1`, `ph2` ou `ph3`) sem esperar o temporizador natural (`t_1`, `t_2`, `t_3`) acabar
- [ ] O avanço redefine `t_X = 0` e `ph_X++` para que a nova fase rode normalmente a partir do início
- [ ] Se a animação da seção corrente já completou todas as 5 fases (`done*` = true), o foco passa para a próxima seção
- [ ] Se todas as 3 seções estiverem completas, pressionar a tecla não faz nada (sem loop infinito)
- [ ] `ArrowLeft` ou `Backspace` volta uma fase (fase anterior na seção atual; se já for fase 0, volta para a seção anterior no estado de conclusão)
  - *Nota para o dev:* retroceder é mais complexo que avançar; se o tempo for limitado, entregar apenas o avanço e registrar o retrocesso como Fase 8
- [ ] O evento de teclado só dispara quando `document.activeElement` não for um `<input>`, `<textarea>` ou `<button>` com foco — para não interferir com acessibilidade
- [ ] Pressionar `Espaço` não rola a página (`event.preventDefault()` quando o modo apresentação captura a tecla)
- [ ] O modo de apresentação funciona junto com o toggle de idioma e o botão de som — essas teclas/cliques continuam operacionais
- [ ] Sons de avanço de fase tocam normalmente ao pular (o som `Sound.boot()` ou o som do segmento correspondente, conforme já acontece ao reiniciar)

### Restrições Técnicas

- Usar `document.addEventListener('keydown', handler)` — apenas um listener global, sem listeners duplicados
- Não usar `eval()`, não criar variáveis globais desnecessárias além de um objeto/flag de controle de estado
- As funções `restart1()`, `restart2()`, `restart3()` já existem e devem ser reaproveitadas para reinício; para avançar fase, criar lógica separada que manipula `ph*` e `t_*` diretamente
- A lógica de avanço deve respeitar os `cancelAnimationFrame` e `requestAnimationFrame` existentes — não criar frames duplicados
- Manter compatibilidade com mobile: o modo apresentação é funcionalidade exclusiva de teclado (desktop/laptop), sem necessidade de fallback touch nesta fase
- Arquivo único, zero dependências externas

### Fora do Escopo

- Botões de navegação visíveis na tela (prev/next) — se solicitado, será Fase 8
- Atalho para reiniciar todas as seções de uma vez
- Avanço automático com temporizador (autoplay mode)
- Indicador visual de "modo apresentação ativo" (ex: barra de progresso no rodapé)
- Suporte a controle remoto de apresentação (Bluetooth/USB) — se necessário, já funciona via `ArrowRight` nativamente no OS

### Estimativa de Complexidade

**Média.** O estado das animações (ph1, ph2, ph3, t_1, t_2, t_3, done1, done2, done3) está em variáveis globais e pode ser manipulado diretamente. O principal cuidado é garantir que os `requestAnimationFrame` em andamento não se multipliquem. ~40–60 linhas de JS.

---

## Dependências entre Tasks

As duas tasks são independentes entre si. Podem ser implementadas em paralelo ou em qualquer ordem. Nenhuma delas altera HTML ou CSS — são adições puramente em JavaScript dentro do bloco `<script>`.

## Critérios de "Pronto" (Definition of Done)

- Testado no Chrome e Firefox desktop
- Testado no Chrome mobile (iOS ou Android) — especialmente que o hum não toca sozinho sem interação
- Nenhuma das 3 animações canvas apresenta regressão visual
- Botão SOM, toggle PT/EN e botões "Repetir" continuam funcionando
- Commit no branch `main` com descrição clara
- Deploy verificado no GitHub Pages: https://alanraldi.github.io/dev_user_claude/
