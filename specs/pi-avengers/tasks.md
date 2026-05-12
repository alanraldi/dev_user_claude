# Tasks — Pi Avengers

> Convenção: `[x]` = concluído · `[ ]` = pendente · `[-]` = descartado/substituído

---

## Fase 0 — Configuração do Ambiente

- [x] Configurar `user.name` e `user.email` no git global (`alanraldi` / `alanraldi@gmail.com`)
- [x] Definir branch padrão como `main` no git global
- [x] Confirmar remote apontando para `github.com/alanraldi/dev_user_claude`
- [x] Habilitar GitHub Pages via API (branch `main`, raiz `/`)
- [x] Verificar que o deploy automático funciona a cada `git push`

---

## Fase 1 — Estrutura e Organização Local

- [x] Renomear `index.htm` → `index.html`
- [x] Remover arquivo duplicado `index - Copia.htm`
- [x] Confirmar que o VSCode mostra os arquivos corretamente no Explorer
  - Causa do problema: seção "OPEN EDITORS" ocupava toda a área, escondendo a pasta
  - Solução: `Workbench: Reset View Locations` via Command Palette

---

## Fase 2 — Primeira Versão da Animação (descartada)

- [-] Círculo rolando sobre uma linha (cicloide)
  - Descartado: visualmente confuso, difícil associar o início do círculo com π
  - Substituído pela abordagem de "desenrolamento"

---

## Fase 3 — Segunda Versão da Animação (base funcional)

- [x] Círculo fixo à esquerda com raio `r` e diâmetro `d` rotulados
- [x] Circunferência se "desenrola" para a direita como uma linha reta
- [x] Linha dividida em segmentos coloridos mostrando quantos diâmetros cabem
  - 1×d, 2×d, 3×d e o restante 0.14×d
- [x] Chave (`brace`) abaixo da linha com label `C = π × d`
- [x] Fórmula e dígitos de π exibidos abaixo do canvas
- [x] Botão "Repetir animação"

---

## Fase 4 — Tema Avengers (versão atual)

- [x] Fundo com grade hexagonal animada (estilo HUD Stark Industries)
- [x] Partículas flutuantes em vermelho e dourado
- [x] Círculo substituído por Reator Arc pulsante (anéis, raios, núcleo com brilho)
- [x] Feixe de energia azul no lugar da linha simples
- [x] Segmentos coloridos por personagem (Iron Man, Cap, Thor, Hulk)
- [x] Badges dos personagens que acendem conforme o segmento é preenchido
- [x] Narração da IA FRIDAY como legenda de cada fase
- [x] Interface HUD: cantos decorativos, labels de fase, crédito Stark Industries
- [x] Fase de boot com scanner animado antes da animação principal
- [x] Reator Arc continua pulsando na tela final (loop infinito)

---

## Fase 5 — Melhorias Implementadas

- [x] Tornar o canvas responsivo para mobile (`width: 100%` + `clamp()` em fontes e espaçamentos)
- [x] Adicionar painel de explicação por fase (texto educativo abaixo de cada canvas, atualiza a cada fase)
- [x] Internacionalização: toggle PT/EN (botão no header, todas as strings em objeto separado)
- [x] Adicionar segundo conceito: área do círculo `A = πr²` como nova seção
  - Segunda animação independente com Reator Arc, segmentos coloridos por personagem e fórmula final
  - Badges dos personagens para a seção de área
  - Narração FRIDAY separada para cada conceito

## Fase 6 — Implementada

- [x] Sons via Web Audio API com botão mute/unmute no header
  - Boot beep (ascending), tons por segmento de personagem, beep por barra de Leibniz, fanfare de conclusão, som místico para o portal
- [x] Terceira seção: Série de Leibniz com tema Doutor Estranho / Kamar-Taj
  - Portal animado com anéis giratórios (roxo/laranja), faíscas e núcleo dourado
  - Barras animadas mostrando termos +1, −1/3, +1/5, −1/7... convergindo para π/4
  - Linha tracejada indicando o alvo π/4 ≈ 0.785
  - Ponto dourado rastreando a soma parcial
  - Resultado final: soma × 4 = π ≈ 3.14159
  - Narrador "WONG AI" no lugar do FRIDAY
  - Badges: Termos positivos, Termos negativos, Convergência → π
  - Sons místicos ao reiniciar

## Fase 7 — Backlog

- [x] Sons contínuos: reator Arc com hum grave enquanto a página está aberta
- [x] Modo apresentação: avança fases com barra de espaço ou seta
- [x] Incluir fonte cientifica da variavel "pi"

---

## Histórico de Decisões

| Data | Decisão | Motivo |
|---|---|---|
| 2026-05-09 | Usar arquivo único `index.html` | Simplicidade, sem build, compatível com GitHub Pages |
| 2026-05-09 | Abandonar cicloide (círculo rolando) | Difícil de associar visualmente ao valor de π |
| 2026-05-09 | Adotar "desenrolamento + segmentos" | Mais claro: mostra diretamente quantos `d` cabem na circunferência |
| 2026-05-10 | Aplicar tema Avengers | Tornar a página mais atrativa e memorável |
| 2026-05-10 | Tudo desenhado via canvas/CSS, sem assets externos | Evitar problemas de copyright com imagens Marvel |
