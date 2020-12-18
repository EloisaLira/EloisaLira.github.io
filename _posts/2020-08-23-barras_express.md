---
layout: post
title: Gráfico de Barras Express
subtitle: Programando do zero sem o uso do Excel
cover-img:  /assets/img/cod_barras_express.gif
thumbnail-img: /assets/img/cod_barras_express.gif
share-img:  /assets/img/cod_barras_express.gif
tags: [DIY, graph,Data Science, Mine, Iron]
---

Quem nunca precisou inserir um gráfico para uma apresentação importante? um gráfico personalizado sem recorrer ao Excel?!?! Esse tutorial é dedicado especialmente para meus amigos Geólogos e para todos que se interessem por programação, preparei um tutorial simples para dar aquele tcham!

Primeiramente, recomendo para aqueles que não estão familiarizados ao ambiente de programação que usem o Google Colab. Esta ferramenta já possui a linguagem Python instalada e as bibliotecas necessárias para começarmms. Não precisa instalar nada em sua máquina.

![gif](https://media-exp1.licdn.com/dms/image/C5612AQGjpN4ccUjU9Q/article-inline_image-shrink_1000_1488/0/1600891356300?e=1613606400&v=beta&t=KvtMUZE31hPYo5BkuThSXUBogfDRNGk-8wIA5OCN4GU)

Clica ![aqui](https://colab.research.google.com/) e selecione a opção "New Notebook"

Sem "embromation" fiz uma breve pesquisa sobre os maiores países produtores de Ferro. A referência é do United States Geological Survey. Dados Minerals Yearbook 2012 (tá desatualizado mas vamos usar essa informação para plotar as informações, ok!?!?).

O primeiro passo, será chamar as bibliotecas da funções responsáveis pela manipulação dos dados (Pandas) e plotagem do dado (Matplotlib). Para facilitar o código, vou atribuir um álias (um apelido) a estas funções "pd" e "plt" respectivamente.

---python
import pandas as pd
import matpltlib.pyplot as plt
----
