# 🧩 ZIP — Pixel Puzzle

> Um jogo de caminho hamiltoniano com visual retrô pixel art, geração procedural de fases e suporte completo a mouse e toque.

---

## 🎮 Como Jogar

1. **Clique no número `1`** para iniciar o traço.
2. **Arraste** o cursor (ou o dedo) para desenhar um caminho pelas células do grid.
3. O caminho deve **passar pelos números marcados em ordem crescente** (1 → 2 → 3…).
4. Você precisa **preencher 100% das células** do grid para vencer.
5. Não é permitido pular células nem sobrepor o próprio traço (voltar corta o traço).

### ⌨️ Atalhos de Teclado

| Tecla | Ação |
|-------|------|
| `Z` | Desfazer última célula |
| `R` | Reiniciar nível |
| `N` | Avançar para o próximo nível |
| `ESC` | Cancelar arraste |

---

## 🗂️ Estrutura do Projeto

```
zip_pixel.html          ← Arquivo único autocontido (sem dependências externas)
```

O jogo é um **arquivo HTML standalone** — não requer servidor, build ou instalação. Basta abrir no navegador.

---

## ✨ Funcionalidades

- **Geração procedural** de fases via algoritmo hamiltoniano com DFS + backtracking
- **3 níveis de dificuldade**: Fácil (4×4 → 6×6), Médio (5×5 → 7×7), Difícil (6×6 → 7×7)
- **Progressão de grid**: o tamanho aumenta conforme o jogador avança nos níveis
- **Cronômetro por fase** com destaque visual ao ultrapassar 2 minutos
- **Melhor tempo** salvo por nível/dificuldade, com badge de recorde ao bater o tempo
- **Progresso persistido**: nível e dificuldade salvos em `localStorage` entre sessões
- **Tema claro/escuro** com persistência em `localStorage`
- **Suporte a touch** (mobile e tablet) com prevenção de scroll acidental
- **Fonte retrô** [Press Start 2P](https://fonts.google.com/specimen/Press+Start+2P) via Google Fonts
- **Favicon** gerado dinamicamente em canvas

---

## 🏗️ Arquitetura Técnica

### Gerador de Puzzles

```
generateHamiltonianPath(size)
  └─ DFS com backtracking + Fisher-Yates shuffle
  └─ Até 80 tentativas com ponto de partida aleatório
  └─ Retorna caminho que cobre todas as N×N células

generatePuzzle(size, waypointDensity)
  └─ Chama generateHamiltonianPath
  └─ Distribui waypoints uniformemente ao longo do caminho
  └─ Retorna { numbers, fullPath, maxNumber }
```

### Fluxo de Estado

```
loadPuzzle()          → gera puzzle, reseta estado, inicia display
  startDraw(r,c)      → inicia caminho no número 1, inicia cronômetro
    continueDraw(r,c) → expande ou recorta caminho, verifica vitória
    stopDraw()        → finaliza arraste
  checkVictory()      → verifica tamanho do caminho + ordem dos waypoints
  undo() / reset()    → volta estado, reseta timer se necessário
```

### Módulos de Persistência

| Chave `localStorage` | Conteúdo |
|----------------------|----------|
| `zip-save` | `{ level, difficulty }` — progresso atual |
| `zip-theme` | `"dark"` ou `"light"` — tema salvo |
| `zip-best-{diff}-{level}` | Melhor tempo em segundos por nível/dificuldade |

---

## 🐛 Correções Aplicadas (v2)

| # | Problema | Solução |
|---|----------|---------|
| 1 | `getComputedStyle` chamado dentro de loops de renderização (O(n²)) | Variáveis CSS lidas uma única vez em `getCSSVars()` antes dos loops |
| 2 | Fonte `Press Start 2P` podia não estar carregada ao renderizar o canvas | `document.fonts.ready.then(...)` garante carregamento antes do primeiro render |
| 3 | `loadPuzzle()` recursivo sem limite poderia loop infinito | Contador `retries` com máximo de 8 tentativas |
| 4 | `checkVictory()` não verificava a ordem real dos waypoints no caminho | Validação percorre o caminho verificando índices crescentes por waypoint |
| 5 | Nenhum salvamento de progresso entre sessões | `saveProgress()` / `loadProgress()` via `localStorage` |
| 6 | `canvas` e `ctx` declarados duas vezes (global + `setupEvents`) | Declaração única no escopo global, atribuição em `setupEvents()` |
| 7 | Sem feedback de tempo ou recorde | Cronômetro por fase + melhor tempo persistido com badge de recorde |

---

## 🚀 Rodando o Jogo

Nenhuma instalação necessária. Apenas:

```bash
# Opção 1: abrir diretamente no navegador
open zip_pixel.html

# Opção 2: servidor local simples (opcional)
python3 -m http.server 8080
# acesse http://localhost:8080/zip_pixel.html
```

Compatível com Chrome, Firefox, Safari e Edge (desktop e mobile).

---

## 🛠️ Possíveis Melhorias Futuras

- [ ] Animação de vitória com partículas no canvas
- [ ] Placar global com Web Share API
- [ ] Modo desafio diário (seed fixo por data)
- [ ] Histórico de fases concluídas
- [ ] Efeitos sonoros com Web Audio API
- [ ] PWA (instalável como app via manifest + service worker)

---

## 📄 Licença

Projeto de uso livre para fins educacionais e pessoais.
