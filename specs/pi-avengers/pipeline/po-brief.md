# PO Brief — Fase 7: Fonte Científica do Pi

**Data:** 2026-05-12
**Tarefas cobertas:** Fase 7 — Task: "Incluir fonte científica da variável 'pi'"
**Arquivo alvo:** `index.html`

---

## Contexto

O Pi Avengers é uma página educativa single-file que demonstra visualmente os conceitos matemáticos de π: circunferência (C = π × d), área (A = πr²) e a Série de Leibniz. A página já está completa em termos visuais e funcionais — animações, sons, internacionalização PT/EN e modo de apresentação estão implementados.

A única tarefa pendente da Fase 7 é incluir uma referência científica para a constante π. A ausência dessa referência é uma lacuna educativa: a página ensina com excelência o que π representa geometricamente, mas não informa ao visitante de onde vem o valor numérico 3.14159… nem qual é a fonte acadêmica ou científica que o define. Para uma página com propósito educativo, citar a fonte legitima o conteúdo e aumenta a credibilidade.

A implementação deve seguir o tema visual Avengers/HUD já estabelecido (fundo escuro, fontes Courier New, cores vermelho `#e53935` e dourado `#f5a623`), ser internacionalizada (PT e EN), e residir no rodapé natural da página — após o bloco de dígitos de π que já existe na linha 126 do HTML.

---

## Tarefa 7.3 — Incluir Fonte Científica da Variável "pi"

### Descrição

Adicionar uma pequena seção de "Referências" abaixo do bloco de dígitos de π já existente (`.digits`, linha 126). A seção deve exibir duas referências numeradas e internacionalizadas que identificam a constante π: uma matemática clássica (Wolfram MathWorld ou NIST DLMF) e uma enciclopédica de acesso público (Wikipedia). O texto deve seguir o visual HUD da página e ser atualizado pelo sistema de internacionalização já existente (`T = {pt:{…}, en:{…}}` + `applyLang()`).

### User Story

Como visitante que acabou de assistir a animação, quero saber qual é a fonte científica do valor de π, para que eu possa verificar a informação e citar a página com confiança em trabalhos escolares.

### Critérios de Aceitação

- [ ] Uma seção `<div id="refs">` aparece abaixo do elemento `.digits` (linha 126) com o título "REFERÊNCIAS" (PT) / "REFERENCES" (EN) em estilo HUD
- [ ] Duas referências são exibidas, numeradas com `[1]` e `[2]`, em fonte Courier New, tamanho `clamp(.62rem, 1.8vw, .72rem)`, cor `#7a8eaa` (mesma do `.explain`)
- [ ] Referência `[1]` aponta para o Wolfram MathWorld — Pi: `https://mathworld.wolfram.com/Pi.html` — com texto âncora visível e link funcional (abre em nova aba, `target="_blank" rel="noopener"`)
- [ ] Referência `[2]` aponta para a Wikipedia (versão PT em PT, versão EN em EN): PT → `https://pt.wikipedia.org/wiki/Pi`, EN → `https://en.wikipedia.org/wiki/Pi` — com texto âncora e link funcional
- [ ] Os textos das referências (rótulos e links) são definidos no objeto `T` do JS, nas chaves `pt` e `en`, e aplicados pela função `applyLang()` via `setHTML()` — nenhum texto de referência está hardcoded em HTML
- [ ] A seção tem um separador visual sutil acima (linha fina idêntica ao `.hr` existente, mas de largura 100% do container) para delimitar a área de referências
- [ ] Os links têm a cor dourada `#f5a623` em estado normal e `#e53935` em hover, via CSS inline ou classe utilitária
- [ ] A seção é visível e legível em mobile (viewport 375px), com quebra de linha natural — nenhum elemento transborda horizontalmente
- [ ] Ao trocar de idioma (botão PT/EN), os textos e links das referências atualizam corretamente junto com o restante da página
- [ ] Nenhuma animação canvas, nenhum som e nenhum botão existente são afetados pela adição

### Localização no código

- **HTML — onde inserir:** linha 127, imediatamente após o `</div>` que fecha `.digits` (linha 126: `<div class="digits">…</div>`) e antes do `</div>` que fecha `.wrap` (linha 127)
- **CSS — onde inserir:** dentro do bloco `<style>` existente (linha 7–63), após a linha 63 (antes de `</style>`), com as classes `.refs` e `.refs a`
- **JS — objeto T:** adicionar as chaves `refTitle`, `ref1`, `ref2` dentro de `pt:{…}` (inicia na linha 134) e `en:{…}` (inicia na linha 152)
- **JS — função `applyLang()`:** linha 176–188, adicionar chamada `setHTML('refs', ...)` no final do bloco, após a linha 187
- **Variáveis relacionadas:** `T` (objeto de tradução), `applyLang()`, `setHTML()`, `lang`
- **Padrão existente a seguir:** o elemento `<div class="digits">` (linha 126) e o `.fcard` (linha 116–119) são os elementos mais próximos visualmente — a seção de referências deve ter aparência mais discreta que o `fcard`, sem borda decorativa de cantos

### Conteúdo exato das strings a implementar no objeto `T`

**PT:**
```
refTitle: 'REFERÊNCIAS',
ref1: '[1] Weisstein, Eric W. <a href="https://mathworld.wolfram.com/Pi.html" target="_blank" rel="noopener">Pi — Wolfram MathWorld</a>',
ref2: '[2] <a href="https://pt.wikipedia.org/wiki/Pi" target="_blank" rel="noopener">Pi — Wikipédia</a>',
```

**EN:**
```
refTitle: 'REFERENCES',
ref1: '[1] Weisstein, Eric W. <a href="https://mathworld.wolfram.com/Pi.html" target="_blank" rel="noopener">Pi — Wolfram MathWorld</a>',
ref2: '[2] <a href="https://en.wikipedia.org/wiki/Pi" target="_blank" rel="noopener">Pi — Wikipedia</a>',
```

### HTML a inserir (após a linha 126, antes do `</div>` da `.wrap`)

```html
<div class="refs">
  <div class="refs-title" id="refs-title"></div>
  <div id="refs-body"></div>
</div>
```

### CSS a inserir (dentro do `<style>`, após linha 62)

```css
.refs{width:100%;text-align:center;margin-top:10px;padding-top:10px;border-top:1px solid rgba(229,57,53,.18)}
.refs-title{font-size:clamp(.55rem,1.6vw,.65rem);letter-spacing:3px;color:rgba(229,57,53,.5);text-transform:uppercase;margin-bottom:6px}
.refs-body{font-size:clamp(.62rem,1.8vw,.72rem);color:#7a8eaa;line-height:1.8}
.refs a{color:#f5a623;text-decoration:none}
.refs a:hover{color:#e53935}
```

**Nota ao dev:** os IDs corretos no HTML são `refs-title` e `refs-body`. No `applyLang()`, usar `setText('refs-title', l.refTitle)` e `setHTML('refs-body', l.ref1 + '<br>' + l.ref2)`.

### Justificativa da escolha das fontes

| Fonte | Motivo |
|---|---|
| Wolfram MathWorld — Pi | Referência matemática rigorosa, peer-reviewed, amplamente citada em trabalhos acadêmicos. Específica para a constante π com definições, fórmulas e histórico. |
| Wikipedia PT/EN | Acessível ao público geral, multilíngue, cobre o histórico e as definições de forma didática — alinhada ao público-alvo da página (estudantes e curiosos). |

Fontes descartadas por este brief:
- NIST DLMF: URL complexa e menos familiar ao público não-especialista.
- Britannica: requer conta para artigos completos.

### Restrições técnicas

- Não criar arquivo CSS separado — o projeto é single-file
- Não usar CDN nem fontes externas de ícones
- Não alterar a estrutura dos canvases, dos objetos `Sound`, `T` (exceto adição de chaves), nem das funções `restart1/2/3()`, `tick1/2/3()`, `loop1/2/3()`
- O bloco `<script>` começa na linha 129 — o HTML da seção de referências deve ser inserido **antes** do `<script>`, dentro do `.wrap`
- Links externos devem sempre ter `rel="noopener"` por segurança (evita `window.opener` no site de destino)
- Manter `width: 100%; max-width: 780px` — o `.wrap` já cuida do container, nenhum novo limite de largura é necessário

### Fora do escopo desta tarefa

- Adicionar uma terceira ou quarta referência (ex: livros físicos, artigos de jornal)
- Criar uma seção "Sobre" ou "Créditos" além das referências
- Adicionar link para o repositório GitHub na página
- Alterar o layout do header, dos cards de animação ou dos botões de controle
- Tocar som ao clicar nos links de referência
- Qualquer alteração visual nas 3 animações canvas

---

## Dependências entre tarefas

Esta é a única tarefa pendente no backlog. Não há dependência com nenhuma outra tarefa anterior ou futura.

```
Fase 7 — Task 7.3: Fonte científica do pi
  Depende de: nenhuma
  Bloqueia: nenhuma
  Ordem de implementação: única
```

## O que NÃO mudar

As seguintes funções e blocos devem ser preservados intactos:

- `Sound` — objeto completo com hum, LFO, toggle, beeps e fanfare
- `restart1()`, `tick1()`, `loop1()` — animação da circunferência
- `restart2()`, `tick2()`, `loop2()` — animação da área
- `restart3()`, `tick3()`, `loop3()` — animação da série de Leibniz
- `_advanceSection()`, `_rewindSection()` — modo apresentação por teclado
- `applyLang()` — pode receber linhas adicionais no final, mas as linhas existentes (178–187) não devem ser alteradas
- `T.pt.f1/f2/f3`, `T.pt.e1/e2/e3`, `T.en.f1/f2/f3`, `T.en.e1/e2/e3` — strings de narração FRIDAY e WONG
- Todo o CSS existente nas linhas 8–62
- Estrutura do HTML das seções 1, 2 e 3 (cards, canvases, badges, explain)
- `<div class="digits">` — linha 126, não alterar conteúdo
