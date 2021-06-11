---
layout: post
title: Webscraping Data using Toronto Data
subtitle: Part 3 of my Coursera Capstone Project in Data Science Specialization
thumbnail-img:"https://justo.ca/blog/wp-content/uploads/2019/11/AdobeStock_197679483.jpeg"
cover-img: "https://i.pinimg.com/originals/5d/b9/0b/5db90bd7093d57d3e338a73e2f9cfb16.jpg"
gh-repo: EloisaLira/eloisalira.github.io
gh-badge: [star, fork, follow]
tags: [portifolio, outliers, machine learning, visualização, dados, folium, python]
comments: true
---


```python
import numpy as np # library to handle data in a vectorized manner

import pandas as pd # library for data analsysis
pd.set_option('display.max_columns', None)
pd.set_option('display.max_rows', None)

import json # library to handle JSON files
from bs4 import BeautifulSoup

#!conda install -c conda-forge geopy --yes # uncomment this line if you haven't completed the Foursquare API lab
from geopy.geocoders import Nominatim # convert an address into latitude and longitude values

import requests # library to handle requests
from pandas.io.json import json_normalize # tranform JSON file into a pandas dataframe

# Matplotlib and associated plotting modules
import matplotlib.cm as cm
import matplotlib.colors as colors

# import k-means from clustering stage
from sklearn.cluster import KMeans

#!conda install -c conda-forge folium=0.5.0 --yes # uncomment this line if you haven't completed the Foursquare API lab
import folium # map rendering library

print('Libraries imported.')
```

    Libraries imported.



```python
!wget -q -O 'canada_data.json' https://en.wikipedia.org/w/index.php?title=List_of_postal_codes_of_Canada:_M&oldid=947772202
print('Data downloaded!')
```

    Data downloaded!


To create the above dataframe:

The dataframe will consist of **three columns: PostalCode, Borough, and Neighborhood**


```python
req = requests.get("https://en.wikipedia.org/wiki/List_of_postal_codes_of_Canada:_M")

soup = BeautifulSoup(req.content,'lxml')

table = soup.find_all('table')[0]

df = pd.read_html(str(table))

neighborhood_row=pd.DataFrame(df[0])
neighborhood_row
```

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
      <td>M1A</td>
      <td>Not assigned</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1</th>
      <td>M2A</td>
      <td>Not assigned</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2</th>
      <td>M3A</td>
      <td>North York</td>
      <td>Parkwoods</td>
    </tr>
    <tr>
      <th>3</th>
      <td>M4A</td>
      <td>North York</td>
      <td>Victoria Village</td>
    </tr>
    <tr>
      <th>4</th>
      <td>M5A</td>
      <td>Downtown Toronto</td>
      <td>Regent Park / Harbourfront</td>
    </tr>
    <tr>
      <th>5</th>
      <td>M6A</td>
      <td>North York</td>
      <td>Lawrence Manor / Lawrence Heights</td>
    </tr>
    <tr>
      <th>6</th>
      <td>M7A</td>
      <td>Downtown Toronto</td>
      <td>Queen's Park / Ontario Provincial Government</td>
    </tr>
    <tr>
      <th>7</th>
      <td>M8A</td>
      <td>Not assigned</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>8</th>
      <td>M9A</td>
      <td>Etobicoke</td>
      <td>Islington Avenue</td>
    </tr>
    <tr>
      <th>9</th>
      <td>M1B</td>
      <td>Scarborough</td>
      <td>Malvern / Rouge</td>
    </tr>
    <tr>
      <th>10</th>
      <td>M2B</td>
      <td>Not assigned</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>11</th>
      <td>M3B</td>
      <td>North York</td>
      <td>Don Mills</td>
    </tr>
    <tr>
      <th>12</th>
      <td>M4B</td>
      <td>East York</td>
      <td>Parkview Hill / Woodbine Gardens</td>
    </tr>
    <tr>
      <th>13</th>
      <td>M5B</td>
      <td>Downtown Toronto</td>
      <td>Garden District, Ryerson</td>
    </tr>
    <tr>
      <th>14</th>
      <td>M6B</td>
      <td>North York</td>
      <td>Glencairn</td>
    </tr>
    <tr>
      <th>15</th>
      <td>M7B</td>
      <td>Not assigned</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>16</th>
      <td>M8B</td>
      <td>Not assigned</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>17</th>
      <td>M9B</td>
      <td>Etobicoke</td>
      <td>West Deane Park / Princess Gardens / Martin Gr...</td>
    </tr>
    <tr>
      <th>18</th>
      <td>M1C</td>
      <td>Scarborough</td>
      <td>Rouge Hill / Port Union / Highland Creek</td>
    </tr>
    <tr>
      <th>19</th>
      <td>M2C</td>
      <td>Not assigned</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>20</th>
      <td>M3C</td>
      <td>North York</td>
      <td>Don Mills</td>
    </tr>
    <tr>
      <th>21</th>
      <td>M4C</td>
      <td>East York</td>
      <td>Woodbine Heights</td>
    </tr>
    <tr>
      <th>22</th>
      <td>M5C</td>
      <td>Downtown Toronto</td>
      <td>St. James Town</td>
    </tr>
    <tr>
      <th>23</th>
      <td>M6C</td>
      <td>York</td>
      <td>Humewood-Cedarvale</td>
    </tr>
    <tr>
      <th>24</th>
      <td>M7C</td>
      <td>Not assigned</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>25</th>
      <td>M8C</td>
      <td>Not assigned</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>26</th>
      <td>M9C</td>
      <td>Etobicoke</td>
      <td>Eringate / Bloordale Gardens / Old Burnhamthor...</td>
    </tr>
    <tr>
      <th>27</th>
      <td>M1E</td>
      <td>Scarborough</td>
      <td>Guildwood / Morningside / West Hill</td>
    </tr>
    <tr>
      <th>28</th>
      <td>M2E</td>
      <td>Not assigned</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>29</th>
      <td>M3E</td>
      <td>Not assigned</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>30</th>
      <td>M4E</td>
      <td>East Toronto</td>
      <td>The Beaches</td>
    </tr>
    <tr>
      <th>31</th>
      <td>M5E</td>
      <td>Downtown Toronto</td>
      <td>Berczy Park</td>
    </tr>
    <tr>
      <th>32</th>
      <td>M6E</td>
      <td>York</td>
      <td>Caledonia-Fairbanks</td>
    </tr>
    <tr>
      <th>33</th>
      <td>M7E</td>
      <td>Not assigned</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>34</th>
      <td>M8E</td>
      <td>Not assigned</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>35</th>
      <td>M9E</td>
      <td>Not assigned</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>36</th>
      <td>M1G</td>
      <td>Scarborough</td>
      <td>Woburn</td>
    </tr>
    <tr>
      <th>37</th>
      <td>M2G</td>
      <td>Not assigned</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>38</th>
      <td>M3G</td>
      <td>Not assigned</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>39</th>
      <td>M4G</td>
      <td>East York</td>
      <td>Leaside</td>
    </tr>
    <tr>
      <th>40</th>
      <td>M5G</td>
      <td>Downtown Toronto</td>
      <td>Central Bay Street</td>
    </tr>
    <tr>
      <th>41</th>
      <td>M6G</td>
      <td>Downtown Toronto</td>
      <td>Christie</td>
    </tr>
    <tr>
      <th>42</th>
      <td>M7G</td>
      <td>Not assigned</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>43</th>
      <td>M8G</td>
      <td>Not assigned</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>44</th>
      <td>M9G</td>
      <td>Not assigned</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>45</th>
      <td>M1H</td>
      <td>Scarborough</td>
      <td>Cedarbrae</td>
    </tr>
    <tr>
      <th>46</th>
      <td>M2H</td>
      <td>North York</td>
      <td>Hillcrest Village</td>
    </tr>
    <tr>
      <th>47</th>
      <td>M3H</td>
      <td>North York</td>
      <td>Bathurst Manor / Wilson Heights / Downsview North</td>
    </tr>
    <tr>
      <th>48</th>
      <td>M4H</td>
      <td>East York</td>
      <td>Thorncliffe Park</td>
    </tr>
    <tr>
      <th>49</th>
      <td>M5H</td>
      <td>Downtown Toronto</td>
      <td>Richmond / Adelaide / King</td>
    </tr>
    <tr>
      <th>50</th>
      <td>M6H</td>
      <td>West Toronto</td>
      <td>Dufferin / Dovercourt Village</td>
    </tr>
    <tr>
      <th>51</th>
      <td>M7H</td>
      <td>Not assigned</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>52</th>
      <td>M8H</td>
      <td>Not assigned</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>53</th>
      <td>M9H</td>
      <td>Not assigned</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>54</th>
      <td>M1J</td>
      <td>Scarborough</td>
      <td>Scarborough Village</td>
    </tr>
    <tr>
      <th>55</th>
      <td>M2J</td>
      <td>North York</td>
      <td>Fairview / Henry Farm / Oriole</td>
    </tr>
    <tr>
      <th>56</th>
      <td>M3J</td>
      <td>North York</td>
      <td>Northwood Park / York University</td>
    </tr>
    <tr>
      <th>57</th>
      <td>M4J</td>
      <td>East York</td>
      <td>East Toronto</td>
    </tr>
    <tr>
      <th>58</th>
      <td>M5J</td>
      <td>Downtown Toronto</td>
      <td>Harbourfront East / Union Station / Toronto Is...</td>
    </tr>
    <tr>
      <th>59</th>
      <td>M6J</td>
      <td>West Toronto</td>
      <td>Little Portugal / Trinity</td>
    </tr>
    <tr>
      <th>60</th>
      <td>M7J</td>
      <td>Not assigned</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>61</th>
      <td>M8J</td>
      <td>Not assigned</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>62</th>
      <td>M9J</td>
      <td>Not assigned</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>63</th>
      <td>M1K</td>
      <td>Scarborough</td>
      <td>Kennedy Park / Ionview / East Birchmount Park</td>
    </tr>
    <tr>
      <th>64</th>
      <td>M2K</td>
      <td>North York</td>
      <td>Bayview Village</td>
    </tr>
    <tr>
      <th>65</th>
      <td>M3K</td>
      <td>North York</td>
      <td>Downsview</td>
    </tr>
    <tr>
      <th>66</th>
      <td>M4K</td>
      <td>East Toronto</td>
      <td>The Danforth West / Riverdale</td>
    </tr>
    <tr>
      <th>67</th>
      <td>M5K</td>
      <td>Downtown Toronto</td>
      <td>Toronto Dominion Centre / Design Exchange</td>
    </tr>
    <tr>
      <th>68</th>
      <td>M6K</td>
      <td>West Toronto</td>
      <td>Brockton / Parkdale Village / Exhibition Place</td>
    </tr>
    <tr>
      <th>69</th>
      <td>M7K</td>
      <td>Not assigned</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>70</th>
      <td>M8K</td>
      <td>Not assigned</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>71</th>
      <td>M9K</td>
      <td>Not assigned</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>72</th>
      <td>M1L</td>
      <td>Scarborough</td>
      <td>Golden Mile / Clairlea / Oakridge</td>
    </tr>
    <tr>
      <th>73</th>
      <td>M2L</td>
      <td>North York</td>
      <td>York Mills / Silver Hills</td>
    </tr>
    <tr>
      <th>74</th>
      <td>M3L</td>
      <td>North York</td>
      <td>Downsview</td>
    </tr>
    <tr>
      <th>75</th>
      <td>M4L</td>
      <td>East Toronto</td>
      <td>India Bazaar / The Beaches West</td>
    </tr>
    <tr>
      <th>76</th>
      <td>M5L</td>
      <td>Downtown Toronto</td>
      <td>Commerce Court / Victoria Hotel</td>
    </tr>
    <tr>
      <th>77</th>
      <td>M6L</td>
      <td>North York</td>
      <td>North Park / Maple Leaf Park / Upwood Park</td>
    </tr>
    <tr>
      <th>78</th>
      <td>M7L</td>
      <td>Not assigned</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>79</th>
      <td>M8L</td>
      <td>Not assigned</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>80</th>
      <td>M9L</td>
      <td>North York</td>
      <td>Humber Summit</td>
    </tr>
    <tr>
      <th>81</th>
      <td>M1M</td>
      <td>Scarborough</td>
      <td>Cliffside / Cliffcrest / Scarborough Village West</td>
    </tr>
    <tr>
      <th>82</th>
      <td>M2M</td>
      <td>North York</td>
      <td>Willowdale / Newtonbrook</td>
    </tr>
    <tr>
      <th>83</th>
      <td>M3M</td>
      <td>North York</td>
      <td>Downsview</td>
    </tr>
    <tr>
      <th>84</th>
      <td>M4M</td>
      <td>East Toronto</td>
      <td>Studio District</td>
    </tr>
    <tr>
      <th>85</th>
      <td>M5M</td>
      <td>North York</td>
      <td>Bedford Park / Lawrence Manor East</td>
    </tr>
    <tr>
      <th>86</th>
      <td>M6M</td>
      <td>York</td>
      <td>Del Ray / Mount Dennis / Keelsdale and Silvert...</td>
    </tr>
    <tr>
      <th>87</th>
      <td>M7M</td>
      <td>Not assigned</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>88</th>
      <td>M8M</td>
      <td>Not assigned</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>89</th>
      <td>M9M</td>
      <td>North York</td>
      <td>Humberlea / Emery</td>
    </tr>
    <tr>
      <th>90</th>
      <td>M1N</td>
      <td>Scarborough</td>
      <td>Birch Cliff / Cliffside West</td>
    </tr>
    <tr>
      <th>91</th>
      <td>M2N</td>
      <td>North York</td>
      <td>Willowdale</td>
    </tr>
    <tr>
      <th>92</th>
      <td>M3N</td>
      <td>North York</td>
      <td>Downsview</td>
    </tr>
    <tr>
      <th>93</th>
      <td>M4N</td>
      <td>Central Toronto</td>
      <td>Lawrence Park</td>
    </tr>
    <tr>
      <th>94</th>
      <td>M5N</td>
      <td>Central Toronto</td>
      <td>Roselawn</td>
    </tr>
    <tr>
      <th>95</th>
      <td>M6N</td>
      <td>York</td>
      <td>Runnymede / The Junction North</td>
    </tr>
    <tr>
      <th>96</th>
      <td>M7N</td>
      <td>Not assigned</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>97</th>
      <td>M8N</td>
      <td>Not assigned</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>98</th>
      <td>M9N</td>
      <td>York</td>
      <td>Weston</td>
    </tr>
    <tr>
      <th>99</th>
      <td>M1P</td>
      <td>Scarborough</td>
      <td>Dorset Park / Wexford Heights / Scarborough To...</td>
    </tr>
    <tr>
      <th>100</th>
      <td>M2P</td>
      <td>North York</td>
      <td>York Mills West</td>
    </tr>
    <tr>
      <th>101</th>
      <td>M3P</td>
      <td>Not assigned</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>102</th>
      <td>M4P</td>
      <td>Central Toronto</td>
      <td>Davisville North</td>
    </tr>
    <tr>
      <th>103</th>
      <td>M5P</td>
      <td>Central Toronto</td>
      <td>Forest Hill North &amp; West</td>
    </tr>
    <tr>
      <th>104</th>
      <td>M6P</td>
      <td>West Toronto</td>
      <td>High Park / The Junction South</td>
    </tr>
    <tr>
      <th>105</th>
      <td>M7P</td>
      <td>Not assigned</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>106</th>
      <td>M8P</td>
      <td>Not assigned</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>107</th>
      <td>M9P</td>
      <td>Etobicoke</td>
      <td>Westmount</td>
    </tr>
    <tr>
      <th>108</th>
      <td>M1R</td>
      <td>Scarborough</td>
      <td>Wexford / Maryvale</td>
    </tr>
    <tr>
      <th>109</th>
      <td>M2R</td>
      <td>North York</td>
      <td>Willowdale</td>
    </tr>
    <tr>
      <th>110</th>
      <td>M3R</td>
      <td>Not assigned</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>111</th>
      <td>M4R</td>
      <td>Central Toronto</td>
      <td>North Toronto West</td>
    </tr>
    <tr>
      <th>112</th>
      <td>M5R</td>
      <td>Central Toronto</td>
      <td>The Annex / North Midtown / Yorkville</td>
    </tr>
    <tr>
      <th>113</th>
      <td>M6R</td>
      <td>West Toronto</td>
      <td>Parkdale / Roncesvalles</td>
    </tr>
    <tr>
      <th>114</th>
      <td>M7R</td>
      <td>Mississauga</td>
      <td>Canada Post Gateway Processing Centre</td>
    </tr>
    <tr>
      <th>115</th>
      <td>M8R</td>
      <td>Not assigned</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>116</th>
      <td>M9R</td>
      <td>Etobicoke</td>
      <td>Kingsview Village / St. Phillips / Martin Grov...</td>
    </tr>
    <tr>
      <th>117</th>
      <td>M1S</td>
      <td>Scarborough</td>
      <td>Agincourt</td>
    </tr>
    <tr>
      <th>118</th>
      <td>M2S</td>
      <td>Not assigned</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>119</th>
      <td>M3S</td>
      <td>Not assigned</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>120</th>
      <td>M4S</td>
      <td>Central Toronto</td>
      <td>Davisville</td>
    </tr>
    <tr>
      <th>121</th>
      <td>M5S</td>
      <td>Downtown Toronto</td>
      <td>University of Toronto / Harbord</td>
    </tr>
    <tr>
      <th>122</th>
      <td>M6S</td>
      <td>West Toronto</td>
      <td>Runnymede / Swansea</td>
    </tr>
    <tr>
      <th>123</th>
      <td>M7S</td>
      <td>Not assigned</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>124</th>
      <td>M8S</td>
      <td>Not assigned</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>125</th>
      <td>M9S</td>
      <td>Not assigned</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>126</th>
      <td>M1T</td>
      <td>Scarborough</td>
      <td>Clarks Corners / Tam O'Shanter / Sullivan</td>
    </tr>
    <tr>
      <th>127</th>
      <td>M2T</td>
      <td>Not assigned</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>128</th>
      <td>M3T</td>
      <td>Not assigned</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>129</th>
      <td>M4T</td>
      <td>Central Toronto</td>
      <td>Moore Park / Summerhill East</td>
    </tr>
    <tr>
      <th>130</th>
      <td>M5T</td>
      <td>Downtown Toronto</td>
      <td>Kensington Market / Chinatown / Grange Park</td>
    </tr>
    <tr>
      <th>131</th>
      <td>M6T</td>
      <td>Not assigned</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>132</th>
      <td>M7T</td>
      <td>Not assigned</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>133</th>
      <td>M8T</td>
      <td>Not assigned</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>134</th>
      <td>M9T</td>
      <td>Not assigned</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>135</th>
      <td>M1V</td>
      <td>Scarborough</td>
      <td>Milliken / Agincourt North / Steeles East / L'...</td>
    </tr>
    <tr>
      <th>136</th>
      <td>M2V</td>
      <td>Not assigned</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>137</th>
      <td>M3V</td>
      <td>Not assigned</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>138</th>
      <td>M4V</td>
      <td>Central Toronto</td>
      <td>Summerhill West / Rathnelly / South Hill / For...</td>
    </tr>
    <tr>
      <th>139</th>
      <td>M5V</td>
      <td>Downtown Toronto</td>
      <td>CN Tower / King and Spadina / Railway Lands / ...</td>
    </tr>
    <tr>
      <th>140</th>
      <td>M6V</td>
      <td>Not assigned</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>141</th>
      <td>M7V</td>
      <td>Not assigned</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>142</th>
      <td>M8V</td>
      <td>Etobicoke</td>
      <td>New Toronto / Mimico South / Humber Bay Shores</td>
    </tr>
    <tr>
      <th>143</th>
      <td>M9V</td>
      <td>Etobicoke</td>
      <td>South Steeles / Silverstone / Humbergate / Jam...</td>
    </tr>
    <tr>
      <th>144</th>
      <td>M1W</td>
      <td>Scarborough</td>
      <td>Steeles West / L'Amoreaux West</td>
    </tr>
    <tr>
      <th>145</th>
      <td>M2W</td>
      <td>Not assigned</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>146</th>
      <td>M3W</td>
      <td>Not assigned</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>147</th>
      <td>M4W</td>
      <td>Downtown Toronto</td>
      <td>Rosedale</td>
    </tr>
    <tr>
      <th>148</th>
      <td>M5W</td>
      <td>Downtown Toronto</td>
      <td>Stn A PO Boxes</td>
    </tr>
    <tr>
      <th>149</th>
      <td>M6W</td>
      <td>Not assigned</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>150</th>
      <td>M7W</td>
      <td>Not assigned</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>151</th>
      <td>M8W</td>
      <td>Etobicoke</td>
      <td>Alderwood / Long Branch</td>
    </tr>
    <tr>
      <th>152</th>
      <td>M9W</td>
      <td>Etobicoke</td>
      <td>Northwest</td>
    </tr>
    <tr>
      <th>153</th>
      <td>M1X</td>
      <td>Scarborough</td>
      <td>Upper Rouge</td>
    </tr>
    <tr>
      <th>154</th>
      <td>M2X</td>
      <td>Not assigned</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>155</th>
      <td>M3X</td>
      <td>Not assigned</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>156</th>
      <td>M4X</td>
      <td>Downtown Toronto</td>
      <td>St. James Town / Cabbagetown</td>
    </tr>
    <tr>
      <th>157</th>
      <td>M5X</td>
      <td>Downtown Toronto</td>
      <td>First Canadian Place / Underground city</td>
    </tr>
    <tr>
      <th>158</th>
      <td>M6X</td>
      <td>Not assigned</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>159</th>
      <td>M7X</td>
      <td>Not assigned</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>160</th>
      <td>M8X</td>
      <td>Etobicoke</td>
      <td>The Kingsway / Montgomery Road / Old Mill North</td>
    </tr>
    <tr>
      <th>161</th>
      <td>M9X</td>
      <td>Not assigned</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>162</th>
      <td>M1Y</td>
      <td>Not assigned</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>163</th>
      <td>M2Y</td>
      <td>Not assigned</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>164</th>
      <td>M3Y</td>
      <td>Not assigned</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>165</th>
      <td>M4Y</td>
      <td>Downtown Toronto</td>
      <td>Church and Wellesley</td>
    </tr>
    <tr>
      <th>166</th>
      <td>M5Y</td>
      <td>Not assigned</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>167</th>
      <td>M6Y</td>
      <td>Not assigned</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>168</th>
      <td>M7Y</td>
      <td>East Toronto</td>
      <td>Business reply mail Processing CentrE</td>
    </tr>
    <tr>
      <th>169</th>
      <td>M8Y</td>
      <td>Etobicoke</td>
      <td>Old Mill South / King's Mill Park / Sunnylea /...</td>
    </tr>
    <tr>
      <th>170</th>
      <td>M9Y</td>
      <td>Not assigned</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>171</th>
      <td>M1Z</td>
      <td>Not assigned</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>172</th>
      <td>M2Z</td>
      <td>Not assigned</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>173</th>
      <td>M3Z</td>
      <td>Not assigned</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>174</th>
      <td>M4Z</td>
      <td>Not assigned</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>175</th>
      <td>M5Z</td>
      <td>Not assigned</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>176</th>
      <td>M6Z</td>
      <td>Not assigned</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>177</th>
      <td>M7Z</td>
      <td>Not assigned</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>178</th>
      <td>M8Z</td>
      <td>Etobicoke</td>
      <td>Mimico NW / The Queensway West / South of Bloo...</td>
    </tr>
    <tr>
      <th>179</th>
      <td>M9Z</td>
      <td>Not assigned</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>




```python
print('The dataframe has {} boroughs and {} neighborhoods.'.format(
        len(neighborhood_row['Borough'].unique()),
        neighborhood_row.shape[0]
    )
)
```

    The dataframe has 11 boroughs and 180 neighborhoods.



Only process the cells that have an assigned borough. Ignore cells with a borough that is **Not assigned**. 



```python
canada_neighborhood = neighborhood_row[neighborhood_row['Borough'] != 'Not assigned'].reset_index(drop=True)
canada_neighborhood

```

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
      <td>Regent Park / Harbourfront</td>
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
    <tr>
      <th>5</th>
      <td>M9A</td>
      <td>Etobicoke</td>
      <td>Islington Avenue</td>
    </tr>
    <tr>
      <th>6</th>
      <td>M1B</td>
      <td>Scarborough</td>
      <td>Malvern / Rouge</td>
    </tr>
    <tr>
      <th>7</th>
      <td>M3B</td>
      <td>North York</td>
      <td>Don Mills</td>
    </tr>
    <tr>
      <th>8</th>
      <td>M4B</td>
      <td>East York</td>
      <td>Parkview Hill / Woodbine Gardens</td>
    </tr>
    <tr>
      <th>9</th>
      <td>M5B</td>
      <td>Downtown Toronto</td>
      <td>Garden District, Ryerson</td>
    </tr>
    <tr>
      <th>10</th>
      <td>M6B</td>
      <td>North York</td>
      <td>Glencairn</td>
    </tr>
    <tr>
      <th>11</th>
      <td>M9B</td>
      <td>Etobicoke</td>
      <td>West Deane Park / Princess Gardens / Martin Gr...</td>
    </tr>
    <tr>
      <th>12</th>
      <td>M1C</td>
      <td>Scarborough</td>
      <td>Rouge Hill / Port Union / Highland Creek</td>
    </tr>
    <tr>
      <th>13</th>
      <td>M3C</td>
      <td>North York</td>
      <td>Don Mills</td>
    </tr>
    <tr>
      <th>14</th>
      <td>M4C</td>
      <td>East York</td>
      <td>Woodbine Heights</td>
    </tr>
    <tr>
      <th>15</th>
      <td>M5C</td>
      <td>Downtown Toronto</td>
      <td>St. James Town</td>
    </tr>
    <tr>
      <th>16</th>
      <td>M6C</td>
      <td>York</td>
      <td>Humewood-Cedarvale</td>
    </tr>
    <tr>
      <th>17</th>
      <td>M9C</td>
      <td>Etobicoke</td>
      <td>Eringate / Bloordale Gardens / Old Burnhamthor...</td>
    </tr>
    <tr>
      <th>18</th>
      <td>M1E</td>
      <td>Scarborough</td>
      <td>Guildwood / Morningside / West Hill</td>
    </tr>
    <tr>
      <th>19</th>
      <td>M4E</td>
      <td>East Toronto</td>
      <td>The Beaches</td>
    </tr>
    <tr>
      <th>20</th>
      <td>M5E</td>
      <td>Downtown Toronto</td>
      <td>Berczy Park</td>
    </tr>
    <tr>
      <th>21</th>
      <td>M6E</td>
      <td>York</td>
      <td>Caledonia-Fairbanks</td>
    </tr>
    <tr>
      <th>22</th>
      <td>M1G</td>
      <td>Scarborough</td>
      <td>Woburn</td>
    </tr>
    <tr>
      <th>23</th>
      <td>M4G</td>
      <td>East York</td>
      <td>Leaside</td>
    </tr>
    <tr>
      <th>24</th>
      <td>M5G</td>
      <td>Downtown Toronto</td>
      <td>Central Bay Street</td>
    </tr>
    <tr>
      <th>25</th>
      <td>M6G</td>
      <td>Downtown Toronto</td>
      <td>Christie</td>
    </tr>
    <tr>
      <th>26</th>
      <td>M1H</td>
      <td>Scarborough</td>
      <td>Cedarbrae</td>
    </tr>
    <tr>
      <th>27</th>
      <td>M2H</td>
      <td>North York</td>
      <td>Hillcrest Village</td>
    </tr>
    <tr>
      <th>28</th>
      <td>M3H</td>
      <td>North York</td>
      <td>Bathurst Manor / Wilson Heights / Downsview North</td>
    </tr>
    <tr>
      <th>29</th>
      <td>M4H</td>
      <td>East York</td>
      <td>Thorncliffe Park</td>
    </tr>
    <tr>
      <th>30</th>
      <td>M5H</td>
      <td>Downtown Toronto</td>
      <td>Richmond / Adelaide / King</td>
    </tr>
    <tr>
      <th>31</th>
      <td>M6H</td>
      <td>West Toronto</td>
      <td>Dufferin / Dovercourt Village</td>
    </tr>
    <tr>
      <th>32</th>
      <td>M1J</td>
      <td>Scarborough</td>
      <td>Scarborough Village</td>
    </tr>
    <tr>
      <th>33</th>
      <td>M2J</td>
      <td>North York</td>
      <td>Fairview / Henry Farm / Oriole</td>
    </tr>
    <tr>
      <th>34</th>
      <td>M3J</td>
      <td>North York</td>
      <td>Northwood Park / York University</td>
    </tr>
    <tr>
      <th>35</th>
      <td>M4J</td>
      <td>East York</td>
      <td>East Toronto</td>
    </tr>
    <tr>
      <th>36</th>
      <td>M5J</td>
      <td>Downtown Toronto</td>
      <td>Harbourfront East / Union Station / Toronto Is...</td>
    </tr>
    <tr>
      <th>37</th>
      <td>M6J</td>
      <td>West Toronto</td>
      <td>Little Portugal / Trinity</td>
    </tr>
    <tr>
      <th>38</th>
      <td>M1K</td>
      <td>Scarborough</td>
      <td>Kennedy Park / Ionview / East Birchmount Park</td>
    </tr>
    <tr>
      <th>39</th>
      <td>M2K</td>
      <td>North York</td>
      <td>Bayview Village</td>
    </tr>
    <tr>
      <th>40</th>
      <td>M3K</td>
      <td>North York</td>
      <td>Downsview</td>
    </tr>
    <tr>
      <th>41</th>
      <td>M4K</td>
      <td>East Toronto</td>
      <td>The Danforth West / Riverdale</td>
    </tr>
    <tr>
      <th>42</th>
      <td>M5K</td>
      <td>Downtown Toronto</td>
      <td>Toronto Dominion Centre / Design Exchange</td>
    </tr>
    <tr>
      <th>43</th>
      <td>M6K</td>
      <td>West Toronto</td>
      <td>Brockton / Parkdale Village / Exhibition Place</td>
    </tr>
    <tr>
      <th>44</th>
      <td>M1L</td>
      <td>Scarborough</td>
      <td>Golden Mile / Clairlea / Oakridge</td>
    </tr>
    <tr>
      <th>45</th>
      <td>M2L</td>
      <td>North York</td>
      <td>York Mills / Silver Hills</td>
    </tr>
    <tr>
      <th>46</th>
      <td>M3L</td>
      <td>North York</td>
      <td>Downsview</td>
    </tr>
    <tr>
      <th>47</th>
      <td>M4L</td>
      <td>East Toronto</td>
      <td>India Bazaar / The Beaches West</td>
    </tr>
    <tr>
      <th>48</th>
      <td>M5L</td>
      <td>Downtown Toronto</td>
      <td>Commerce Court / Victoria Hotel</td>
    </tr>
    <tr>
      <th>49</th>
      <td>M6L</td>
      <td>North York</td>
      <td>North Park / Maple Leaf Park / Upwood Park</td>
    </tr>
    <tr>
      <th>50</th>
      <td>M9L</td>
      <td>North York</td>
      <td>Humber Summit</td>
    </tr>
    <tr>
      <th>51</th>
      <td>M1M</td>
      <td>Scarborough</td>
      <td>Cliffside / Cliffcrest / Scarborough Village West</td>
    </tr>
    <tr>
      <th>52</th>
      <td>M2M</td>
      <td>North York</td>
      <td>Willowdale / Newtonbrook</td>
    </tr>
    <tr>
      <th>53</th>
      <td>M3M</td>
      <td>North York</td>
      <td>Downsview</td>
    </tr>
    <tr>
      <th>54</th>
      <td>M4M</td>
      <td>East Toronto</td>
      <td>Studio District</td>
    </tr>
    <tr>
      <th>55</th>
      <td>M5M</td>
      <td>North York</td>
      <td>Bedford Park / Lawrence Manor East</td>
    </tr>
    <tr>
      <th>56</th>
      <td>M6M</td>
      <td>York</td>
      <td>Del Ray / Mount Dennis / Keelsdale and Silvert...</td>
    </tr>
    <tr>
      <th>57</th>
      <td>M9M</td>
      <td>North York</td>
      <td>Humberlea / Emery</td>
    </tr>
    <tr>
      <th>58</th>
      <td>M1N</td>
      <td>Scarborough</td>
      <td>Birch Cliff / Cliffside West</td>
    </tr>
    <tr>
      <th>59</th>
      <td>M2N</td>
      <td>North York</td>
      <td>Willowdale</td>
    </tr>
    <tr>
      <th>60</th>
      <td>M3N</td>
      <td>North York</td>
      <td>Downsview</td>
    </tr>
    <tr>
      <th>61</th>
      <td>M4N</td>
      <td>Central Toronto</td>
      <td>Lawrence Park</td>
    </tr>
    <tr>
      <th>62</th>
      <td>M5N</td>
      <td>Central Toronto</td>
      <td>Roselawn</td>
    </tr>
    <tr>
      <th>63</th>
      <td>M6N</td>
      <td>York</td>
      <td>Runnymede / The Junction North</td>
    </tr>
    <tr>
      <th>64</th>
      <td>M9N</td>
      <td>York</td>
      <td>Weston</td>
    </tr>
    <tr>
      <th>65</th>
      <td>M1P</td>
      <td>Scarborough</td>
      <td>Dorset Park / Wexford Heights / Scarborough To...</td>
    </tr>
    <tr>
      <th>66</th>
      <td>M2P</td>
      <td>North York</td>
      <td>York Mills West</td>
    </tr>
    <tr>
      <th>67</th>
      <td>M4P</td>
      <td>Central Toronto</td>
      <td>Davisville North</td>
    </tr>
    <tr>
      <th>68</th>
      <td>M5P</td>
      <td>Central Toronto</td>
      <td>Forest Hill North &amp; West</td>
    </tr>
    <tr>
      <th>69</th>
      <td>M6P</td>
      <td>West Toronto</td>
      <td>High Park / The Junction South</td>
    </tr>
    <tr>
      <th>70</th>
      <td>M9P</td>
      <td>Etobicoke</td>
      <td>Westmount</td>
    </tr>
    <tr>
      <th>71</th>
      <td>M1R</td>
      <td>Scarborough</td>
      <td>Wexford / Maryvale</td>
    </tr>
    <tr>
      <th>72</th>
      <td>M2R</td>
      <td>North York</td>
      <td>Willowdale</td>
    </tr>
    <tr>
      <th>73</th>
      <td>M4R</td>
      <td>Central Toronto</td>
      <td>North Toronto West</td>
    </tr>
    <tr>
      <th>74</th>
      <td>M5R</td>
      <td>Central Toronto</td>
      <td>The Annex / North Midtown / Yorkville</td>
    </tr>
    <tr>
      <th>75</th>
      <td>M6R</td>
      <td>West Toronto</td>
      <td>Parkdale / Roncesvalles</td>
    </tr>
    <tr>
      <th>76</th>
      <td>M7R</td>
      <td>Mississauga</td>
      <td>Canada Post Gateway Processing Centre</td>
    </tr>
    <tr>
      <th>77</th>
      <td>M9R</td>
      <td>Etobicoke</td>
      <td>Kingsview Village / St. Phillips / Martin Grov...</td>
    </tr>
    <tr>
      <th>78</th>
      <td>M1S</td>
      <td>Scarborough</td>
      <td>Agincourt</td>
    </tr>
    <tr>
      <th>79</th>
      <td>M4S</td>
      <td>Central Toronto</td>
      <td>Davisville</td>
    </tr>
    <tr>
      <th>80</th>
      <td>M5S</td>
      <td>Downtown Toronto</td>
      <td>University of Toronto / Harbord</td>
    </tr>
    <tr>
      <th>81</th>
      <td>M6S</td>
      <td>West Toronto</td>
      <td>Runnymede / Swansea</td>
    </tr>
    <tr>
      <th>82</th>
      <td>M1T</td>
      <td>Scarborough</td>
      <td>Clarks Corners / Tam O'Shanter / Sullivan</td>
    </tr>
    <tr>
      <th>83</th>
      <td>M4T</td>
      <td>Central Toronto</td>
      <td>Moore Park / Summerhill East</td>
    </tr>
    <tr>
      <th>84</th>
      <td>M5T</td>
      <td>Downtown Toronto</td>
      <td>Kensington Market / Chinatown / Grange Park</td>
    </tr>
    <tr>
      <th>85</th>
      <td>M1V</td>
      <td>Scarborough</td>
      <td>Milliken / Agincourt North / Steeles East / L'...</td>
    </tr>
    <tr>
      <th>86</th>
      <td>M4V</td>
      <td>Central Toronto</td>
      <td>Summerhill West / Rathnelly / South Hill / For...</td>
    </tr>
    <tr>
      <th>87</th>
      <td>M5V</td>
      <td>Downtown Toronto</td>
      <td>CN Tower / King and Spadina / Railway Lands / ...</td>
    </tr>
    <tr>
      <th>88</th>
      <td>M8V</td>
      <td>Etobicoke</td>
      <td>New Toronto / Mimico South / Humber Bay Shores</td>
    </tr>
    <tr>
      <th>89</th>
      <td>M9V</td>
      <td>Etobicoke</td>
      <td>South Steeles / Silverstone / Humbergate / Jam...</td>
    </tr>
    <tr>
      <th>90</th>
      <td>M1W</td>
      <td>Scarborough</td>
      <td>Steeles West / L'Amoreaux West</td>
    </tr>
    <tr>
      <th>91</th>
      <td>M4W</td>
      <td>Downtown Toronto</td>
      <td>Rosedale</td>
    </tr>
    <tr>
      <th>92</th>
      <td>M5W</td>
      <td>Downtown Toronto</td>
      <td>Stn A PO Boxes</td>
    </tr>
    <tr>
      <th>93</th>
      <td>M8W</td>
      <td>Etobicoke</td>
      <td>Alderwood / Long Branch</td>
    </tr>
    <tr>
      <th>94</th>
      <td>M9W</td>
      <td>Etobicoke</td>
      <td>Northwest</td>
    </tr>
    <tr>
      <th>95</th>
      <td>M1X</td>
      <td>Scarborough</td>
      <td>Upper Rouge</td>
    </tr>
    <tr>
      <th>96</th>
      <td>M4X</td>
      <td>Downtown Toronto</td>
      <td>St. James Town / Cabbagetown</td>
    </tr>
    <tr>
      <th>97</th>
      <td>M5X</td>
      <td>Downtown Toronto</td>
      <td>First Canadian Place / Underground city</td>
    </tr>
    <tr>
      <th>98</th>
      <td>M8X</td>
      <td>Etobicoke</td>
      <td>The Kingsway / Montgomery Road / Old Mill North</td>
    </tr>
    <tr>
      <th>99</th>
      <td>M4Y</td>
      <td>Downtown Toronto</td>
      <td>Church and Wellesley</td>
    </tr>
    <tr>
      <th>100</th>
      <td>M7Y</td>
      <td>East Toronto</td>
      <td>Business reply mail Processing CentrE</td>
    </tr>
    <tr>
      <th>101</th>
      <td>M8Y</td>
      <td>Etobicoke</td>
      <td>Old Mill South / King's Mill Park / Sunnylea /...</td>
    </tr>
    <tr>
      <th>102</th>
      <td>M8Z</td>
      <td>Etobicoke</td>
      <td>Mimico NW / The Queensway West / South of Bloo...</td>
    </tr>
  </tbody>
</table>


``
More than one neighborhood can exist in one postal code area. For example, in the table on the Wikipedia page, you will notice that M5A is listed twice and has two neighborhoods: Harbourfront and Regent Park. These two rows will be combined into one row with the neighborhoods separated with a comma as shown in row 11 in the above table.
``


```python
canada_neighborhood[canada_neighborhood['Postal code'] == 'M5A']

#but there are more itens to be replaced let's see how it goes
```
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
      <th>2</th>
      <td>M5A</td>
      <td>Downtown Toronto</td>
      <td>Regent Park / Harbourfront</td>
    </tr>
  </tbody>
</table>
</div>


```python
#Removing the slash from the 'Neighborhood'column to replace by comma

canada_neighborhood['Neighborhood']= canada_neighborhood.Neighborhood.str.replace('Regent Park / Harbourfront','Regent Park , Harbourfront')
canada_neighborhood
```

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
    <tr>
      <th>5</th>
      <td>M9A</td>
      <td>Etobicoke</td>
      <td>Islington Avenue</td>
    </tr>
    <tr>
      <th>6</th>
      <td>M1B</td>
      <td>Scarborough</td>
      <td>Malvern / Rouge</td>
    </tr>
    <tr>
      <th>7</th>
      <td>M3B</td>
      <td>North York</td>
      <td>Don Mills</td>
    </tr>
    <tr>
      <th>8</th>
      <td>M4B</td>
      <td>East York</td>
      <td>Parkview Hill / Woodbine Gardens</td>
    </tr>
    <tr>
      <th>9</th>
      <td>M5B</td>
      <td>Downtown Toronto</td>
      <td>Garden District, Ryerson</td>
    </tr>
    <tr>
      <th>10</th>
      <td>M6B</td>
      <td>North York</td>
      <td>Glencairn</td>
    </tr>
    <tr>
      <th>11</th>
      <td>M9B</td>
      <td>Etobicoke</td>
      <td>West Deane Park / Princess Gardens / Martin Gr...</td>
    </tr>
    <tr>
      <th>12</th>
      <td>M1C</td>
      <td>Scarborough</td>
      <td>Rouge Hill / Port Union / Highland Creek</td>
    </tr>
    <tr>
      <th>13</th>
      <td>M3C</td>
      <td>North York</td>
      <td>Don Mills</td>
    </tr>
    <tr>
      <th>14</th>
      <td>M4C</td>
      <td>East York</td>
      <td>Woodbine Heights</td>
    </tr>
    <tr>
      <th>15</th>
      <td>M5C</td>
      <td>Downtown Toronto</td>
      <td>St. James Town</td>
    </tr>
    <tr>
      <th>16</th>
      <td>M6C</td>
      <td>York</td>
      <td>Humewood-Cedarvale</td>
    </tr>
    <tr>
      <th>17</th>
      <td>M9C</td>
      <td>Etobicoke</td>
      <td>Eringate / Bloordale Gardens / Old Burnhamthor...</td>
    </tr>
    <tr>
      <th>18</th>
      <td>M1E</td>
      <td>Scarborough</td>
      <td>Guildwood / Morningside / West Hill</td>
    </tr>
    <tr>
      <th>19</th>
      <td>M4E</td>
      <td>East Toronto</td>
      <td>The Beaches</td>
    </tr>
    <tr>
      <th>20</th>
      <td>M5E</td>
      <td>Downtown Toronto</td>
      <td>Berczy Park</td>
    </tr>
    <tr>
      <th>21</th>
      <td>M6E</td>
      <td>York</td>
      <td>Caledonia-Fairbanks</td>
    </tr>
    <tr>
      <th>22</th>
      <td>M1G</td>
      <td>Scarborough</td>
      <td>Woburn</td>
    </tr>
    <tr>
      <th>23</th>
      <td>M4G</td>
      <td>East York</td>
      <td>Leaside</td>
    </tr>
    <tr>
      <th>24</th>
      <td>M5G</td>
      <td>Downtown Toronto</td>
      <td>Central Bay Street</td>
    </tr>
    <tr>
      <th>25</th>
      <td>M6G</td>
      <td>Downtown Toronto</td>
      <td>Christie</td>
    </tr>
    <tr>
      <th>26</th>
      <td>M1H</td>
      <td>Scarborough</td>
      <td>Cedarbrae</td>
    </tr>
    <tr>
      <th>27</th>
      <td>M2H</td>
      <td>North York</td>
      <td>Hillcrest Village</td>
    </tr>
    <tr>
      <th>28</th>
      <td>M3H</td>
      <td>North York</td>
      <td>Bathurst Manor / Wilson Heights / Downsview North</td>
    </tr>
    <tr>
      <th>29</th>
      <td>M4H</td>
      <td>East York</td>
      <td>Thorncliffe Park</td>
    </tr>
    <tr>
      <th>30</th>
      <td>M5H</td>
      <td>Downtown Toronto</td>
      <td>Richmond / Adelaide / King</td>
    </tr>
    <tr>
      <th>31</th>
      <td>M6H</td>
      <td>West Toronto</td>
      <td>Dufferin / Dovercourt Village</td>
    </tr>
    <tr>
      <th>32</th>
      <td>M1J</td>
      <td>Scarborough</td>
      <td>Scarborough Village</td>
    </tr>
    <tr>
      <th>33</th>
      <td>M2J</td>
      <td>North York</td>
      <td>Fairview / Henry Farm / Oriole</td>
    </tr>
    <tr>
      <th>34</th>
      <td>M3J</td>
      <td>North York</td>
      <td>Northwood Park / York University</td>
    </tr>
    <tr>
      <th>35</th>
      <td>M4J</td>
      <td>East York</td>
      <td>East Toronto</td>
    </tr>
    <tr>
      <th>36</th>
      <td>M5J</td>
      <td>Downtown Toronto</td>
      <td>Harbourfront East / Union Station / Toronto Is...</td>
    </tr>
    <tr>
      <th>37</th>
      <td>M6J</td>
      <td>West Toronto</td>
      <td>Little Portugal / Trinity</td>
    </tr>
    <tr>
      <th>38</th>
      <td>M1K</td>
      <td>Scarborough</td>
      <td>Kennedy Park / Ionview / East Birchmount Park</td>
    </tr>
    <tr>
      <th>39</th>
      <td>M2K</td>
      <td>North York</td>
      <td>Bayview Village</td>
    </tr>
    <tr>
      <th>40</th>
      <td>M3K</td>
      <td>North York</td>
      <td>Downsview</td>
    </tr>
    <tr>
      <th>41</th>
      <td>M4K</td>
      <td>East Toronto</td>
      <td>The Danforth West / Riverdale</td>
    </tr>
    <tr>
      <th>42</th>
      <td>M5K</td>
      <td>Downtown Toronto</td>
      <td>Toronto Dominion Centre / Design Exchange</td>
    </tr>
    <tr>
      <th>43</th>
      <td>M6K</td>
      <td>West Toronto</td>
      <td>Brockton / Parkdale Village / Exhibition Place</td>
    </tr>
    <tr>
      <th>44</th>
      <td>M1L</td>
      <td>Scarborough</td>
      <td>Golden Mile / Clairlea / Oakridge</td>
    </tr>
    <tr>
      <th>45</th>
      <td>M2L</td>
      <td>North York</td>
      <td>York Mills / Silver Hills</td>
    </tr>
    <tr>
      <th>46</th>
      <td>M3L</td>
      <td>North York</td>
      <td>Downsview</td>
    </tr>
    <tr>
      <th>47</th>
      <td>M4L</td>
      <td>East Toronto</td>
      <td>India Bazaar / The Beaches West</td>
    </tr>
    <tr>
      <th>48</th>
      <td>M5L</td>
      <td>Downtown Toronto</td>
      <td>Commerce Court / Victoria Hotel</td>
    </tr>
    <tr>
      <th>49</th>
      <td>M6L</td>
      <td>North York</td>
      <td>North Park / Maple Leaf Park / Upwood Park</td>
    </tr>
    <tr>
      <th>50</th>
      <td>M9L</td>
      <td>North York</td>
      <td>Humber Summit</td>
    </tr>
    <tr>
      <th>51</th>
      <td>M1M</td>
      <td>Scarborough</td>
      <td>Cliffside / Cliffcrest / Scarborough Village West</td>
    </tr>
    <tr>
      <th>52</th>
      <td>M2M</td>
      <td>North York</td>
      <td>Willowdale / Newtonbrook</td>
    </tr>
    <tr>
      <th>53</th>
      <td>M3M</td>
      <td>North York</td>
      <td>Downsview</td>
    </tr>
    <tr>
      <th>54</th>
      <td>M4M</td>
      <td>East Toronto</td>
      <td>Studio District</td>
    </tr>
    <tr>
      <th>55</th>
      <td>M5M</td>
      <td>North York</td>
      <td>Bedford Park / Lawrence Manor East</td>
    </tr>
    <tr>
      <th>56</th>
      <td>M6M</td>
      <td>York</td>
      <td>Del Ray / Mount Dennis / Keelsdale and Silvert...</td>
    </tr>
    <tr>
      <th>57</th>
      <td>M9M</td>
      <td>North York</td>
      <td>Humberlea / Emery</td>
    </tr>
    <tr>
      <th>58</th>
      <td>M1N</td>
      <td>Scarborough</td>
      <td>Birch Cliff / Cliffside West</td>
    </tr>
    <tr>
      <th>59</th>
      <td>M2N</td>
      <td>North York</td>
      <td>Willowdale</td>
    </tr>
    <tr>
      <th>60</th>
      <td>M3N</td>
      <td>North York</td>
      <td>Downsview</td>
    </tr>
    <tr>
      <th>61</th>
      <td>M4N</td>
      <td>Central Toronto</td>
      <td>Lawrence Park</td>
    </tr>
    <tr>
      <th>62</th>
      <td>M5N</td>
      <td>Central Toronto</td>
      <td>Roselawn</td>
    </tr>
    <tr>
      <th>63</th>
      <td>M6N</td>
      <td>York</td>
      <td>Runnymede / The Junction North</td>
    </tr>
    <tr>
      <th>64</th>
      <td>M9N</td>
      <td>York</td>
      <td>Weston</td>
    </tr>
    <tr>
      <th>65</th>
      <td>M1P</td>
      <td>Scarborough</td>
      <td>Dorset Park / Wexford Heights / Scarborough To...</td>
    </tr>
    <tr>
      <th>66</th>
      <td>M2P</td>
      <td>North York</td>
      <td>York Mills West</td>
    </tr>
    <tr>
      <th>67</th>
      <td>M4P</td>
      <td>Central Toronto</td>
      <td>Davisville North</td>
    </tr>
    <tr>
      <th>68</th>
      <td>M5P</td>
      <td>Central Toronto</td>
      <td>Forest Hill North &amp; West</td>
    </tr>
    <tr>
      <th>69</th>
      <td>M6P</td>
      <td>West Toronto</td>
      <td>High Park / The Junction South</td>
    </tr>
    <tr>
      <th>70</th>
      <td>M9P</td>
      <td>Etobicoke</td>
      <td>Westmount</td>
    </tr>
    <tr>
      <th>71</th>
      <td>M1R</td>
      <td>Scarborough</td>
      <td>Wexford / Maryvale</td>
    </tr>
    <tr>
      <th>72</th>
      <td>M2R</td>
      <td>North York</td>
      <td>Willowdale</td>
    </tr>
    <tr>
      <th>73</th>
      <td>M4R</td>
      <td>Central Toronto</td>
      <td>North Toronto West</td>
    </tr>
    <tr>
      <th>74</th>
      <td>M5R</td>
      <td>Central Toronto</td>
      <td>The Annex / North Midtown / Yorkville</td>
    </tr>
    <tr>
      <th>75</th>
      <td>M6R</td>
      <td>West Toronto</td>
      <td>Parkdale / Roncesvalles</td>
    </tr>
    <tr>
      <th>76</th>
      <td>M7R</td>
      <td>Mississauga</td>
      <td>Canada Post Gateway Processing Centre</td>
    </tr>
    <tr>
      <th>77</th>
      <td>M9R</td>
      <td>Etobicoke</td>
      <td>Kingsview Village / St. Phillips / Martin Grov...</td>
    </tr>
    <tr>
      <th>78</th>
      <td>M1S</td>
      <td>Scarborough</td>
      <td>Agincourt</td>
    </tr>
    <tr>
      <th>79</th>
      <td>M4S</td>
      <td>Central Toronto</td>
      <td>Davisville</td>
    </tr>
    <tr>
      <th>80</th>
      <td>M5S</td>
      <td>Downtown Toronto</td>
      <td>University of Toronto / Harbord</td>
    </tr>
    <tr>
      <th>81</th>
      <td>M6S</td>
      <td>West Toronto</td>
      <td>Runnymede / Swansea</td>
    </tr>
    <tr>
      <th>82</th>
      <td>M1T</td>
      <td>Scarborough</td>
      <td>Clarks Corners / Tam O'Shanter / Sullivan</td>
    </tr>
    <tr>
      <th>83</th>
      <td>M4T</td>
      <td>Central Toronto</td>
      <td>Moore Park / Summerhill East</td>
    </tr>
    <tr>
      <th>84</th>
      <td>M5T</td>
      <td>Downtown Toronto</td>
      <td>Kensington Market / Chinatown / Grange Park</td>
    </tr>
    <tr>
      <th>85</th>
      <td>M1V</td>
      <td>Scarborough</td>
      <td>Milliken / Agincourt North / Steeles East / L'...</td>
    </tr>
    <tr>
      <th>86</th>
      <td>M4V</td>
      <td>Central Toronto</td>
      <td>Summerhill West / Rathnelly / South Hill / For...</td>
    </tr>
    <tr>
      <th>87</th>
      <td>M5V</td>
      <td>Downtown Toronto</td>
      <td>CN Tower / King and Spadina / Railway Lands / ...</td>
    </tr>
    <tr>
      <th>88</th>
      <td>M8V</td>
      <td>Etobicoke</td>
      <td>New Toronto / Mimico South / Humber Bay Shores</td>
    </tr>
    <tr>
      <th>89</th>
      <td>M9V</td>
      <td>Etobicoke</td>
      <td>South Steeles / Silverstone / Humbergate / Jam...</td>
    </tr>
    <tr>
      <th>90</th>
      <td>M1W</td>
      <td>Scarborough</td>
      <td>Steeles West / L'Amoreaux West</td>
    </tr>
    <tr>
      <th>91</th>
      <td>M4W</td>
      <td>Downtown Toronto</td>
      <td>Rosedale</td>
    </tr>
    <tr>
      <th>92</th>
      <td>M5W</td>
      <td>Downtown Toronto</td>
      <td>Stn A PO Boxes</td>
    </tr>
    <tr>
      <th>93</th>
      <td>M8W</td>
      <td>Etobicoke</td>
      <td>Alderwood / Long Branch</td>
    </tr>
    <tr>
      <th>94</th>
      <td>M9W</td>
      <td>Etobicoke</td>
      <td>Northwest</td>
    </tr>
    <tr>
      <th>95</th>
      <td>M1X</td>
      <td>Scarborough</td>
      <td>Upper Rouge</td>
    </tr>
    <tr>
      <th>96</th>
      <td>M4X</td>
      <td>Downtown Toronto</td>
      <td>St. James Town / Cabbagetown</td>
    </tr>
    <tr>
      <th>97</th>
      <td>M5X</td>
      <td>Downtown Toronto</td>
      <td>First Canadian Place / Underground city</td>
    </tr>
    <tr>
      <th>98</th>
      <td>M8X</td>
      <td>Etobicoke</td>
      <td>The Kingsway / Montgomery Road / Old Mill North</td>
    </tr>
    <tr>
      <th>99</th>
      <td>M4Y</td>
      <td>Downtown Toronto</td>
      <td>Church and Wellesley</td>
    </tr>
    <tr>
      <th>100</th>
      <td>M7Y</td>
      <td>East Toronto</td>
      <td>Business reply mail Processing CentrE</td>
    </tr>
    <tr>
      <th>101</th>
      <td>M8Y</td>
      <td>Etobicoke</td>
      <td>Old Mill South / King's Mill Park / Sunnylea /...</td>
    </tr>
    <tr>
      <th>102</th>
      <td>M8Z</td>
      <td>Etobicoke</td>
      <td>Mimico NW / The Queensway West / South of Bloo...</td>
    </tr>
  </tbody>
</table>
</div>



``If a cell has a borough but a Not assigned neighborhood, then the neighborhood will be the same as the borough ``


```python
canada_neighborhood.replace(canada_neighborhood['Neighborhood']== 'NaN', canada_neighborhood['Borough'])

```


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
    <tr>
      <th>5</th>
      <td>M9A</td>
      <td>Etobicoke</td>
      <td>Islington Avenue</td>
    </tr>
    <tr>
      <th>6</th>
      <td>M1B</td>
      <td>Scarborough</td>
      <td>Malvern / Rouge</td>
    </tr>
    <tr>
      <th>7</th>
      <td>M3B</td>
      <td>North York</td>
      <td>Don Mills</td>
    </tr>
    <tr>
      <th>8</th>
      <td>M4B</td>
      <td>East York</td>
      <td>Parkview Hill / Woodbine Gardens</td>
    </tr>
    <tr>
      <th>9</th>
      <td>M5B</td>
      <td>Downtown Toronto</td>
      <td>Garden District, Ryerson</td>
    </tr>
    <tr>
      <th>10</th>
      <td>M6B</td>
      <td>North York</td>
      <td>Glencairn</td>
    </tr>
    <tr>
      <th>11</th>
      <td>M9B</td>
      <td>Etobicoke</td>
      <td>West Deane Park / Princess Gardens / Martin Gr...</td>
    </tr>
    <tr>
      <th>12</th>
      <td>M1C</td>
      <td>Scarborough</td>
      <td>Rouge Hill / Port Union / Highland Creek</td>
    </tr>
    <tr>
      <th>13</th>
      <td>M3C</td>
      <td>North York</td>
      <td>Don Mills</td>
    </tr>
    <tr>
      <th>14</th>
      <td>M4C</td>
      <td>East York</td>
      <td>Woodbine Heights</td>
    </tr>
    <tr>
      <th>15</th>
      <td>M5C</td>
      <td>Downtown Toronto</td>
      <td>St. James Town</td>
    </tr>
    <tr>
      <th>16</th>
      <td>M6C</td>
      <td>York</td>
      <td>Humewood-Cedarvale</td>
    </tr>
    <tr>
      <th>17</th>
      <td>M9C</td>
      <td>Etobicoke</td>
      <td>Eringate / Bloordale Gardens / Old Burnhamthor...</td>
    </tr>
    <tr>
      <th>18</th>
      <td>M1E</td>
      <td>Scarborough</td>
      <td>Guildwood / Morningside / West Hill</td>
    </tr>
    <tr>
      <th>19</th>
      <td>M4E</td>
      <td>East Toronto</td>
      <td>The Beaches</td>
    </tr>
    <tr>
      <th>20</th>
      <td>M5E</td>
      <td>Downtown Toronto</td>
      <td>Berczy Park</td>
    </tr>
    <tr>
      <th>21</th>
      <td>M6E</td>
      <td>York</td>
      <td>Caledonia-Fairbanks</td>
    </tr>
    <tr>
      <th>22</th>
      <td>M1G</td>
      <td>Scarborough</td>
      <td>Woburn</td>
    </tr>
    <tr>
      <th>23</th>
      <td>M4G</td>
      <td>East York</td>
      <td>Leaside</td>
    </tr>
    <tr>
      <th>24</th>
      <td>M5G</td>
      <td>Downtown Toronto</td>
      <td>Central Bay Street</td>
    </tr>
    <tr>
      <th>25</th>
      <td>M6G</td>
      <td>Downtown Toronto</td>
      <td>Christie</td>
    </tr>
    <tr>
      <th>26</th>
      <td>M1H</td>
      <td>Scarborough</td>
      <td>Cedarbrae</td>
    </tr>
    <tr>
      <th>27</th>
      <td>M2H</td>
      <td>North York</td>
      <td>Hillcrest Village</td>
    </tr>
    <tr>
      <th>28</th>
      <td>M3H</td>
      <td>North York</td>
      <td>Bathurst Manor / Wilson Heights / Downsview North</td>
    </tr>
    <tr>
      <th>29</th>
      <td>M4H</td>
      <td>East York</td>
      <td>Thorncliffe Park</td>
    </tr>
    <tr>
      <th>30</th>
      <td>M5H</td>
      <td>Downtown Toronto</td>
      <td>Richmond / Adelaide / King</td>
    </tr>
    <tr>
      <th>31</th>
      <td>M6H</td>
      <td>West Toronto</td>
      <td>Dufferin / Dovercourt Village</td>
    </tr>
    <tr>
      <th>32</th>
      <td>M1J</td>
      <td>Scarborough</td>
      <td>Scarborough Village</td>
    </tr>
    <tr>
      <th>33</th>
      <td>M2J</td>
      <td>North York</td>
      <td>Fairview / Henry Farm / Oriole</td>
    </tr>
    <tr>
      <th>34</th>
      <td>M3J</td>
      <td>North York</td>
      <td>Northwood Park / York University</td>
    </tr>
    <tr>
      <th>35</th>
      <td>M4J</td>
      <td>East York</td>
      <td>East Toronto</td>
    </tr>
    <tr>
      <th>36</th>
      <td>M5J</td>
      <td>Downtown Toronto</td>
      <td>Harbourfront East / Union Station / Toronto Is...</td>
    </tr>
    <tr>
      <th>37</th>
      <td>M6J</td>
      <td>West Toronto</td>
      <td>Little Portugal / Trinity</td>
    </tr>
    <tr>
      <th>38</th>
      <td>M1K</td>
      <td>Scarborough</td>
      <td>Kennedy Park / Ionview / East Birchmount Park</td>
    </tr>
    <tr>
      <th>39</th>
      <td>M2K</td>
      <td>North York</td>
      <td>Bayview Village</td>
    </tr>
    <tr>
      <th>40</th>
      <td>M3K</td>
      <td>North York</td>
      <td>Downsview</td>
    </tr>
    <tr>
      <th>41</th>
      <td>M4K</td>
      <td>East Toronto</td>
      <td>The Danforth West / Riverdale</td>
    </tr>
    <tr>
      <th>42</th>
      <td>M5K</td>
      <td>Downtown Toronto</td>
      <td>Toronto Dominion Centre / Design Exchange</td>
    </tr>
    <tr>
      <th>43</th>
      <td>M6K</td>
      <td>West Toronto</td>
      <td>Brockton / Parkdale Village / Exhibition Place</td>
    </tr>
    <tr>
      <th>44</th>
      <td>M1L</td>
      <td>Scarborough</td>
      <td>Golden Mile / Clairlea / Oakridge</td>
    </tr>
    <tr>
      <th>45</th>
      <td>M2L</td>
      <td>North York</td>
      <td>York Mills / Silver Hills</td>
    </tr>
    <tr>
      <th>46</th>
      <td>M3L</td>
      <td>North York</td>
      <td>Downsview</td>
    </tr>
    <tr>
      <th>47</th>
      <td>M4L</td>
      <td>East Toronto</td>
      <td>India Bazaar / The Beaches West</td>
    </tr>
    <tr>
      <th>48</th>
      <td>M5L</td>
      <td>Downtown Toronto</td>
      <td>Commerce Court / Victoria Hotel</td>
    </tr>
    <tr>
      <th>49</th>
      <td>M6L</td>
      <td>North York</td>
      <td>North Park / Maple Leaf Park / Upwood Park</td>
    </tr>
    <tr>
      <th>50</th>
      <td>M9L</td>
      <td>North York</td>
      <td>Humber Summit</td>
    </tr>
    <tr>
      <th>51</th>
      <td>M1M</td>
      <td>Scarborough</td>
      <td>Cliffside / Cliffcrest / Scarborough Village West</td>
    </tr>
    <tr>
      <th>52</th>
      <td>M2M</td>
      <td>North York</td>
      <td>Willowdale / Newtonbrook</td>
    </tr>
    <tr>
      <th>53</th>
      <td>M3M</td>
      <td>North York</td>
      <td>Downsview</td>
    </tr>
    <tr>
      <th>54</th>
      <td>M4M</td>
      <td>East Toronto</td>
      <td>Studio District</td>
    </tr>
    <tr>
      <th>55</th>
      <td>M5M</td>
      <td>North York</td>
      <td>Bedford Park / Lawrence Manor East</td>
    </tr>
    <tr>
      <th>56</th>
      <td>M6M</td>
      <td>York</td>
      <td>Del Ray / Mount Dennis / Keelsdale and Silvert...</td>
    </tr>
    <tr>
      <th>57</th>
      <td>M9M</td>
      <td>North York</td>
      <td>Humberlea / Emery</td>
    </tr>
    <tr>
      <th>58</th>
      <td>M1N</td>
      <td>Scarborough</td>
      <td>Birch Cliff / Cliffside West</td>
    </tr>
    <tr>
      <th>59</th>
      <td>M2N</td>
      <td>North York</td>
      <td>Willowdale</td>
    </tr>
    <tr>
      <th>60</th>
      <td>M3N</td>
      <td>North York</td>
      <td>Downsview</td>
    </tr>
    <tr>
      <th>61</th>
      <td>M4N</td>
      <td>Central Toronto</td>
      <td>Lawrence Park</td>
    </tr>
    <tr>
      <th>62</th>
      <td>M5N</td>
      <td>Central Toronto</td>
      <td>Roselawn</td>
    </tr>
    <tr>
      <th>63</th>
      <td>M6N</td>
      <td>York</td>
      <td>Runnymede / The Junction North</td>
    </tr>
    <tr>
      <th>64</th>
      <td>M9N</td>
      <td>York</td>
      <td>Weston</td>
    </tr>
    <tr>
      <th>65</th>
      <td>M1P</td>
      <td>Scarborough</td>
      <td>Dorset Park / Wexford Heights / Scarborough To...</td>
    </tr>
    <tr>
      <th>66</th>
      <td>M2P</td>
      <td>North York</td>
      <td>York Mills West</td>
    </tr>
    <tr>
      <th>67</th>
      <td>M4P</td>
      <td>Central Toronto</td>
      <td>Davisville North</td>
    </tr>
    <tr>
      <th>68</th>
      <td>M5P</td>
      <td>Central Toronto</td>
      <td>Forest Hill North &amp; West</td>
    </tr>
    <tr>
      <th>69</th>
      <td>M6P</td>
      <td>West Toronto</td>
      <td>High Park / The Junction South</td>
    </tr>
    <tr>
      <th>70</th>
      <td>M9P</td>
      <td>Etobicoke</td>
      <td>Westmount</td>
    </tr>
    <tr>
      <th>71</th>
      <td>M1R</td>
      <td>Scarborough</td>
      <td>Wexford / Maryvale</td>
    </tr>
    <tr>
      <th>72</th>
      <td>M2R</td>
      <td>North York</td>
      <td>Willowdale</td>
    </tr>
    <tr>
      <th>73</th>
      <td>M4R</td>
      <td>Central Toronto</td>
      <td>North Toronto West</td>
    </tr>
    <tr>
      <th>74</th>
      <td>M5R</td>
      <td>Central Toronto</td>
      <td>The Annex / North Midtown / Yorkville</td>
    </tr>
    <tr>
      <th>75</th>
      <td>M6R</td>
      <td>West Toronto</td>
      <td>Parkdale / Roncesvalles</td>
    </tr>
    <tr>
      <th>76</th>
      <td>M7R</td>
      <td>Mississauga</td>
      <td>Canada Post Gateway Processing Centre</td>
    </tr>
    <tr>
      <th>77</th>
      <td>M9R</td>
      <td>Etobicoke</td>
      <td>Kingsview Village / St. Phillips / Martin Grov...</td>
    </tr>
    <tr>
      <th>78</th>
      <td>M1S</td>
      <td>Scarborough</td>
      <td>Agincourt</td>
    </tr>
    <tr>
      <th>79</th>
      <td>M4S</td>
      <td>Central Toronto</td>
      <td>Davisville</td>
    </tr>
    <tr>
      <th>80</th>
      <td>M5S</td>
      <td>Downtown Toronto</td>
      <td>University of Toronto / Harbord</td>
    </tr>
    <tr>
      <th>81</th>
      <td>M6S</td>
      <td>West Toronto</td>
      <td>Runnymede / Swansea</td>
    </tr>
    <tr>
      <th>82</th>
      <td>M1T</td>
      <td>Scarborough</td>
      <td>Clarks Corners / Tam O'Shanter / Sullivan</td>
    </tr>
    <tr>
      <th>83</th>
      <td>M4T</td>
      <td>Central Toronto</td>
      <td>Moore Park / Summerhill East</td>
    </tr>
    <tr>
      <th>84</th>
      <td>M5T</td>
      <td>Downtown Toronto</td>
      <td>Kensington Market / Chinatown / Grange Park</td>
    </tr>
    <tr>
      <th>85</th>
      <td>M1V</td>
      <td>Scarborough</td>
      <td>Milliken / Agincourt North / Steeles East / L'...</td>
    </tr>
    <tr>
      <th>86</th>
      <td>M4V</td>
      <td>Central Toronto</td>
      <td>Summerhill West / Rathnelly / South Hill / For...</td>
    </tr>
    <tr>
      <th>87</th>
      <td>M5V</td>
      <td>Downtown Toronto</td>
      <td>CN Tower / King and Spadina / Railway Lands / ...</td>
    </tr>
    <tr>
      <th>88</th>
      <td>M8V</td>
      <td>Etobicoke</td>
      <td>New Toronto / Mimico South / Humber Bay Shores</td>
    </tr>
    <tr>
      <th>89</th>
      <td>M9V</td>
      <td>Etobicoke</td>
      <td>South Steeles / Silverstone / Humbergate / Jam...</td>
    </tr>
    <tr>
      <th>90</th>
      <td>M1W</td>
      <td>Scarborough</td>
      <td>Steeles West / L'Amoreaux West</td>
    </tr>
    <tr>
      <th>91</th>
      <td>M4W</td>
      <td>Downtown Toronto</td>
      <td>Rosedale</td>
    </tr>
    <tr>
      <th>92</th>
      <td>M5W</td>
      <td>Downtown Toronto</td>
      <td>Stn A PO Boxes</td>
    </tr>
    <tr>
      <th>93</th>
      <td>M8W</td>
      <td>Etobicoke</td>
      <td>Alderwood / Long Branch</td>
    </tr>
    <tr>
      <th>94</th>
      <td>M9W</td>
      <td>Etobicoke</td>
      <td>Northwest</td>
    </tr>
    <tr>
      <th>95</th>
      <td>M1X</td>
      <td>Scarborough</td>
      <td>Upper Rouge</td>
    </tr>
    <tr>
      <th>96</th>
      <td>M4X</td>
      <td>Downtown Toronto</td>
      <td>St. James Town / Cabbagetown</td>
    </tr>
    <tr>
      <th>97</th>
      <td>M5X</td>
      <td>Downtown Toronto</td>
      <td>First Canadian Place / Underground city</td>
    </tr>
    <tr>
      <th>98</th>
      <td>M8X</td>
      <td>Etobicoke</td>
      <td>The Kingsway / Montgomery Road / Old Mill North</td>
    </tr>
    <tr>
      <th>99</th>
      <td>M4Y</td>
      <td>Downtown Toronto</td>
      <td>Church and Wellesley</td>
    </tr>
    <tr>
      <th>100</th>
      <td>M7Y</td>
      <td>East Toronto</td>
      <td>Business reply mail Processing CentrE</td>
    </tr>
    <tr>
      <th>101</th>
      <td>M8Y</td>
      <td>Etobicoke</td>
      <td>Old Mill South / King's Mill Park / Sunnylea /...</td>
    </tr>
    <tr>
      <th>102</th>
      <td>M8Z</td>
      <td>Etobicoke</td>
      <td>Mimico NW / The Queensway West / South of Bloo...</td>
    </tr>
  </tbody>
</table>


``In the last cell of your notebook, use the .shape method to print the number of rows of your dataframe.``



```python
print('The dataframe has now {} boroughs and {} neighborhoods.'.format(
        len(canada_neighborhood['Borough'].unique()),
        canada_neighborhood.shape[0]
    )
)
```

    The dataframe has now 10 boroughs and 103 neighborhoods.





``  Conclusion: 
After all the activity I conclude that our data frame has actually 103 neighborhoods. In other words 77 itens was treated as outliers. Even more there was decreased in 1 Borough.``
```
