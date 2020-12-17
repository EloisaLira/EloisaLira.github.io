---
layout: post
title: Controle de Outliers
subtitle: Visualização de dados espúrios na prática
gh-repo: https://github.com/EloisaLira/eloisalira.github.io/tree/master/_posts
gh-badge: [star, fork, follow]
tags: [test]
comments: true
---

```python

```

Gostaria de compartilhar uma prática simples mas, de grande valia na rotina de caracterização de reservatórios óleo e gás.

É corriqueira a realização de algumas etapas na caracterização desses reservatórios a partir de dados sísmicos e de logs de poços. 

Porém, esses produtos são amparados na informação de dados de poços. Essa informação é o que chamamos de *dado hard* é a informação mais fidedigna a respeito de uma litologia.

Para que a os estudos de reservatório tenha sucesso é, fundamental garantir uma boa qualidade de seus inputs. É aí que entra a aplicação do conceito de *outliers*.

Muitas vezes os perfis de poços carregam valores espúrios oriundos da coleta por isso tão importante tratá-los. Aqui fiz uma aplicação simples que se faz importante na petrofísica, geologia e geofísica mas que podemos extender para inúmeros casos na rotina de trabalho.

Vou mostrar como carregar perfis no formato las e construir essa análise de outliers na forma gráfica de *boxplots*. Para tal, será usada a linguagem Python.

Os dados de input  foram dados open source do Campo de Otway na Austrália.

### Instalação de Bibliotecas


```python
import pandas as pd
import numpy as np
import seaborn as sns
import matplotlib.pyplot as plt
import lasio
%matplotlib inline

print("installed")
```

    installed


### LISTA PARA CARREGAMENTO DOS POCOS


```python
Input_path="/home/familia/Documents/Pioneer_Field_LAS/Untitled Folder/OtwayBasin/*.las"
import glob
my_list=[glob.glob(Input_path)]
print("Número de poços carregados",len(my_list[0])) #>>> para exibir o numero de pocos carregados
```

    Número de poços carregados 78


### CARREGAMENDO COM O LASIO


```python
#Todos os pocos carregados com pseudonome "P"
for i in range(len(my_list[0])):
    globals()["P" + str(i)]=lasio.read(my_list[0][i]).df()
    globals()["P" + str(i)]= globals()["P" + str(i)].reindex(sorted(globals()["P" + str(i)].columns), axis=1)
    i=i+1    
```

### TESTE  


```python
#vamos imputar os dados faltantes TESTE
lista_colunas=['CALI','DRHO','DT','GR','NPHI','RDEP','RHOB','RMED','RMIC','SP','WELL']
print(len(lista_colunas))
print(len(globals()["P" + str(1)].columns))
print(list(set(lista_colunas) -set(P1.columns)))
P1.columns
```

    11
    13
    ['WELL']





    Index(['CALI', 'DRHO', 'DT', 'GR', 'MINV', 'MNOR', 'NPHI', 'PEF', 'RDEP',
           'RHOB', 'RMED', 'RMIC', 'SP'],
          dtype='object')



### CRIAÇÃO DE LOG FALTANTE COM NAN


```python
for i in range(len(my_list[0])):
    if(list(set(lista_colunas) -set(globals()["P" + str(i)].columns)) != []):
        diff_list = list(set(lista_colunas) -set(globals()["P" + str(i)].columns))
        print(i,diff_list)
        for j in range(len(diff_list)): #adiciona cada coluna por vez de acordo c a diff_list
            globals()["P" + str(i)][diff_list[j]] = pd.Series(np.nan, index=globals()["P" + str(i)].index)
            
        j=j+1
    #reordena por ordem alfabética cada dataframe
    globals()["P" + str(i)]= globals()["P" + str(i)].reindex(sorted(globals()["P" + str(i)].columns), axis=1)    
    i=i+1
    print(i," ____")
    
```

    0 ['WELL', 'NPHI', 'DRHO', 'RHOB']
    1  ____
    1 ['WELL']
    2  ____
    2 ['WELL']
    3  ____
    3 ['WELL']
    4  ____
    4 ['WELL']
    5  ____
    5 ['WELL']
    6  ____
    6 ['WELL']
    7  ____
    7 ['WELL']
    8  ____
    8 ['WELL', 'RMED', 'SP', 'RDEP']
    9  ____
    9 ['WELL', 'NPHI', 'DRHO', 'RHOB']
    10  ____
    10 ['WELL']
    11  ____
    11 ['WELL']
    12  ____
    12 ['WELL']
    13  ____
    13 ['WELL', 'CALI', 'RMIC', 'RHOB', 'DRHO', 'NPHI']
    14  ____
    14 ['WELL']
    15  ____
    15 ['WELL']
    16  ____
    16 ['WELL', 'DT', 'CALI', 'RMIC', 'RHOB', 'DRHO', 'NPHI']
    17  ____
    17 ['WELL']
    18  ____
    18 ['WELL', 'RMIC', 'RHOB', 'DRHO', 'NPHI']
    19  ____
    19 ['WELL']
    20  ____
    20 ['WELL']
    21  ____
    21 ['WELL']
    22  ____
    22 ['WELL']
    23  ____
    23 ['WELL']
    24  ____
    24 ['WELL', 'DT', 'RMIC', 'RHOB', 'GR', 'DRHO', 'NPHI']
    25  ____
    25 ['WELL', 'RMIC', 'RHOB', 'DRHO', 'NPHI']
    26  ____
    26 ['WELL']
    27  ____
    27 ['WELL']
    28  ____
    28 ['WELL', 'RMIC', 'NPHI']
    29  ____
    29 ['WELL', 'SP', 'RHOB', 'DRHO', 'NPHI']
    30  ____
    30 ['WELL']
    31  ____
    31 ['WELL']
    32  ____
    32 ['WELL']
    33  ____
    33 ['WELL']
    34  ____
    34 ['WELL', 'RMIC', 'RHOB', 'DRHO', 'NPHI']
    35  ____
    35 ['WELL']
    36  ____
    36 ['WELL', 'DT', 'RMIC', 'RHOB', 'GR', 'DRHO', 'NPHI']
    37  ____
    37 ['WELL', 'RMED', 'SP', 'RDEP']
    38  ____
    38 ['WELL']
    39  ____
    39 ['WELL']
    40  ____
    40 ['WELL']
    41  ____
    41 ['WELL']
    42  ____
    42 ['WELL']
    43  ____
    43 ['WELL']
    44  ____
    44 ['WELL']
    45  ____
    45 ['WELL']
    46  ____
    46 ['WELL', 'RMIC', 'RHOB', 'DRHO', 'NPHI']
    47  ____
    47 ['WELL']
    48  ____
    48 ['WELL', 'NPHI', 'DRHO', 'RHOB']
    49  ____
    49 ['WELL']
    50  ____
    50 ['WELL']
    51  ____
    51 ['WELL']
    52  ____
    52 ['WELL', 'NPHI', 'DRHO', 'RHOB']
    53  ____
    53 ['WELL', 'RMIC']
    54  ____
    54 ['WELL']
    55  ____
    55 ['WELL']
    56  ____
    56 ['WELL']
    57  ____
    57 ['WELL', 'DT', 'CALI', 'RMIC', 'RHOB', 'DRHO', 'NPHI']
    58  ____
    58 ['WELL', 'SP']
    59  ____
    59 ['WELL', 'RMIC', 'NPHI']
    60  ____
    60 ['WELL', 'RMIC']
    61  ____
    61 ['WELL', 'RMIC', 'RHOB', 'DRHO', 'NPHI']
    62  ____
    62 ['WELL']
    63  ____
    63 ['WELL', 'CALI', 'RMIC', 'RHOB', 'DRHO', 'NPHI']
    64  ____
    64 ['WELL', 'RMIC', 'RHOB', 'DRHO', 'NPHI']
    65  ____
    65 ['WELL']
    66  ____
    66 ['WELL']
    67  ____
    67 ['WELL']
    68  ____
    68 ['WELL']
    69  ____
    69 ['WELL', 'NPHI', 'DRHO', 'RHOB']
    70  ____
    70 ['WELL', 'RMIC', 'NPHI']
    71  ____
    71 ['WELL', 'RMIC']
    72  ____
    72 ['WELL']
    73  ____
    73 ['WELL', 'DT', 'RMED', 'SP', 'CALI', 'RMIC', 'RHOB', 'GR', 'DRHO', 'NPHI']
    74  ____
    74 ['WELL']
    75  ____
    75 ['WELL']
    76  ____
    76 ['WELL', 'RMIC', 'RHOB', 'DRHO', 'NPHI']
    77  ____
    77 ['WELL']
    78  ____



```python
P29
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>CALI</th>
      <th>DRHO</th>
      <th>DT</th>
      <th>GR</th>
      <th>NPHI</th>
      <th>RDEP</th>
      <th>RHOB</th>
      <th>RMED</th>
      <th>RMIC</th>
      <th>SP</th>
      <th>WELL</th>
    </tr>
    <tr>
      <th>DEPTH</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0.0000</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>0.1524</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>0.3048</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>0.4572</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>0.6096</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>1094.5368</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1094.6892</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1094.8416</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1094.9940</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1095.1464</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
<p>7187 rows × 11 columns</p>
</div>



### JUNÇÃO DE TODOS OS POÇOS EM UNICO ARQUIVO


```python
#todos os pocos com os perfis


lista_pocos=['P1','P9','P10','P14','P17','P19','P25','P26','P30','P35','P37','P38','P47','P49','P53','P58','P62','P64','P65','P70','P74','P77']
```


```python

all_wells=pd.DataFrame()
all_wells=P1
#for i in range(len(my_list[0])):
for i in range(len(lista_pocos)):
    all_wells=all_wells.append((globals()[str(lista_pocos[i])]))
    i=i+1
```


```python
all_wells.describe()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>CALI</th>
      <th>DRHO</th>
      <th>DT</th>
      <th>GR</th>
      <th>MINV</th>
      <th>MNOR</th>
      <th>NPHI</th>
      <th>PEF</th>
      <th>RDEP</th>
      <th>RHOB</th>
      <th>RMED</th>
      <th>RMIC</th>
      <th>SP</th>
      <th>WELL</th>
      <th>DTS</th>
      <th>NEUT</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>275651.000000</td>
      <td>164388.000000</td>
      <td>290362.000000</td>
      <td>373447.000000</td>
      <td>116505.000000</td>
      <td>116505.000000</td>
      <td>160307.000000</td>
      <td>145218.000000</td>
      <td>256111.000000</td>
      <td>164471.000000</td>
      <td>260299.000000</td>
      <td>199014.000000</td>
      <td>249079.000000</td>
      <td>0.0</td>
      <td>20004.000000</td>
      <td>2370.000000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>9.410099</td>
      <td>0.071992</td>
      <td>96.720829</td>
      <td>80.824487</td>
      <td>3.389594</td>
      <td>2.661659</td>
      <td>0.259453</td>
      <td>3.026118</td>
      <td>22.292091</td>
      <td>2.358424</td>
      <td>18.930929</td>
      <td>41.241388</td>
      <td>37.444625</td>
      <td>NaN</td>
      <td>143.465757</td>
      <td>1330.494785</td>
    </tr>
    <tr>
      <th>std</th>
      <td>1.895997</td>
      <td>0.096493</td>
      <td>24.467956</td>
      <td>36.358582</td>
      <td>8.224229</td>
      <td>5.355036</td>
      <td>0.106553</td>
      <td>0.914562</td>
      <td>820.143139</td>
      <td>0.191193</td>
      <td>715.466800</td>
      <td>953.697102</td>
      <td>85.795078</td>
      <td>NaN</td>
      <td>20.631918</td>
      <td>265.019184</td>
    </tr>
    <tr>
      <th>min</th>
      <td>-3.680000</td>
      <td>-2.086000</td>
      <td>-19.000000</td>
      <td>0.000000</td>
      <td>0.025000</td>
      <td>-0.342800</td>
      <td>-0.045500</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>-135.375000</td>
      <td>NaN</td>
      <td>76.053900</td>
      <td>487.062400</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>8.497100</td>
      <td>0.000000</td>
      <td>79.400000</td>
      <td>51.000000</td>
      <td>0.419900</td>
      <td>0.482600</td>
      <td>0.191900</td>
      <td>2.539100</td>
      <td>2.164550</td>
      <td>2.249700</td>
      <td>2.092500</td>
      <td>1.110500</td>
      <td>-21.822800</td>
      <td>NaN</td>
      <td>128.955625</td>
      <td>1260.929225</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>8.850000</td>
      <td>0.027300</td>
      <td>90.150000</td>
      <td>85.688600</td>
      <td>1.114800</td>
      <td>1.004700</td>
      <td>0.264300</td>
      <td>2.955100</td>
      <td>4.375200</td>
      <td>2.392000</td>
      <td>4.720600</td>
      <td>2.085100</td>
      <td>10.300000</td>
      <td>NaN</td>
      <td>146.815150</td>
      <td>1369.418600</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>9.750000</td>
      <td>0.127700</td>
      <td>110.800000</td>
      <td>109.683500</td>
      <td>2.765400</td>
      <td>1.977300</td>
      <td>0.334500</td>
      <td>3.294000</td>
      <td>8.752550</td>
      <td>2.494300</td>
      <td>9.607700</td>
      <td>4.149500</td>
      <td>74.687800</td>
      <td>NaN</td>
      <td>158.783100</td>
      <td>1471.396100</td>
    </tr>
    <tr>
      <th>max</th>
      <td>40.620000</td>
      <td>3.154600</td>
      <td>1183.000000</td>
      <td>803.201600</td>
      <td>374.234400</td>
      <td>95.289100</td>
      <td>3.276700</td>
      <td>10.258700</td>
      <td>60000.000000</td>
      <td>3.239900</td>
      <td>60000.000000</td>
      <td>24945.929700</td>
      <td>4528.000000</td>
      <td>NaN</td>
      <td>190.523700</td>
      <td>2088.022500</td>
    </tr>
  </tbody>
</table>
</div>



### Missing values
Very common in LAS files because depth of interest may vary for specific measurements. Printed values of -99999.0 in LAS files means null value. Missing values are not common in the middle part of the dataset. Usually, it happens in the head or tail of a file. isna function returns True if the location has a null value, otherwise, it will be False. We may add up these boolean values using sum() function as below:


```python
#Counting all Nan values(percentage) in each well log
Tot_nan=all_wells.isna().sum()
Tot_not_nan=all_wells.count()#essa variável nao leva em consideração numero de nan

100*Tot_nan/(Tot_nan+Tot_not_nan)
#in percentage : missing values
```




    CALI     27.042099
    DRHO     56.490623
    DT       23.148467
    GR        1.157953
    MINV     69.164051
    MNOR     69.164051
    NPHI     57.570761
    PEF      61.564440
    RDEP     32.213847
    RHOB     56.468655
    RMED     31.105388
    RMIC     47.325989
    SP       34.075041
    WELL    100.000000
    DTS      94.705443
    NEUT     99.372720
    dtype: float64



**_PARA O EXERCICIO VOU SELECIONAR SOMENTE NPHI, GR, RHOB,DT_**


```python
all_wells_D=all_wells.drop(['CALI','RDEP','RMED','RMIC','SP','DRHO','PEF','NEUT','MINV','MNOR','WELL'],axis=1)
```

### Calculando o número de Nan values em cada perfil

Let’s suppose that we want to preserve GR, DT, and RHOB as important logs in the data set. So, to get rid of missing values we can drop rows that one of those logs is missed in that row.

This line of code will drop all rows that one (or more) of the subset logs (‘GR’, ‘DT’,...) has a null value.


```python
all_wells_dropped = all_wells_D.dropna(axis=0, how='any')
print("ANTES DO DROPNA",all_wells.shape)
print("DEPOIS DO DROPNA",all_wells_dropped.shape)
print("  ")
A=(145975*11/306849*15)
print("REDUÇÃO DO DADO EM %.3f"% A)
```

    ANTES DO DROPNA (377822, 16)
    DEPOIS DO DROPNA (13892, 5)
      
    REDUÇÃO DO DADO EM 78.494


### Statistics

Run describe command to see data statistics. This can be helpful to see the data range and outliers.


```python
all_wells_dropped.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>DT</th>
      <th>GR</th>
      <th>NPHI</th>
      <th>RHOB</th>
      <th>DTS</th>
    </tr>
    <tr>
      <th>DEPTH</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2898.8004</th>
      <td>68.0511</td>
      <td>97.7741</td>
      <td>0.2354</td>
      <td>2.0982</td>
      <td>158.2822</td>
    </tr>
    <tr>
      <th>2898.9528</th>
      <td>69.5735</td>
      <td>95.8630</td>
      <td>0.2674</td>
      <td>2.1121</td>
      <td>158.2287</td>
    </tr>
    <tr>
      <th>2899.1052</th>
      <td>71.8478</td>
      <td>97.5757</td>
      <td>0.2990</td>
      <td>2.1082</td>
      <td>160.2277</td>
    </tr>
    <tr>
      <th>2899.2576</th>
      <td>75.6225</td>
      <td>89.2717</td>
      <td>0.2782</td>
      <td>2.0961</td>
      <td>164.1069</td>
    </tr>
    <tr>
      <th>2899.4100</th>
      <td>79.0742</td>
      <td>90.0361</td>
      <td>0.2613</td>
      <td>2.0752</td>
      <td>163.9499</td>
    </tr>
  </tbody>
</table>
</div>



## Produzindo o Boxplot com outliers


```python
import seaborn as sns
```


```python
all_wells_dropped.describe()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>DT</th>
      <th>GR</th>
      <th>NPHI</th>
      <th>RHOB</th>
      <th>DTS</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>13892.000000</td>
      <td>13892.000000</td>
      <td>13892.000000</td>
      <td>13892.000000</td>
      <td>13892.000000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>77.362332</td>
      <td>93.121544</td>
      <td>0.165715</td>
      <td>2.432747</td>
      <td>140.459897</td>
    </tr>
    <tr>
      <th>std</th>
      <td>9.475476</td>
      <td>41.846689</td>
      <td>0.105444</td>
      <td>0.153735</td>
      <td>18.057316</td>
    </tr>
    <tr>
      <th>min</th>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>-0.005600</td>
      <td>0.000000</td>
      <td>85.413400</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>69.967775</td>
      <td>64.501750</td>
      <td>0.078000</td>
      <td>2.383200</td>
      <td>126.218950</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>76.292350</td>
      <td>87.457550</td>
      <td>0.144900</td>
      <td>2.466200</td>
      <td>141.008850</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>84.341425</td>
      <td>117.480675</td>
      <td>0.249400</td>
      <td>2.523100</td>
      <td>153.344200</td>
    </tr>
    <tr>
      <th>max</th>
      <td>119.328400</td>
      <td>803.201600</td>
      <td>0.534300</td>
      <td>3.008400</td>
      <td>190.523700</td>
    </tr>
  </tbody>
</table>
</div>



### criando coluna com poços


```python
df1=pd.DataFrame({'VALUE':all_wells_dropped['DT']})
df2=pd.DataFrame({'VALUE':all_wells_dropped['GR']})
df3=pd.DataFrame({'VALUE':all_wells_dropped['NPHI']})
df4=pd.DataFrame({'VALUE':all_wells_dropped['RHOB']})
df1['LOG'] = 'DT'
df2['LOG'] = 'GR'
df3['LOG'] = 'NPHI'
df4['LOG'] = 'RHOB'
```

## Vamos aos plots

**É importante entender que durante o processo de perfilagem podem aparecer dados espúrios por isso vamos olhar a distribuição de cada perfil.**

1. NPHI varia entre **0** e **60**
2. GR varia entre **0** e **200**
3. RHOB varia entre **1** e **3**
4. DT varia entre **0** e **140**


```python
graph=sns.boxplot(x=df1['LOG'], y=df1['VALUE'],color='orange',width=0.4)

graph.axhline(140,linewidth=1, linestyle='--',color='r')

plt.xlabel("Perfil", fontsize= 12)
plt.ylabel("Value", fontsize= 12)


plt.title("Controle de Outliers DT", fontsize= 15)
sns.set(rc={'figure.figsize':(5,6)})

plt.savefig('boxplot_dtp_com_outliers.png', transparent = True)
```


![png](https://github.com/EloisaLira/eloisalira.github.io/blob/master/assets/img/boxplot_dtp_com_outliers.png)



```python
graph=sns.boxplot(x=df2['LOG'], y=df2['VALUE'], color='green',width=0.4)

graph.axhline(200,linewidth=1, linestyle='--',color='r')

plt.xlabel("Perfil", fontsize= 12)
plt.ylabel("Value", fontsize= 12)

plt.title("Controle de Outliers GR", fontsize= 15)

sns.set(rc={'figure.figsize':(5,6)})

plt.savefig('boxplot_gr_com_outliers.png', transparent = True)
```


![png](https://github.com/EloisaLira/eloisalira.github.io/blob/master/assets/img/boxplot_gr_com_outliers.png)



```python
graph=sns.boxplot(x=df3['LOG'], y=df3['VALUE'], color='purple',width=0.4)
graph.axhline(.60,linewidth=1, linestyle='--',color='r')

plt.xlabel("Perfil", fontsize= 12)
plt.ylabel("Value em %", fontsize= 12)

plt.title("Controle de Outliers NPHI", fontsize= 15)

sns.set(rc={'figure.figsize':(5,6)})

plt.savefig('boxplot_nphi_com_outliers.png', transparent = True)
```


![png](https://github.com/EloisaLira/eloisalira.github.io/blob/master/assets/img/boxplot_nphi_com_outliers.png)



```python
graph=sns.boxplot(x=df4['LOG'], y=df4['VALUE'], color='yellow',width=0.3)

graph.axhline(3,linewidth=1, linestyle='--',color='r')
graph.axhline(1,linewidth=1, linestyle='--',color='r')

plt.xlabel("Perfil", fontsize= 12)
plt.ylabel("Value", fontsize= 12)
sns.set(rc={'figure.figsize':(5,6)})

plt.title("Controle de Outliers RHOB", fontsize= 15)
plt.savefig('boxplot_rhob_com_outliers.png', transparent = True)
```


![png](https://github.com/EloisaLira/eloisalira.github.io/blob/master/assets/img/boxplot_rhob_com_outliers.png)



```python

```


```python

```
