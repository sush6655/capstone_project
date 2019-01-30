

```python
import numpy as np # library to handle data in a vectorized manner

import pandas as pd # library for data analsysis
pd.set_option('display.max_columns', None)
pd.set_option('display.max_rows', None)

from bs4 import BeautifulSoup
import requests

import json # library to handle JSON files

#!conda install -c conda-forge geopy --yes # uncomment this line if you haven't completed the Foursquare API lab
from geopy.geocoders import Nominatim # convert an address into latitude and longitude values

import requests # library to handle requests
from pandas.io.json import json_normalize # tranform JSON file into a pandas dataframe


# import k-means from clustering stage
from sklearn.cluster import KMeans

#!conda install -c conda-forge folium=0.5.0 --yes # uncomment this line if you haven't completed the Foursquare API lab
import folium # map rendering library

print('Libraries imported.')
```

    Libraries imported.



```python
page='https://en.wikipedia.org/wiki/List_of_postal_codes_of_Canada:_M'
doc= requests.get(wikipedia_link).text
```


```python
postcode_list = []  #List to hold the Postcodes
borough_list = []  #List to hold the Boroughs
neighbourhood_list = [] #List to hold the Neighbourhoods
```


```python
soup = BeautifulSoup(doc, 'html.parser')  #Initialize a BeatifulSoup object to parse the doc
table = soup.find('tbody') #Get the table
data = table.find_all('td') #Get the table data as a list
print(data[0].text , data[1].text , data[2].text) #check the first row
```

    M1A Not assigned Not assigned
    



```python
data_rows = len(data)
x = range(0,int(data_rows),3) #create a range to iterate through the list , always getting the first item (PostCode) of every row 
for index in x:
    if (data[index+1].text != "Not assigned"):  #Assure that the row doesn't contain "Not aassigned" value
        #Then get each value into the apropriate list
        if data[index].text not in postcode_list: #If the postcode is not in the list , append the whole row
            postcode_list.append(data[index].text)
            borough_list.append(data[index+1].text)
            neighbourhood_list.append(data[index+2].text.replace('\n','')) #We get rid of the '\n' from every neighbourhood data
        else:  #If it the postcode is already in the list , find its index and add the additional neighborhood in the neighbourhood list (comma seperated)
            position = postcode_list.index(data[index].text)
            neighbourhood_list[position] += ", "
            neighbourhood_list[position] += data[index+2].text.replace('\n','')
```


```python

```


```python
dic = {}
dic['Postcode'] = postcode_list
dic['Borough'] = borough_list
dic['Neighbourhood'] = neighbourhood_list
```


```python
df = pd.DataFrame.from_dict(dic)

print(df.head())
```

      Postcode           Borough                     Neighbourhood
    0      M3A        North York                         Parkwoods
    1      M4A        North York                  Victoria Village
    2      M5A  Downtown Toronto         Harbourfront, Regent Park
    3      M6A        North York  Lawrence Heights, Lawrence Manor
    4      M7A      Queen's Park                      Not assigned



```python
print(df.shape[0])
```

    103



```python
gdf = pd.read_csv('http://cocl.us/Geospatial_data') #Read the data csv
p = list(gdf['Postal Code']) #Get the data into lists
lat = list(gdf['Latitude'])
lon = list(gdf['Longitude'])

print(gdf.head())

```

      Postal Code   Latitude  Longitude
    0         M1B  43.806686 -79.194353
    1         M1C  43.784535 -79.160497
    2         M1E  43.763573 -79.188711
    3         M1G  43.770992 -79.216917
    4         M1H  43.773136 -79.239476



```python
lat_list = [] #Create two new lists to hold the appended data
lon_list = []

for x in postcode_list:  #fill the new lists based on the postcodes in df
        position = p.index(x)
        lat_list.append(lat[position])
        lon_list.append(lon[position])
```


```python
dic['Latitude'] = lat_list  #Append the new data in the previously created dictionary
dic['Longitude'] = lon_list

df = pd.DataFrame.from_dict(dic)  #Recreate the dataframe 

print(df.head())
```

      Postcode           Borough                     Neighbourhood   Latitude  \
    0      M3A        North York                         Parkwoods  43.753259   
    1      M4A        North York                  Victoria Village  43.725882   
    2      M5A  Downtown Toronto         Harbourfront, Regent Park  43.654260   
    3      M6A        North York  Lawrence Heights, Lawrence Manor  43.718518   
    4      M7A      Queen's Park                      Not assigned  43.662301   
    
       Longitude  
    0 -79.329656  
    1 -79.315572  
    2 -79.360636  
    3 -79.464763  
    4 -79.389494  



```python

```
