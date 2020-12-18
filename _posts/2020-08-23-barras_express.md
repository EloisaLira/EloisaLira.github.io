---
layout: post
title: Gráfico de Barras Express
subtitle: Adeus Excel!
cover-img:  /assets/img/cod_barras_express.gif
thumbnail-img: /assets/img/cod_barras_express.gif
share-img:  /assets/img/cod_barras_express.gif
tags: [DIY, graph,Data Science, Mine, Iron]
---

Quem nunca precisou inserir um gráfico para uma apresentação importante? um gráfico personalizado sem recorrer ao Excel?!?! Esse tutorial é dedicado especialmente para meus amigos Geólogos e para todos que se interessem por programação, preparei um tutorial simples para dar aquele tcham!

Primeiramente, recomendo para aqueles que não estão familiarizados ao ambiente de programação que usem o Google Colab. Esta ferramenta já possui a linguagem Python instalada e as bibliotecas necessárias para começarmms. Não precisa instalar nada em sua máquina.

![gif](https://media-exp1.licdn.com/dms/image/C5612AQGjpN4ccUjU9Q/article-inline_image-shrink_1000_1488/0/1600891356300?e=1613606400&v=beta&t=KvtMUZE31hPYo5BkuThSXUBogfDRNGk-8wIA5OCN4GU)

Clica [aqui](https://colab.research.google.com/) e selecione a opção "New Notebook"

Sem "embromation" fiz uma breve pesquisa sobre os maiores países produtores de Ferro. A referência é do United States Geological Survey. Dados Minerals Yearbook 2012 (tá desatualizado mas vamos usar essa informação para plotar as informações, ok!?!?).

O primeiro passo, será chamar as bibliotecas da funções responsáveis pela manipulação dos dados (Pandas) e plotagem do dado (Matplotlib). Para facilitar o código, vou atribuir um álias (um apelido) a estas funções "pd" e "plt" respectivamente.

```python
import pandas as pd
import matplotlib.pyplot as plt
```
Agora a criação dos dados . Criaremos um dado estruturado em países versus a produção de Fe (Ferro). Este será nosso Data Frame:


```python
country=['China','Austrália','Brasil','Índia','Rússia','Ucrânia','África do Sul','EUA','Canadá','Irã']
prod_mundial=[44.71,17.78,13.59,4.9,3.58,2.8,2.15,1.84,1.35,1.26]
```


Agora que temos o nossos eixos X (**prod_mundial**) e Y (**country**), vamos plotar da maneira mais simples possível.

```python
plt.bar(country,prod_mundial,color='green',linewidth=0.7, edgecolor='black')
plt.title("Maiores países produtores de Fe")
```
Ainda podemos melhorar muitos aspectos desse gráfico. Vamos trabalhar em alguns deles por partes.

## Primeiramente as barras

Vamos mudar a cor das barras (**color='green'**), espessura do delineado delas (**linewidth=0.7**), cor do delineado (**edgecolor='black'**), alinhamento das barras (**align='center'**) e por último a orientação do gráfico (**orientation='vertical' ou 'horizontal'**).


```python
plt.bar(country,prod_mundial,color='green', linewidth=0.7,edgecolor='black',align='center',orientation='vertical')
```

![image](https://media-exp1.licdn.com/dms/image/C4E12AQHENn3JNfCmOg/article-inline_image-shrink_1000_1488/0/1600886663291?e=1613606400&v=beta&t=YgTYC9u7WQqq9qxn6qnWBMB-6D79yIXSFx_bVCpIkRM)

Para as barras existem diversas edições que podemos fazer. Mas, vamos seguir a diante com o nosso gráfico....

## Editando o título
Vamos alterar a fonte do título (**fontname='Marlett**), o estilo da fonte para o negrito (**fontweight='bold'**) e o seu tamanho (**fontsize=15**). Essa será simples!

```python
plt.bar(country,prod_mundial,color='green', linewidth=0.7,edgecolor='black',align='center',orientation='vertical')
plt.title("Maiores países produtores de Fe", fontname='Marlett', fontsize=15, fontweight='bold')

```

## Editando os eixos
Aqui podemos fazer primeiramente as modificações no eixo Y. Vamos adicionar o nome do eixo, alterar tamanho (**fontsize**) e tipo da fonte (**fontname**). As duas últimas características nos já vimos anteriormente. Então nosso gráfico fica até agora:

```python
plt.bar(country,prod_mundial,color='green', linewidth=0.7,edgecolor='black',align='center',orientation='vertical')
plt.title("Maiores países produtores de Fe", fontname='Marlett', fontsize=15, fontweight='bold')
plt.ylabel("Produção em %",fontname='Marlett', fontsize=12)

```

![image](https://media-exp1.licdn.com/dms/image/C4E12AQFXpuSVB34L2g/article-inline_image-shrink_1000_1488/0/1600888512605?e=1613606400&v=beta&t=JLnyYUkEGedpk9CLT3B7lRqNP1DhLoUAuqW729vIIl0)

Agora vamos ajustar o eixo X. Podemos rotacionar os valores (países produtores) do eixo X que são mencionados como **ticks**. E ainda falta denominar o eixo X como "Países produtores" é o **xlabel**.

Vamos rotacional o **ticks** e adicionar o nome do eixo X! Vamos ver no código:

```python
plt.bar(country,prod_mundial,color='green', linewidth=0.7,edgecolor='black',align='center',orientation='vertical')
plt.title("Maiores países produtores de Fe", fontname='Marlett', fontsize=15, fontweight='bold')
plt.ylabel("Produção em %",fontname='Marlett', fontsize=12)
plt.xlabel("Paises produtores",fontname='Marlett'fontsize=14)
plt.xticks(rotation=45,fontsize=14)

```

![image](https://media-exp1.licdn.com/dms/image/C4E12AQGp7HQi40BXFg/article-inline_image-shrink_1000_1488/0/1600890038278?e=1613606400&v=beta&t=IpTI6C9Ibkbi6HvwHeB_WuzneYkpqutxvl62Wa5DyhE)

Espero que tenha ajudado. É claro que existem várias bibliotecas além do [Matplotlib](https://matplotlib.org/) um exemplo delas é a [Seaborn](https://seaborn.pydata.org/). Uma forma de aprender a construir gráficos diversos é praticando a situações diversas. Vamos praticar outros tipos de gráficos nas próximas publicações. Bons estudos!


