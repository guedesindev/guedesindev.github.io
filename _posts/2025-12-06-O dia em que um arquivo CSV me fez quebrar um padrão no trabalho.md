---
title: "O dia em que um arquivo CSV me fez quebrar um padrão no trabalho"
date: 2025-12-06
description: "Um mix de sentimentos: em um momento, uma aula arrastada e teórica; em outro, prática intensa e um projeto fantástico."
categories: [Projeto, Metaaprendizado, Processo Evolutivo, Python, Otimizando meu trabalho, Quebrando Padrões]
tags: [aprendizado contínuo, projetos com Python, otimização com python]
---

## O dia em que um arquivo CSV me fez quebrar um padrão no trabalho

> _"Feito imperfeito vence perfeito nunca feito."_  
> — Aldo Novak

Todos os dias no meu trabalho, recebo uma lista de processos para analisar documentação. A tarefa é simples... mas repetitiva. E claro, a repetição é o berço da fadiga mental.

*Fluxo sempre foi o mesmo:*

- abrir o processo
- copiar o CNPJ
- ir ao site oficial
- verificar as autorizações da empresa
- comparar o endereço
- repetir o processo para o local de armazenamento

Nada complexo. Mas _demorado_. E cada repetição acumulava um pequeno desgaste.

Até que um dia, com a cabeça de dev que insiste em não me deixar em paz, pensei:

| "Por que eu estou fazendo manualmente algo que um script pode fazer em segundos?"

E assim começou uma mini jornada que misturou Python, dados abertos, encoding maluco, arquivos gigantes e... um certo sentimento de vitória intelectual no fim.

### Primeira pista: se o site mostra a informação, ela existe em algum lugar

Minha primeira dúvida foi:
| "Será que vou ter que criar um webscraper? Automatizar navegação? Selenizar a vida?

Mas, como todo bom dev curioso, fui investigar.

Se a informação está disponível publicamente, ela _vem de algum lugar_, e provavelmente este é um lugar público.

E não deu outra, no portal de dados abertos encontrei o CSV com todas as empresas e suas autorizações.

Mamão com açúcar. 💾 Baixei tudo.

### Primeiro obstáculo: encoding. Sempre ele.

Quem trabalha com dados do governo sabe: Cada atualização pode vir com encoding e separadores diferentes.

Num dia o aqruivo vem UTF-8 e separado por ';', no outro vem ISO-8859-1 e separado por ','.

A vida é imprevisível, mas o CSV consegue ser mais ainda. 

E aí entra a é que entra a primeira solução:

- Usei o módulo `chardet` para detectar automaticamente o encoding;
- Usei o módulo `csv` para identificar o separador do arquivo.

Você pode estar se perguntando como eu descobri que esses dois módulos me ajudaria nisso. E eu te digo, para você evoluir como dev é nessa hora que deve usar as LLMs. Perguntei mesmo ao Gemini como fazer, sem ele implementar a solução, e ele indicou os módulos, fui na documentação e analisei como usar.

Resolvi dois problemas com elegância.

Meu script já começava a ter cara de gente grande. 

## Mais aí veio a realidade: 150 mil linhas não perdoam.

Imagine:
Cada consulta começava exatamente assim:
`Leia o CSV -> detect o encoding -> detect o separador -> crie um DataFrame -> só então consulte o CNPJ`

Para cada única consulta já parecia muito, imagina para dezenas por dia? Inaceitável!

Eu precisava de uma forma para fazer esse trabalho _apenas uma vez por semana_, no máximo.

E aí, lendo a documentação do pandas, descobri o formato **Parquet**.

E foi assim que me senti por um instante:

> "Ohh, que novidade!"
>    <br>--- *o mundo inteiro*
> <br><br>"Pois é... para mim foi mesmo"
>    <br>--- *eu*


### Por que o Parquet mudou o jogo (para mim)

Ao contrário dos CSVs, o Parquet é um formato colunar, o que significa:
- leituras mais rápidas;
- não há problemas com encoding;
- não há problemas com separador;
- ocupa menos espaço;
- é otimizado para análise de dados;
- é padrão em Big Data e pipelines modernos.

Uma verdadeira solução.

E a melhor parte: 

Eu só precisava montar meu DataFrame uma única vez e depois salvá-lo em Parquet.

### Meu sistema de atualização automática semanal

Criei uma regra simples, mas eficiente:
1. Baixo o CSV (toda segunda pela manhã)
2. Rodo o script.
3. Ele verifica: quem é mais recente? CSV ou Parquet?
4. Se o CSV for mais recente -> converto em Parquet.
5. Caso contrário -> leio diretamente o Parquet. 

Ou seja:
Na maior parte do tempo, o script consulta diretamente o Parquet, rápido como um raio.

Fiquei genuinamente orgulhoso, confesso 😊.

#### E por que manter os dois arquivos?

Porque eventualmente o layout do CSV pode mudar, e eu quero sempre ter o arquivo original de referência.

E também porque, no futuro, posso automatizar esse pipeline para;
- baixar os arquivos;
- validar;
- limpar;
- gerar os parquets;
- notificar quando houver inconsistências.

Sim... audácia pura.

## Conclusão
### Um simples incômodo se tornou uma quebra de padrão

Esse pequeno projeto me lembrou que _nem sempre precisamos de grandes soluções para grandes dores_.

Às vezes é só um processo repetititvo que precisa de um olhar mais atento, e uma fagulha de curiosidade.

Hone, minha rotina está mais leve. E a cada consulta que eu faço em milissegundos, ao invés de vários minutos, penso:
> Quebrar padrões também é isso, transformar um incômodo em oportunidade.

E se você tiver uma sugestão melhor, uma solução mais otimizada ou uma nova forma de atacar o problema...

estou ansioso para ler.

---
Hoje, sinto que cresci um pouco mais.  
Porque, no fim das contas...

> Não é sobre o simplesmente codar.<\br>
> É sobre quebrar padrões mentais.
