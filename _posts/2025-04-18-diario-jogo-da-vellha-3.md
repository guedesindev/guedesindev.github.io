---
title: "Regras de Segurança Firebase Realtime Database"
date: 2025-04-21
description: "Depois de alterar vários pontos do código e verificar o funcionamento, nada adiantou até entender as regras de segurança do Firebase."
categories: [Projetos, Quebrando Padrões, Desenvolvimento Pessoal]
tags: [firebase, realtime database, desafios, javascript, projetos pessoais, aprendizado, backend, jogo da velha]
---

## Um problema típico

> **"Imperfeito feito vence, perfeito nunca feito."**
---

O código parecia certo, mas a partida entre os dois jogadores não iniciava. A falha foi detectada e corrigida. Qual era o problema? Regras de segurança do *Firebase Realtime Database*.

Primeiro, uma dica para quem está começando como eu: **controle a ansiedade**. Antes de começar a codificar um projeto, pesquise e entenda cada ferramenta que pretende utilizar.

Neste projeto fui tomado pela pressa. Li por alto sobre o Firebase Realtime Database e decidi: "é essa a ferramenta que vou usar". Quando comecei a codificar, percebi que havia muito mais para entender antes de seguir com a implementação.

O que fiz então? Recorri ao ChatGPT, que me deu códigos prontos. Tentei entender e aplicar, mas a cada nova implementação, percebia que a complexidade aumentava. Depois de muitos prompts mal estruturados e respostas que refletiam isso, o projeto até funcionou em certo momento. Mas a falta de conhecimento cobra caro. Qualquer falha se tornava um desafio ainda maior de resolver.

Foi necessário parar o desenvolvimento e estudar o que deveria ter sido entendido antes. Mais uma vez o projeto parou, e o sentimento de incompetência apareceu. Essa pausa — e esse sentimento — poderiam ter sido evitados por uma palavra simples: **preparação**.

Agora que falei da dor, quero compartilhar o que aprendi com ela.

## Regras de Segurança da Realtime Database

Depois de muito procurar, finalmente encontrei a documentação correta: [Security Rules language](https://firebase.google.com/docs/rules/rules-language). Aprendi a estrutura básica.

```json
{
  "rules":{
    "<<caminho>>":{
      //Condições para permissões: Leitura, Escrita e Validação
      ".read":"<<condição>>", //Condição para leitura
      ".write":"<<condicao>>",//Condição para escrita
      ".validate":"<<condicao>>"//Condição de validação
    }
  }
}
```

Essa estrutura, semelhante a um JSON, tem 3 componentes principais:

- ***Caminho***: a localização no banco de dados.
- ***Requisições***: métodos para conceder permissão de leitura, escrita ou validação.
- ***Condições***: critérios que autorizam a operação se forem validados como verdadeiros.

### Como a regra estava?

A estrutura do jogo é basicamente:

```bash
|games
  |game
    |id
      |players
        |player1
        |player2
      |currentPlayer
      |isFull
      |eventos
      |moves
```

O problema estava nas regras de segurança que impediam o cadastro dos jogadores. A validação esperava uma estrutura que não existia `userGames`.

Veja como estava:

```json
"players": {
  "player1": {
    ".write": "root.child('userGames/' + auth.uid + '/' + $matchId + '/playerId').val() === data.child('id').val() || !data.exists()"
  },
  "player2": {
    ".write": "root.child('userGames/' + auth.uid + '/' + $matchId + '/playerId').val() === data.child('id').val() || !data.exists()"
  }
}
```

A lógica acima verifica se existe um playerId associado ao usuário autenticado dentro da estrutura `userGames`, mas ela não existia no banco. Resultado: permissão negada e a chave isFull nunca era atualizada para `true`.

### Como corrigi

Entendendo isso, reescrevi as regras de forma mais simples e adequada à minha estrutura real.

```json
"players":{
  "player1":{
    ".write":"!data.exists()"
  },
  "player2":{
    ".write":"!data.exists() && root.child('games/game' + $matchId + '/players/player1').exists()"
  }
}
```

Explicando:

- `player1`: só pode ser escrito se ainda não existir.
- `player2`: só pode ser escrito se `player1` já existir e ele próprio ainda não existir.

Agora, a regra para `isFull`.

```json
"isFull":{
  ".read":"auth != null",
  ".write":"!data.exists() || (root.child('games/game' + $matchId + '/players/player1').exists() && root.child('games/game' + $matchId + '/players/player2').exists())"
}
```

Ou seja, a leitura é liberada para qualquer usuário autenticado, e a escrita só é permitida se ambos os jogadores estiverem cadastrados.
Com essas mudanças, a chave `isFull` finalmente é atualizada corretamente e a partida pode começar.

---

Lembre-se: este diário é sobre crescimento pessoal - o meu e o seu, se você também enfrente bloqueios semelhantes.

>Porque **não é sobre o jogo. É sobre quebrar padrões**.

📌 [Repositório no GitHub](https://github.com/guedesindev/tic-tac-toe)  
📓 [Leia o post anterior da série](https://guedesindev.github.io/projetos/quebrando%20padr%C3%B5es/desenvolvimento%20pessoal/diario-jogo-da-vellha-1/)  
🧠 [Saiba mais sobre o as regras de segurança do firebase realtime database](https://firebase.google.com/docs/rules)
