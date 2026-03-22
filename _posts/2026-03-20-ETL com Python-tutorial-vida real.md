---
title: "ETL com Python: o dia em que o tutorial não me preparou pra vida real"
date: 2026-03-20
description: "Como me deparei com a dura realidade do ambismo entre os sistemas desenvolvidos em tutoriais e a vida real"
categories:  [Python, ETL, Dados, Quebrando Padrões]
tags: [python, etl, dados, quebrando padrões]
---


> _"Feito imperfeito vence perfeito nunca feito."_  
> _Aldo Novak_


Acho fantástico como a maioria dos vídeos que vejo sobre análise de dados em Python segue sempre o mesmo roteiro:

CSV bonitinho
Encoding padrão (UTF-8)
Separador certinho (; ou ,)
Tudo funcionando… perfeitamente

E aí você pensa:
“Nossa, ETL é tranquilo!”

Spoiler: não é bem assim.
---

## 📌 A ideia

Eu pensei:
> “Vou mostrar pro meu chefe que consigo ir além de analisar documentos… vou fazer um ETL com dados reais.”

Peguei 3 arquivos CSV:

Banco Central
Prefeitura de São Paulo
Anvisa

Confiança lá em cima.
---

## 🚀 Começo promissor

Fiz o básico:

Criei o ambiente virtual
Instalei o pandas
Usei pathlib (porque sim, gosto de fazer do jeito “bonito”)

Código:

```python
from pathlib import Path
import pandas as pd

csv_bc = Path("bcdata.sgs.11426.csv")

df = pd.read_csv(csv_bc, encoding="utf-8", sep=";")
print("="*30)
print(df.info())
print("="*30+"\n")
```

## Resultado?

👉 Funcionou perfeitamente.

Aí veio o pensamento perigoso:

> “Tá fácil demais isso aqui…”
---

## 💥 O primeiro choque de realidade

Fui pro segundo arquivo (Prefeitura de SP).

Mesma abordagem.

Mesmo código.

Mesmo otimismo.

E aí… BOOM 💣

```bash
UnicodeDecodeError: 'utf-8' codec can't decode byte 0xe3...
```

Naquele momento, eu virei oficialmente:

> “O iniciante olhando erro gigante no terminal”
---

## 🤯 O que o tutorial não te ensina

O pandas não estava conseguindo interpretar o arquivo.

E o erro, apesar de enorme, dizia basicamente isso:

👉 “O encoding não é UTF-8”

Simples… depois que você entende.

Mas no começo?

Parece grego.
---

## 🧠 Tentativa clássica (e inútil)

Pensei:

“Vou deixar o pandas decidir sozinho”

## Resultado?

👉 Outro erro.

Ou seja:
na vida real, o ambiente não vem pronto.
---

## 🔍 A virada de chave

Lembrei de uma máxima:

> “Se você está passando por isso, alguém já passou também.”

Fui pesquisar.

E encontrei a solução: **chardet**
---

## 🛠️ Detectando o encoding

Código:

```python
import chardet

with open(csv_sp, 'rb') as f:
    result = chardet.detect(f.read())
    encoding = result['encoding']
```

Agora sim.

👉 Eu sabia qual encoding usar.
---

## ✅ Aplicando a solução
```python
df_sp = pd.read_csv(csv_sp, encoding=encoding, sep=";")
```
Resultado?

👉 Funcionou.

👉 Sem chute.

👉 Sem sofrimento desnecessário.
---

## 🔁 Aplicando para todos os arquivos

Fiz isso nos 3 CSVs.

Todos carregados com sucesso.

E aí veio o aprendizado de verdade:
---

## 🧠 O insight

Tutorial te ensina o caminho ideal.
A vida real te obriga a entender o caminho.
---


## ⚡ Quebrando o padrão

Se você só segue tutorial:

- Você replica<br>
- Você não entende<br>
- Você trava quando algo muda<br>

Quando você enfrenta erro real:

- Você investiga<br>
- Você entende<br>
- Você evolui<br>
---

## 🎯 Conclusão

O problema nunca foi o CSV.

O problema era eu acreditar que o mundo real era igual ao tutorial.

Não é.

E ainda bem que não é.
---

## 🚀 Próximo passo

Se quiser, no próximo post eu mostro:

👉 Como detectar automaticamente o separador do CSV também
---

## 💬 E você?

Já passou por isso?

Aquele momento em que:

“No tutorial funciona… mas no seu código não?”

Me conta 👇