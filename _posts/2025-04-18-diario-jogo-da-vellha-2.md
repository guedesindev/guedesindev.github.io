---
title: "Firebase e o Jogo da Velha: Persistência Além dos Dados"
date: 2025-04-18
description: "Neste post da série sobre o desenvolvimento do Jogo da Velha, compartilho os desafios com autenticação e persistência no Firebase Realtime, e como superá-los se tornou mais uma vitória contra a procrastinação."
categories: [Projetos, Quebrando Padrões, Desenvolvimento Pessoal]
tags: [firebase, realtime database, desafios, javascript, projetos pessoais, aprendizado, backend, jogo da velha]
---

## Firebase e Jogo da Velha: Sincronia em Tempo Real com JavaScript

> **"Não é sobre o jogo. É sobre quebrar padrões."**

---

Dando continuidade ao projeto do Jogo da Velha multiplayer, sem frameworks e com Firebase, hoje compartilho um dos trechos mais frustrantes — e também mais gratificantes — da jornada até aqui.

### O desafio do Firebase Realtime Database

A proposta era simples: quando um jogador digita seu nome, ele deveria ser autenticado e associado a uma partida em andamento. Se não houver partida disponível, o sistema cria uma nova. Se houver uma partida com apenas um jogador, o novo player entra como o segundo participante, e o jogo começa.

Simples no papel. Mas na prática…

### O problema inesperado

Tudo parecia funcionar, **mas a estrutura do banco de dados se comportava de maneira estranha**: em vez de adicionar o novo jogador a uma partida existente (onde `isFull === false`), **sempre era criada uma nova partida**, como se não houvesse nenhuma sala disponível.

Após revisar repetidamente o código percebi que os dados **estavam sendo persistidos** no Firebase, mas **não estavam sendo lido**. E por isso os métodos para obter as listas de partidas disponíveis estava sempre retornando vazio. Vários consoles.log() pra lá, outros pra cá, parecia que tudo estava certo, por que não resgatava a lista de partidas?

E aí, veio a pista: as regras de segurança do banco.

### A descoberta

As regras padrão do Firebase Realtime Database estavam impedindo **leitura** na estrutura `/games/game`. Isso fazia com que a aplicação não conseguisse obter a lista de partidas já escritas no banco e nem ao menos se havia alguma disponível, por isso sempre criava uma nova sala.

Foi preciso editar manualmente as regras de segurança, permitindo a leitura para usuários autenticados via `firebase authentication`.

Após isso, tudo começou a funcionar. As partidas finalmente estavam sendo populadas com dois jogadores, os eventos estavam sendo sincronizados entretanto, outro problema foi percebido.

### Ainda há o que melhorar

Mesmo com esse avanço, ainda persiste um detalhe: o valor booleano `isFull` **ainda não está sendo alterado corretamente** no momento da entrada do segundo jogador. Isso impede que o sistema reconheça a sala como “fechada” e pronta para iniciar o jogo, o que pode causar comportamentos inesperados.

Esse será o próximo passo.

---

### Conclusão

Esse trecho do projeto me lembrou algo essencial:
>**Às vezes o código está certo — mas o ambiente não está.**

Persistência não é só uma função do banco de dados. É também uma função nossa, como desenvolvedores.

Mais do que escrever um jogo, estou escrevendo um novo jeito de lidar com os obstáculos. Com paciência, debug, pesquisa e atitude.

> Porque ***não é sobre o jogo. É sobre quebrar padrões***.

---

📌 [Repositório no GitHub](https://github.com/guedesindev/tic-tac-toe)  
📓 [Leia o post anterior da série](https://guedesindev.github.io/projetos/quebrando%20padr%C3%B5es/desenvolvimento%20pessoal/diario-jogo-da-vellha-1/)  
🧠 [Saiba mais sobre o padrão Observer usado no projeto](https://youtu.be/4OLCrClb_So?si=_abqjX4FQtL_PsL6)
