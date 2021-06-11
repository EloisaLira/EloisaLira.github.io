---
layout: post
title: Segmenting and Clustering Neighborhoods in Toronto
subtitle: Part2 of my Coursera Capstone Project in Data Science Specialization
thumbnail-img:  /assets/img/toronto_neighbor.jpeg
cover-img:  /assets/img/toronto_neighbor.jpeg
gh-repo: EloisaLira/eloisalira.github.io
gh-badge: [star, fork, follow]
tags: [transformação digital, mindset, inovação]
comments: true
---

Instaling the libraries.

```python
pip install geocoder
```

    Requirement already satisfied: geocoder in /home/familia/anaconda3/lib/python3.7/site-packages (1.38.1)
    Requirement already satisfied: click in /home/familia/anaconda3/lib/python3.7/site-packages (from geocoder) (7.0)
    Requirement already satisfied: requests in /home/familia/anaconda3/lib/python3.7/site-packages (from geocoder) (2.21.0)
    Requirement already satisfied: future in /home/familia/anaconda3/lib/python3.7/site-packages (from geocoder) (0.17.1)
    Requirement already satisfied: ratelim in /home/familia/anaconda3/lib/python3.7/site-packages (from geocoder) (0.1.6)
    Requirement already satisfied: six in /home/familia/anaconda3/lib/python3.7/site-packages (from geocoder) (1.12.0)
    Requirement already satisfied: chardet<3.1.0,>=3.0.2 in /home/familia/anaconda3/lib/python3.7/site-packages (from requests->geocoder) (3.0.4)
    Requirement already satisfied: urllib3<1.25,>=1.21.1 in /home/familia/anaconda3/lib/python3.7/site-packages (from requests->geocoder) (1.24.1)
    Requirement already satisfied: certifi>=2017.4.17 in /home/familia/anaconda3/lib/python3.7/site-packages (from requests->geocoder) (2019.11.28)
    Requirement already satisfied: idna<2.9,>=2.5 in /home/familia/anaconda3/lib/python3.7/site-packages (from requests->geocoder) (2.8)
    Requirement already satisfied: decorator in /home/familia/anaconda3/lib/python3.7/site-packages (from ratelim->geocoder) (4.4.0)
    Note: you may need to restart the kernel to use updated packages.



```python
import numpy as np # library to handle data in a vectorized manner

import pandas as pd # library for data analsysis
pd.set_option('display.max_columns', None)
pd.set_option('display.max_rows', None)

import json # library to handle JSON files
from bs4 import BeautifulSoup

import geocoder # import geocoder

#!conda install -c conda-forge geopy --yes # uncomment this line if you haven't completed the Foursquare API lab
from geopy.geocoders import Nominatim # convert an address into latitude and longitude values

import requests # library to handle requests
from pandas.io.json import json_normalize # tranform JSON file into a pandas dataframe

# Matplotlib and associated plotting modules
import matplotlib.cm as cm
import matplotlib.colors as colors

# import k-means from clustering stage
from sklearn.cluster import KMeans

!conda install -c conda-forge folium=0.5.0 --yes # uncomment this line if you haven't completed the Foursquare API lab
import folium # map rendering library


print('Libraries imported.')
```

    Collecting package metadata (repodata.json): done
    Solving environment: done
    
    # All requested packages already installed.
    
    Libraries imported.



```python
url= 'https://cocl.us/Geospatial_data'
postal_code=pd.read_csv(url)
postal_code.head()

```


<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Postal Code</th>
      <th>Latitude</th>
      <th>Longitude</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>M1B</td>
      <td>43.806686</td>
      <td>-79.194353</td>
    </tr>
    <tr>
      <th>1</th>
      <td>M1C</td>
      <td>43.784535</td>
      <td>-79.160497</td>
    </tr>
    <tr>
      <th>2</th>
      <td>M1E</td>
      <td>43.763573</td>
      <td>-79.188711</td>
    </tr>
    <tr>
      <th>3</th>
      <td>M1G</td>
      <td>43.770992</td>
      <td>-79.216917</td>
    </tr>
    <tr>
      <th>4</th>
      <td>M1H</td>
      <td>43.773136</td>
      <td>-79.239476</td>
    </tr>
  </tbody>
</table>
</div>




```python
!wget -q -O 'canada_data.json' https://en.wikipedia.org/w/index.php?title=List_of_postal_codes_of_Canada:_M&oldid=947772202
print('Data downloaded!')
req = requests.get("https://en.wikipedia.org/wiki/List_of_postal_codes_of_Canada:_M")
soup = BeautifulSoup(req.content,'lxml')
table = soup.find_all('table')[0]
df = pd.read_html(str(table))
neighborhood_row=pd.DataFrame(df[0])
canada_neighborhood = neighborhood_row[neighborhood_row['Borough'] != 'Not assigned'].reset_index(drop=True)
canada_neighborhood['Neighborhood']= canada_neighborhood.Neighborhood.str.replace('Regent Park / Harbourfront','Regent Park , Harbourfront')
canada_neighborhood.replace(canada_neighborhood['Neighborhood']== 'NaN', canada_neighborhood['Borough'])

canada_neighborhood.head()
```

    Data downloaded!


<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Postal code</th>
      <th>Borough</th>
      <th>Neighborhood</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>M3A</td>
      <td>North York</td>
      <td>Parkwoods</td>
    </tr>
    <tr>
      <th>1</th>
      <td>M4A</td>
      <td>North York</td>
      <td>Victoria Village</td>
    </tr>
    <tr>
      <th>2</th>
      <td>M5A</td>
      <td>Downtown Toronto</td>
      <td>Regent Park , Harbourfront</td>
    </tr>
    <tr>
      <th>3</th>
      <td>M6A</td>
      <td>North York</td>
      <td>Lawrence Manor / Lawrence Heights</td>
    </tr>
    <tr>
      <th>4</th>
      <td>M7A</td>
      <td>Downtown Toronto</td>
      <td>Queen's Park / Ontario Provincial Government</td>
    </tr>
  </tbody>
</table>
</div>




```python
canada_merged = canada_neighborhood.merge(postal_code, left_on='Postal code', right_on='Postal Code',how='inner')

```


```python
canada_merged.head()
canada_merged.drop('Postal Code',axis=1)

```


<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Postal code</th>
      <th>Borough</th>
      <th>Neighborhood</th>
      <th>Latitude</th>
      <th>Longitude</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>M3A</td>
      <td>North York</td>
      <td>Parkwoods</td>
      <td>43.753259</td>
      <td>-79.329656</td>
    </tr>
    <tr>
      <th>1</th>
      <td>M4A</td>
      <td>North York</td>
      <td>Victoria Village</td>
      <td>43.725882</td>
      <td>-79.315572</td>
    </tr>
    <tr>
      <th>2</th>
      <td>M5A</td>
      <td>Downtown Toronto</td>
      <td>Regent Park , Harbourfront</td>
      <td>43.654260</td>
      <td>-79.360636</td>
    </tr>
    <tr>
      <th>3</th>
      <td>M6A</td>
      <td>North York</td>
      <td>Lawrence Manor / Lawrence Heights</td>
      <td>43.718518</td>
      <td>-79.464763</td>
    </tr>
    <tr>
      <th>4</th>
      <td>M7A</td>
      <td>Downtown Toronto</td>
      <td>Queen's Park / Ontario Provincial Government</td>
      <td>43.662301</td>
      <td>-79.389494</td>
    </tr>
    <tr>
      <th>5</th>
      <td>M9A</td>
      <td>Etobicoke</td>
      <td>Islington Avenue</td>
      <td>43.667856</td>
      <td>-79.532242</td>
    </tr>
    <tr>
      <th>6</th>
      <td>M1B</td>
      <td>Scarborough</td>
      <td>Malvern / Rouge</td>
      <td>43.806686</td>
      <td>-79.194353</td>
    </tr>
    <tr>
      <th>7</th>
      <td>M3B</td>
      <td>North York</td>
      <td>Don Mills</td>
      <td>43.745906</td>
      <td>-79.352188</td>
    </tr>
    <tr>
      <th>8</th>
      <td>M4B</td>
      <td>East York</td>
      <td>Parkview Hill / Woodbine Gardens</td>
      <td>43.706397</td>
      <td>-79.309937</td>
    </tr>
    <tr>
      <th>9</th>
      <td>M5B</td>
      <td>Downtown Toronto</td>
      <td>Garden District, Ryerson</td>
      <td>43.657162</td>
      <td>-79.378937</td>
    </tr>
    <tr>
      <th>10</th>
      <td>M6B</td>
      <td>North York</td>
      <td>Glencairn</td>
      <td>43.709577</td>
      <td>-79.445073</td>
    </tr>
    <tr>
      <th>11</th>
      <td>M9B</td>
      <td>Etobicoke</td>
      <td>West Deane Park / Princess Gardens / Martin Gr...</td>
      <td>43.650943</td>
      <td>-79.554724</td>
    </tr>
    <tr>
      <th>12</th>
      <td>M1C</td>
      <td>Scarborough</td>
      <td>Rouge Hill / Port Union / Highland Creek</td>
      <td>43.784535</td>
      <td>-79.160497</td>
    </tr>
    <tr>
      <th>13</th>
      <td>M3C</td>
      <td>North York</td>
      <td>Don Mills</td>
      <td>43.725900</td>
      <td>-79.340923</td>
    </tr>
    <tr>
      <th>14</th>
      <td>M4C</td>
      <td>East York</td>
      <td>Woodbine Heights</td>
      <td>43.695344</td>
      <td>-79.318389</td>
    </tr>
    <tr>
      <th>15</th>
      <td>M5C</td>
      <td>Downtown Toronto</td>
      <td>St. James Town</td>
      <td>43.651494</td>
      <td>-79.375418</td>
    </tr>
    <tr>
      <th>16</th>
      <td>M6C</td>
      <td>York</td>
      <td>Humewood-Cedarvale</td>
      <td>43.693781</td>
      <td>-79.428191</td>
    </tr>
    <tr>
      <th>17</th>
      <td>M9C</td>
      <td>Etobicoke</td>
      <td>Eringate / Bloordale Gardens / Old Burnhamthor...</td>
      <td>43.643515</td>
      <td>-79.577201</td>
    </tr>
    <tr>
      <th>18</th>
      <td>M1E</td>
      <td>Scarborough</td>
      <td>Guildwood / Morningside / West Hill</td>
      <td>43.763573</td>
      <td>-79.188711</td>
    </tr>
    <tr>
      <th>19</th>
      <td>M4E</td>
      <td>East Toronto</td>
      <td>The Beaches</td>
      <td>43.676357</td>
      <td>-79.293031</td>
    </tr>
    <tr>
      <th>20</th>
      <td>M5E</td>
      <td>Downtown Toronto</td>
      <td>Berczy Park</td>
      <td>43.644771</td>
      <td>-79.373306</td>
    </tr>
    <tr>
      <th>21</th>
      <td>M6E</td>
      <td>York</td>
      <td>Caledonia-Fairbanks</td>
      <td>43.689026</td>
      <td>-79.453512</td>
    </tr>
    <tr>
      <th>22</th>
      <td>M1G</td>
      <td>Scarborough</td>
      <td>Woburn</td>
      <td>43.770992</td>
      <td>-79.216917</td>
    </tr>
    <tr>
      <th>23</th>
      <td>M4G</td>
      <td>East York</td>
      <td>Leaside</td>
      <td>43.709060</td>
      <td>-79.363452</td>
    </tr>
    <tr>
      <th>24</th>
      <td>M5G</td>
      <td>Downtown Toronto</td>
      <td>Central Bay Street</td>
      <td>43.657952</td>
      <td>-79.387383</td>
    </tr>
    <tr>
      <th>25</th>
      <td>M6G</td>
      <td>Downtown Toronto</td>
      <td>Christie</td>
      <td>43.669542</td>
      <td>-79.422564</td>
    </tr>
    <tr>
      <th>26</th>
      <td>M1H</td>
      <td>Scarborough</td>
      <td>Cedarbrae</td>
      <td>43.773136</td>
      <td>-79.239476</td>
    </tr>
    <tr>
      <th>27</th>
      <td>M2H</td>
      <td>North York</td>
      <td>Hillcrest Village</td>
      <td>43.803762</td>
      <td>-79.363452</td>
    </tr>
    <tr>
      <th>28</th>
      <td>M3H</td>
      <td>North York</td>
      <td>Bathurst Manor / Wilson Heights / Downsview North</td>
      <td>43.754328</td>
      <td>-79.442259</td>
    </tr>
    <tr>
      <th>29</th>
      <td>M4H</td>
      <td>East York</td>
      <td>Thorncliffe Park</td>
      <td>43.705369</td>
      <td>-79.349372</td>
    </tr>
    <tr>
      <th>30</th>
      <td>M5H</td>
      <td>Downtown Toronto</td>
      <td>Richmond / Adelaide / King</td>
      <td>43.650571</td>
      <td>-79.384568</td>
    </tr>
    <tr>
      <th>31</th>
      <td>M6H</td>
      <td>West Toronto</td>
      <td>Dufferin / Dovercourt Village</td>
      <td>43.669005</td>
      <td>-79.442259</td>
    </tr>
    <tr>
      <th>32</th>
      <td>M1J</td>
      <td>Scarborough</td>
      <td>Scarborough Village</td>
      <td>43.744734</td>
      <td>-79.239476</td>
    </tr>
    <tr>
      <th>33</th>
      <td>M2J</td>
      <td>North York</td>
      <td>Fairview / Henry Farm / Oriole</td>
      <td>43.778517</td>
      <td>-79.346556</td>
    </tr>
    <tr>
      <th>34</th>
      <td>M3J</td>
      <td>North York</td>
      <td>Northwood Park / York University</td>
      <td>43.767980</td>
      <td>-79.487262</td>
    </tr>
    <tr>
      <th>35</th>
      <td>M4J</td>
      <td>East York</td>
      <td>East Toronto</td>
      <td>43.685347</td>
      <td>-79.338106</td>
    </tr>
    <tr>
      <th>36</th>
      <td>M5J</td>
      <td>Downtown Toronto</td>
      <td>Harbourfront East / Union Station / Toronto Is...</td>
      <td>43.640816</td>
      <td>-79.381752</td>
    </tr>
    <tr>
      <th>37</th>
      <td>M6J</td>
      <td>West Toronto</td>
      <td>Little Portugal / Trinity</td>
      <td>43.647927</td>
      <td>-79.419750</td>
    </tr>
    <tr>
      <th>38</th>
      <td>M1K</td>
      <td>Scarborough</td>
      <td>Kennedy Park / Ionview / East Birchmount Park</td>
      <td>43.727929</td>
      <td>-79.262029</td>
    </tr>
    <tr>
      <th>39</th>
      <td>M2K</td>
      <td>North York</td>
      <td>Bayview Village</td>
      <td>43.786947</td>
      <td>-79.385975</td>
    </tr>
    <tr>
      <th>40</th>
      <td>M3K</td>
      <td>North York</td>
      <td>Downsview</td>
      <td>43.737473</td>
      <td>-79.464763</td>
    </tr>
    <tr>
      <th>41</th>
      <td>M4K</td>
      <td>East Toronto</td>
      <td>The Danforth West / Riverdale</td>
      <td>43.679557</td>
      <td>-79.352188</td>
    </tr>
    <tr>
      <th>42</th>
      <td>M5K</td>
      <td>Downtown Toronto</td>
      <td>Toronto Dominion Centre / Design Exchange</td>
      <td>43.647177</td>
      <td>-79.381576</td>
    </tr>
    <tr>
      <th>43</th>
      <td>M6K</td>
      <td>West Toronto</td>
      <td>Brockton / Parkdale Village / Exhibition Place</td>
      <td>43.636847</td>
      <td>-79.428191</td>
    </tr>
    <tr>
      <th>44</th>
      <td>M1L</td>
      <td>Scarborough</td>
      <td>Golden Mile / Clairlea / Oakridge</td>
      <td>43.711112</td>
      <td>-79.284577</td>
    </tr>
    <tr>
      <th>45</th>
      <td>M2L</td>
      <td>North York</td>
      <td>York Mills / Silver Hills</td>
      <td>43.757490</td>
      <td>-79.374714</td>
    </tr>
    <tr>
      <th>46</th>
      <td>M3L</td>
      <td>North York</td>
      <td>Downsview</td>
      <td>43.739015</td>
      <td>-79.506944</td>
    </tr>
    <tr>
      <th>47</th>
      <td>M4L</td>
      <td>East Toronto</td>
      <td>India Bazaar / The Beaches West</td>
      <td>43.668999</td>
      <td>-79.315572</td>
    </tr>
    <tr>
      <th>48</th>
      <td>M5L</td>
      <td>Downtown Toronto</td>
      <td>Commerce Court / Victoria Hotel</td>
      <td>43.648198</td>
      <td>-79.379817</td>
    </tr>
    <tr>
      <th>49</th>
      <td>M6L</td>
      <td>North York</td>
      <td>North Park / Maple Leaf Park / Upwood Park</td>
      <td>43.713756</td>
      <td>-79.490074</td>
    </tr>
    <tr>
      <th>50</th>
      <td>M9L</td>
      <td>North York</td>
      <td>Humber Summit</td>
      <td>43.756303</td>
      <td>-79.565963</td>
    </tr>
    <tr>
      <th>51</th>
      <td>M1M</td>
      <td>Scarborough</td>
      <td>Cliffside / Cliffcrest / Scarborough Village West</td>
      <td>43.716316</td>
      <td>-79.239476</td>
    </tr>
    <tr>
      <th>52</th>
      <td>M2M</td>
      <td>North York</td>
      <td>Willowdale / Newtonbrook</td>
      <td>43.789053</td>
      <td>-79.408493</td>
    </tr>
    <tr>
      <th>53</th>
      <td>M3M</td>
      <td>North York</td>
      <td>Downsview</td>
      <td>43.728496</td>
      <td>-79.495697</td>
    </tr>
    <tr>
      <th>54</th>
      <td>M4M</td>
      <td>East Toronto</td>
      <td>Studio District</td>
      <td>43.659526</td>
      <td>-79.340923</td>
    </tr>
    <tr>
      <th>55</th>
      <td>M5M</td>
      <td>North York</td>
      <td>Bedford Park / Lawrence Manor East</td>
      <td>43.733283</td>
      <td>-79.419750</td>
    </tr>
    <tr>
      <th>56</th>
      <td>M6M</td>
      <td>York</td>
      <td>Del Ray / Mount Dennis / Keelsdale and Silvert...</td>
      <td>43.691116</td>
      <td>-79.476013</td>
    </tr>
    <tr>
      <th>57</th>
      <td>M9M</td>
      <td>North York</td>
      <td>Humberlea / Emery</td>
      <td>43.724766</td>
      <td>-79.532242</td>
    </tr>
    <tr>
      <th>58</th>
      <td>M1N</td>
      <td>Scarborough</td>
      <td>Birch Cliff / Cliffside West</td>
      <td>43.692657</td>
      <td>-79.264848</td>
    </tr>
    <tr>
      <th>59</th>
      <td>M2N</td>
      <td>North York</td>
      <td>Willowdale</td>
      <td>43.770120</td>
      <td>-79.408493</td>
    </tr>
    <tr>
      <th>60</th>
      <td>M3N</td>
      <td>North York</td>
      <td>Downsview</td>
      <td>43.761631</td>
      <td>-79.520999</td>
    </tr>
    <tr>
      <th>61</th>
      <td>M4N</td>
      <td>Central Toronto</td>
      <td>Lawrence Park</td>
      <td>43.728020</td>
      <td>-79.388790</td>
    </tr>
    <tr>
      <th>62</th>
      <td>M5N</td>
      <td>Central Toronto</td>
      <td>Roselawn</td>
      <td>43.711695</td>
      <td>-79.416936</td>
    </tr>
    <tr>
      <th>63</th>
      <td>M6N</td>
      <td>York</td>
      <td>Runnymede / The Junction North</td>
      <td>43.673185</td>
      <td>-79.487262</td>
    </tr>
    <tr>
      <th>64</th>
      <td>M9N</td>
      <td>York</td>
      <td>Weston</td>
      <td>43.706876</td>
      <td>-79.518188</td>
    </tr>
    <tr>
      <th>65</th>
      <td>M1P</td>
      <td>Scarborough</td>
      <td>Dorset Park / Wexford Heights / Scarborough To...</td>
      <td>43.757410</td>
      <td>-79.273304</td>
    </tr>
    <tr>
      <th>66</th>
      <td>M2P</td>
      <td>North York</td>
      <td>York Mills West</td>
      <td>43.752758</td>
      <td>-79.400049</td>
    </tr>
    <tr>
      <th>67</th>
      <td>M4P</td>
      <td>Central Toronto</td>
      <td>Davisville North</td>
      <td>43.712751</td>
      <td>-79.390197</td>
    </tr>
    <tr>
      <th>68</th>
      <td>M5P</td>
      <td>Central Toronto</td>
      <td>Forest Hill North &amp; West</td>
      <td>43.696948</td>
      <td>-79.411307</td>
    </tr>
    <tr>
      <th>69</th>
      <td>M6P</td>
      <td>West Toronto</td>
      <td>High Park / The Junction South</td>
      <td>43.661608</td>
      <td>-79.464763</td>
    </tr>
    <tr>
      <th>70</th>
      <td>M9P</td>
      <td>Etobicoke</td>
      <td>Westmount</td>
      <td>43.696319</td>
      <td>-79.532242</td>
    </tr>
    <tr>
      <th>71</th>
      <td>M1R</td>
      <td>Scarborough</td>
      <td>Wexford / Maryvale</td>
      <td>43.750072</td>
      <td>-79.295849</td>
    </tr>
    <tr>
      <th>72</th>
      <td>M2R</td>
      <td>North York</td>
      <td>Willowdale</td>
      <td>43.782736</td>
      <td>-79.442259</td>
    </tr>
    <tr>
      <th>73</th>
      <td>M4R</td>
      <td>Central Toronto</td>
      <td>North Toronto West</td>
      <td>43.715383</td>
      <td>-79.405678</td>
    </tr>
    <tr>
      <th>74</th>
      <td>M5R</td>
      <td>Central Toronto</td>
      <td>The Annex / North Midtown / Yorkville</td>
      <td>43.672710</td>
      <td>-79.405678</td>
    </tr>
    <tr>
      <th>75</th>
      <td>M6R</td>
      <td>West Toronto</td>
      <td>Parkdale / Roncesvalles</td>
      <td>43.648960</td>
      <td>-79.456325</td>
    </tr>
    <tr>
      <th>76</th>
      <td>M7R</td>
      <td>Mississauga</td>
      <td>Canada Post Gateway Processing Centre</td>
      <td>43.636966</td>
      <td>-79.615819</td>
    </tr>
    <tr>
      <th>77</th>
      <td>M9R</td>
      <td>Etobicoke</td>
      <td>Kingsview Village / St. Phillips / Martin Grov...</td>
      <td>43.688905</td>
      <td>-79.554724</td>
    </tr>
    <tr>
      <th>78</th>
      <td>M1S</td>
      <td>Scarborough</td>
      <td>Agincourt</td>
      <td>43.794200</td>
      <td>-79.262029</td>
    </tr>
    <tr>
      <th>79</th>
      <td>M4S</td>
      <td>Central Toronto</td>
      <td>Davisville</td>
      <td>43.704324</td>
      <td>-79.388790</td>
    </tr>
    <tr>
      <th>80</th>
      <td>M5S</td>
      <td>Downtown Toronto</td>
      <td>University of Toronto / Harbord</td>
      <td>43.662696</td>
      <td>-79.400049</td>
    </tr>
    <tr>
      <th>81</th>
      <td>M6S</td>
      <td>West Toronto</td>
      <td>Runnymede / Swansea</td>
      <td>43.651571</td>
      <td>-79.484450</td>
    </tr>
    <tr>
      <th>82</th>
      <td>M1T</td>
      <td>Scarborough</td>
      <td>Clarks Corners / Tam O'Shanter / Sullivan</td>
      <td>43.781638</td>
      <td>-79.304302</td>
    </tr>
    <tr>
      <th>83</th>
      <td>M4T</td>
      <td>Central Toronto</td>
      <td>Moore Park / Summerhill East</td>
      <td>43.689574</td>
      <td>-79.383160</td>
    </tr>
    <tr>
      <th>84</th>
      <td>M5T</td>
      <td>Downtown Toronto</td>
      <td>Kensington Market / Chinatown / Grange Park</td>
      <td>43.653206</td>
      <td>-79.400049</td>
    </tr>
    <tr>
      <th>85</th>
      <td>M1V</td>
      <td>Scarborough</td>
      <td>Milliken / Agincourt North / Steeles East / L'...</td>
      <td>43.815252</td>
      <td>-79.284577</td>
    </tr>
    <tr>
      <th>86</th>
      <td>M4V</td>
      <td>Central Toronto</td>
      <td>Summerhill West / Rathnelly / South Hill / For...</td>
      <td>43.686412</td>
      <td>-79.400049</td>
    </tr>
    <tr>
      <th>87</th>
      <td>M5V</td>
      <td>Downtown Toronto</td>
      <td>CN Tower / King and Spadina / Railway Lands / ...</td>
      <td>43.628947</td>
      <td>-79.394420</td>
    </tr>
    <tr>
      <th>88</th>
      <td>M8V</td>
      <td>Etobicoke</td>
      <td>New Toronto / Mimico South / Humber Bay Shores</td>
      <td>43.605647</td>
      <td>-79.501321</td>
    </tr>
    <tr>
      <th>89</th>
      <td>M9V</td>
      <td>Etobicoke</td>
      <td>South Steeles / Silverstone / Humbergate / Jam...</td>
      <td>43.739416</td>
      <td>-79.588437</td>
    </tr>
    <tr>
      <th>90</th>
      <td>M1W</td>
      <td>Scarborough</td>
      <td>Steeles West / L'Amoreaux West</td>
      <td>43.799525</td>
      <td>-79.318389</td>
    </tr>
    <tr>
      <th>91</th>
      <td>M4W</td>
      <td>Downtown Toronto</td>
      <td>Rosedale</td>
      <td>43.679563</td>
      <td>-79.377529</td>
    </tr>
    <tr>
      <th>92</th>
      <td>M5W</td>
      <td>Downtown Toronto</td>
      <td>Stn A PO Boxes</td>
      <td>43.646435</td>
      <td>-79.374846</td>
    </tr>
    <tr>
      <th>93</th>
      <td>M8W</td>
      <td>Etobicoke</td>
      <td>Alderwood / Long Branch</td>
      <td>43.602414</td>
      <td>-79.543484</td>
    </tr>
    <tr>
      <th>94</th>
      <td>M9W</td>
      <td>Etobicoke</td>
      <td>Northwest</td>
      <td>43.706748</td>
      <td>-79.594054</td>
    </tr>
    <tr>
      <th>95</th>
      <td>M1X</td>
      <td>Scarborough</td>
      <td>Upper Rouge</td>
      <td>43.836125</td>
      <td>-79.205636</td>
    </tr>
    <tr>
      <th>96</th>
      <td>M4X</td>
      <td>Downtown Toronto</td>
      <td>St. James Town / Cabbagetown</td>
      <td>43.667967</td>
      <td>-79.367675</td>
    </tr>
    <tr>
      <th>97</th>
      <td>M5X</td>
      <td>Downtown Toronto</td>
      <td>First Canadian Place / Underground city</td>
      <td>43.648429</td>
      <td>-79.382280</td>
    </tr>
    <tr>
      <th>98</th>
      <td>M8X</td>
      <td>Etobicoke</td>
      <td>The Kingsway / Montgomery Road / Old Mill North</td>
      <td>43.653654</td>
      <td>-79.506944</td>
    </tr>
    <tr>
      <th>99</th>
      <td>M4Y</td>
      <td>Downtown Toronto</td>
      <td>Church and Wellesley</td>
      <td>43.665860</td>
      <td>-79.383160</td>
    </tr>
    <tr>
      <th>100</th>
      <td>M7Y</td>
      <td>East Toronto</td>
      <td>Business reply mail Processing CentrE</td>
      <td>43.662744</td>
      <td>-79.321558</td>
    </tr>
    <tr>
      <th>101</th>
      <td>M8Y</td>
      <td>Etobicoke</td>
      <td>Old Mill South / King's Mill Park / Sunnylea /...</td>
      <td>43.636258</td>
      <td>-79.498509</td>
    </tr>
    <tr>
      <th>102</th>
      <td>M8Z</td>
      <td>Etobicoke</td>
      <td>Mimico NW / The Queensway West / South of Bloo...</td>
      <td>43.628841</td>
      <td>-79.520999</td>
    </tr>
  </tbody>
</table>
</div>

