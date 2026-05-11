# Spec — Pi Avengers

## Objetivo

Criar uma página web única, visual e educativa que demonstre a relação entre a constante π e a circunferência de um círculo, usando tema inspirado nos Vingadores da Marvel.

A página deve ser acessível via GitHub Pages sem necessidade de backend ou infraestrutura adicional.

---

## Problema

O conceito de π é abstrato para a maioria das pessoas. A relação `C = π × d` é memorizada, mas raramente compreendida visualmente. O objetivo é tornar essa relação intuitiva através de animação interativa.

---

## Requisitos

### Funcionais
- Demonstrar visualmente que o diâmetro cabe exatamente π vezes na circunferência
- Animar a "medição" da circunferência com o diâmetro como unidade
- Mostrar os segmentos: 1×d, 2×d, 3×d e o restante 0.14×d
- Exibir os dígitos de π e a fórmula `C = π × d = 2πr`
- Permitir repetir a animação a qualquer momento

### Visuais / Tema
- Tema Marvel/Avengers: cores, tipografia HUD, estética Stark Industries
- Círculo principal estilizado como Reator Arc do Iron Man (pulsante, com brilho)
- Grade hexagonal animada no fundo (referência ao HUD do Iron Man)
- Partículas flutuantes em vermelho e dourado
- Segmentos da circunferência coloridos por personagem:
  - Iron Man → vermelho `#e53935`
  - Capitão América → azul `#1976d2`
  - Thor → dourado `#f5a623`
  - Hulk → verde `#43a047`
- Narração da IA FRIDAY como legenda das fases

### Técnicos
- Implementação em um único arquivo `index.html` (HTML + CSS + JS inline)
- Sem dependências externas (sem frameworks, sem CDN)
- Compatível com GitHub Pages (branch `main`, raiz do repositório)
- Canvas 2D para todas as animações

---

## Casos de Uso

| Quem | Ação | Resultado esperado |
|---|---|---|
| Visitante | Acessa a URL do GitHub Pages | Vê a animação iniciar automaticamente |
| Visitante | Assiste até o fim | Entende que C = π × d visualmente |
| Visitante | Clica em "Reiniciar protocolo" | Animação recomeça do início |

---

## Fora do Escopo

- Backend ou banco de dados
- Múltiplas páginas
- Autenticação
- Suporte a dispositivos muito antigos (IE, Safari < 14)
- Imagens externas ou assets de terceiros (tudo desenhado via canvas/CSS)
- Uso de imagens ou logos oficiais da Marvel (evitar copyright)

---

## Ambiente

| Item | Valor |
|---|---|
| Repositório | `github.com/alanraldi/dev_user_claude` |
| URL pública | `https://alanraldi.github.io/dev_user_claude/` |
| Branch principal | `main` |
| Arquivo principal | `index.html` |
| Deploy | Automático via GitHub Pages a cada `git push` |
