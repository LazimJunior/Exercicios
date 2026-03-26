# Frontend Exercises Collection — Manipulação de DOM, Lógica de Estado e UX Interativa em Vanilla JS

[![JavaScript](https://img.shields.io/badge/JavaScript-ES6+-yellow.svg)](https://developer.mozilla.org/en-US/docs/Web/JavaScript)
[![HTML5](https://img.shields.io/badge/HTML5-Semântico-orange.svg)]()
[![CSS3](https://img.shields.io/badge/CSS3-Grid%20%2F%20Flexbox-blue.svg)]()

> Cinco aplicações web client-side em Vanilla JS cobrindo geração de números aleatórios sem repetição, gerenciamento de estado de aluguel via CSS class toggling, carrinho de compras com parsing de strings, sorteio por permutação (Fisher-Yates) e controle de estoque de ingressos com state management no DOM.

---

## Diferencial de Engenharia

### Gerador de Números Sem Repetição com Reset Automático (`Número Secreto`)

`gerarNumeroAleatorio()` implementa um sorteio **sem reposição** usando uma lista de controle (`listaDeNumerosSorteados`) e recursão com reset automático:

```javascript
if (quantidadeDeElementosNaLista == numeroLimite) {
    listaDeNumerosSorteados = []; // Esgotou o ciclo → reinicia
}
if (listaDeNumerosSorteados.includes(numeroEscolhido)) {
    return gerarNumeroAleatorio(); // Recursão até encontrar número não sorteado
}
```

A lógica garante que todos os `N` números sejam sorteados antes de qualquer repetição — equivalente a um embaralhamento lazy. O problema é que a recursão não tem limite explícito: em `numeroLimite` muito alto e com má sorte nas colisões, pode atingir o stack limit do JavaScript. Para escala pequena (≤ 10), é funcionalmente correto.

### Fisher-Yates Shuffle (`Amigo Secreto`)

`embaralhar()` implementa o algoritmo de Knuth (Fisher-Yates moderno) corretamente:

```javascript
for (let indice = lista.length; indice; indice--) {
    const indiceAleatorio = Math.floor(Math.random() * indice);
    [lista[indice - 1], lista[indiceAleatorio]] = [lista[indiceAleatorio], lista[indice - 1]];
}
```

Essa é a implementação canônica com complexidade O(n) e distribuição uniforme — superior à abordagem ingênua `sort(() => Math.random() - 0.5)`, que produz distribuições estatisticamente viesadas.

---

## Projetos

### 1. Número Secreto
Jogo de adivinhação com feedback direcional (maior/menor), contador de tentativas e síntese de voz via `responsiveVoice.js`. Estado gerenciado em variáveis de módulo no escopo global do script.

### 2. AluGames
Dashboard de aluguel de board games com toggle de estado via CSS class manipulation (`dashboard__item__img--rented`, `dashboard__item__button--return`). Estado persistido **exclusivamente no DOM** — não há objeto de dados; o estado é inferido pela presença/ausência de classes CSS.

### 3. Carrinho de Compras
Formulário de adição de produtos com cálculo de total acumulado. Parsing do preço extraído da string do `<option>` (`produto.split('R$')[1]`). Validação de quantidade com `isNaN` e comparação `<= 0`.

### 4. Amigo Secreto
Sorteio em cadeia circular (cada pessoa → próxima, último → primeiro) com shuffling Fisher-Yates antes da atribuição.

### 5. Ingresso (e-Ticket)
Controle de estoque de ingressos por categoria com decremento de disponibilidade no DOM. Estado de estoque persistido como `textContent` dos elementos `<span>` — o DOM é o banco de dados.

### 6. Sorteador de Números
Sorteio de `N` números em intervalo `[de, ate]` com exibição de resultados. **Contém bug crítico** (ver Análise de Trade-offs).

---

## Stack Tecnológica

| Tecnologia | Justificativa Técnica |
|---|---|
| **Vanilla JavaScript (ES6+)** | Sem framework — demonstra domínio direto de APIs do browser (DOM, eventos, escopo) sem abstração |
| **HTML5 semântico** | Uso de `<main>`, `<section>`, `<article>`, `<label for>` — acessibilidade básica estrutural |
| **CSS3 Flexbox/Grid** | Layout responsivo sem bibliotecas; `grid-template-columns` no carrinho, `flexbox` nos demais |
| **Google Fonts (CDN)** | Chakra Petch (display/headlines) + Inter (corpo) — carregamento via `<link preconnect>` com font-display implícito |
| **responsiveVoice.js (CDN)** | Síntese de voz para acessibilidade no Número Secreto — dependência de CDN externo sem fallback |

---

## Arquitetura & Fluxo de Dados

### Padrão recorrente: DOM como fonte de verdade

Quatro dos cinco projetos usam o DOM diretamente como storage de estado, sem objeto JavaScript intermediário:

```
Evento de usuário (onclick)
        │
        ▼
Leitura do estado atual via querySelector / getElementById
        │
        ▼
Cálculo / transformação em memória (variável local)
        │
        ▼
Escrita do novo estado no DOM (innerHTML / textContent / classList)
```

**Exceção:** `Amigo Secreto` e `Número Secreto` mantêm arrays no escopo do módulo (`amigos[]`, `listaDeNumerosSorteados[]`) — estado real fora do DOM.

### Fluxo do Carrinho

```
<select> produto + <input> quantidade
        │
        ▼ adicionar()
Extração: nomeProduto = split('-')[0]
          valorUnitario = parseFloat(split('R$')[1])
          preco = qtd * valorUnitario
        │
        ▼
innerHTML += template string com linha do produto
totalGeral += preco
campoTotal.textContent = `R$ ${totalGeral}`
```

---

## Guia de Setup

Todos os projetos são **client-side puros** — nenhuma dependência de build tool ou servidor.

```bash
# Clone o repositório
git clone https://github.com/<seu-usuario>/frontend-exercises.git

# Abra qualquer projeto diretamente no browser
# Opção 1: Live Server (VS Code extension recomendada)
# Opção 2: servidor HTTP mínimo
cd "Número secreto"
python -m http.server 5500
# Acesse: http://localhost:5500
```

> **Dependência externa:** `Número Secreto` requer conexão com internet para `responsiveVoice.js`. Sem conexão, a síntese de voz falha silenciosamente mas o jogo funciona.

---
