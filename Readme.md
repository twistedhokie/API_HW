

```python
#import dependencies
import matplotlib.pyplot as plt
import numpy as np
import requests
import pandas as pd
import openweathermapy.core as owm
from citipy import citipy
from pprint import pprint

from config import api_key
```


```python
#collect latitude and longitude data for getting a random city sample
lat = np.random.uniform(-90,90,100) 
lon = np.random.uniform(-180,180,100) 

print (lat)
print (lon)
```

    [-79.91987348  51.35755435  25.59645038 -34.28286766  21.82565451
     -38.65361601 -14.06257448  89.20009882 -84.91870997 -61.24147501
     -64.67457369  40.06466633  14.95924372  -3.17208738  13.37471162
      54.79722499  -8.48668904  83.94805031 -73.18101479 -62.39279926
      76.43665672 -24.50005579  10.46087611 -43.28870301  80.62893231
      82.24270808   9.13593071  64.91584384   9.42148174  65.36099265
      45.10727593 -51.85896516  -0.0979447  -64.25728036  28.74024663
      85.65615984 -75.54962297  22.06998375  17.13932386 -73.20697138
     -55.38509231 -31.57867456 -48.08396137 -71.40808835  18.31371691
     -73.60159715  71.14787707  22.04284819 -17.94004163  -1.79842453
       1.57537092 -74.76337166 -21.95966783 -41.96893751  30.36887598
     -78.75022224  63.1171714   25.9378952   67.57225611  69.38915797
     -68.47955021 -88.03187774 -79.52653383  26.77069374  -0.41363213
     -15.57369512 -63.41690922  25.33012948  88.95388529  31.53758226
     -69.6272899  -46.07702544 -36.41783241  -1.38012201 -11.19263862
      79.99286997  46.32808401 -60.57848785  83.89809789  -1.05566016
      49.29354092  62.30361862  -3.88128281   9.01676547  73.40939713
     -21.51784792  39.23580945  34.6412602   26.28987322  -0.33350358
     -71.31498997  -1.90413707  45.49054667  78.74460151 -10.20280377
     -13.25456242 -17.64672043  28.66768385 -32.92792116 -16.83769649]
    [-147.28968112  173.25472783    9.04528882  109.16521453   72.42708879
     -145.89864195  179.40185832  -23.20958503  149.38268049  -46.01854353
     -111.41333374  168.19992516   55.94629733  133.14641247  -18.26999934
       34.49961448   76.75358707  -93.39667853  -57.92384812  135.10589955
       30.38988079   24.33243687  -91.41057204 -106.31856451  172.30038124
      -98.52715716  -78.8356769  -147.81787323  -27.32007329   39.78261791
      151.45075064  -72.58458584   19.63207785  -32.68098076 -154.66566642
      -98.31599479   11.68187277  151.16346516   13.41825427 -159.27497081
     -166.53009652  144.17326207   11.33749061   23.66185214   57.88470817
        0.18502349  -59.28365449   26.92492539  -60.23885931   15.86018213
      154.9028002   109.60019601 -164.66490728   -8.018909     -1.36646168
     -173.07007571 -100.87907946   18.22954328  165.95353152  148.010818
       52.9811787    68.44839303   55.16819338  -26.52214906 -137.91069303
       64.04915002  110.55382402  -23.23204116 -103.24591248 -117.41157544
      141.2672181  -162.17187458  100.9530589   -59.99885622   27.90784784
     -126.58841584  -29.6837911    41.19641585  -99.47223024   83.77569265
      -99.46295224   10.60112213 -128.84416882 -163.43626631 -171.45375497
     -108.76663818 -129.03399225 -115.07569207 -121.93842269 -126.92654914
       18.68289163  137.30645606  -22.78194204 -135.74627324  -58.56781909
       70.42100739  163.63069441   68.01414289   33.58116778   39.58702603]
    


```python
#create dataframe for cities located by random lat/lon data
cities_df = pd.DataFrame({"Latitude": lat, "Longitude": lon})
cities_df.head()
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
      <th>Latitude</th>
      <th>Longitude</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>-79.919873</td>
      <td>-147.289681</td>
    </tr>
    <tr>
      <th>1</th>
      <td>51.357554</td>
      <td>173.254728</td>
    </tr>
    <tr>
      <th>2</th>
      <td>25.596450</td>
      <td>9.045289</td>
    </tr>
    <tr>
      <th>3</th>
      <td>-34.282868</td>
      <td>109.165215</td>
    </tr>
    <tr>
      <th>4</th>
      <td>21.825655</td>
      <td>72.427089</td>
    </tr>
  </tbody>
</table>
</div>




```python
#add blank columns for random city names, as well as weather data and URL for each city
cities_df["City"] = ""
cities_df["Temperature"] = ""
cities_df["Cloudiness"] = ""
cities_df["Wind Speed"] = ""
cities_df["Humidity"] = ""
cities_df["URL"] = ""

cities_df.head()
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
      <th>Latitude</th>
      <th>Longitude</th>
      <th>City</th>
      <th>Temperature</th>
      <th>Cloudiness</th>
      <th>Wind Speed</th>
      <th>Humidity</th>
      <th>URL</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>-79.919873</td>
      <td>-147.289681</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>1</th>
      <td>51.357554</td>
      <td>173.254728</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>2</th>
      <td>25.596450</td>
      <td>9.045289</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>3</th>
      <td>-34.282868</td>
      <td>109.165215</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>4</th>
      <td>21.825655</td>
      <td>72.427089</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
  </tbody>
</table>
</div>




```python
#determine city names using the random lat/lon values

for index, row in cities_df.iterrows():
    latitude = row['Latitude']
    longitude = row ["Longitude"]
    
    cities_df.set_value(index, "City", citipy.nearest_city(latitude, longitude).city_name)
    cities_df.set_value(index, "Country Code", citipy.nearest_city(latitude, longitude).country_code)

cities_df.head()
```

    C:\Users\acave\Anaconda3\lib\site-packages\ipykernel_launcher.py:7: FutureWarning: set_value is deprecated and will be removed in a future release. Please use .at[] or .iat[] accessors instead
      import sys
    C:\Users\acave\Anaconda3\lib\site-packages\ipykernel_launcher.py:8: FutureWarning: set_value is deprecated and will be removed in a future release. Please use .at[] or .iat[] accessors instead
      
    




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
      <th>Latitude</th>
      <th>Longitude</th>
      <th>City</th>
      <th>Temperature</th>
      <th>Cloudiness</th>
      <th>Wind Speed</th>
      <th>Humidity</th>
      <th>URL</th>
      <th>Country Code</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>-79.919873</td>
      <td>-147.289681</td>
      <td>mataura</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td>pf</td>
    </tr>
    <tr>
      <th>1</th>
      <td>51.357554</td>
      <td>173.254728</td>
      <td>nikolskoye</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td>ru</td>
    </tr>
    <tr>
      <th>2</th>
      <td>25.596450</td>
      <td>9.045289</td>
      <td>gat</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td>ly</td>
    </tr>
    <tr>
      <th>3</th>
      <td>-34.282868</td>
      <td>109.165215</td>
      <td>busselton</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td>au</td>
    </tr>
    <tr>
      <th>4</th>
      <td>21.825655</td>
      <td>72.427089</td>
      <td>ghogha</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td>in</td>
    </tr>
  </tbody>
</table>
</div>




```python
#set up URL to gather information from openweathermapy
url = "http://api.openweathermap.org/data/2.5/weather?units=Imperial"

#identify desired settings for outputs
settings = {"units": "imperial", "appid": api_key}
```


```python
for index, row in cities_df.iterrows():
    city = row['City']
    country = row ["Country Code"]
    city_country = str(city)+ "," + str(country)
    
    try:
        #current_weather = owm.get_current(city_country, **settings)
        current_weather_url = url + "&appid=" + api_key + "&q=" + city_country
        current_weather = requests.get(current_weather_url).json()
        #print (current_weather_url)
        
        
        cities_df.set_value(index, "Temperature", current_weather['main']['temp'])
        cities_df.set_value(index, "Humidity", current_weather['main']['humidity'])
        cities_df.set_value(index, "Cloudiness", current_weather['clouds']['all'])
        cities_df.set_value(index, "Wind Speed", current_weather['wind']['speed'])
        cities_df.set_value(index, "URL", current_weather_url)

    
    except:
        cities_df.set_value(index, "Temperature", "No Data from API")
        cities_df.set_value(index, "Humidity", "No Data from API")
        cities_df.set_value(index, "Cloudiness", "No Data from API")
        cities_df.set_value(index, "Wind Speed", "No Data from API")
        cities_df.set_value(index, "URL", "No Data from API")
        print("No Data Available")

cities_df = cities_df[["City", "Country Code", "Latitude", "Longitude","Temperature", "Humidity", "Cloudiness", "Wind Speed", "URL"]]

cities_df.head()
```

    C:\Users\acave\Anaconda3\lib\site-packages\ipykernel_launcher.py:21: FutureWarning: set_value is deprecated and will be removed in a future release. Please use .at[] or .iat[] accessors instead
    C:\Users\acave\Anaconda3\lib\site-packages\ipykernel_launcher.py:22: FutureWarning: set_value is deprecated and will be removed in a future release. Please use .at[] or .iat[] accessors instead
    C:\Users\acave\Anaconda3\lib\site-packages\ipykernel_launcher.py:23: FutureWarning: set_value is deprecated and will be removed in a future release. Please use .at[] or .iat[] accessors instead
    C:\Users\acave\Anaconda3\lib\site-packages\ipykernel_launcher.py:24: FutureWarning: set_value is deprecated and will be removed in a future release. Please use .at[] or .iat[] accessors instead
    C:\Users\acave\Anaconda3\lib\site-packages\ipykernel_launcher.py:25: FutureWarning: set_value is deprecated and will be removed in a future release. Please use .at[] or .iat[] accessors instead
    C:\Users\acave\Anaconda3\lib\site-packages\ipykernel_launcher.py:13: FutureWarning: set_value is deprecated and will be removed in a future release. Please use .at[] or .iat[] accessors instead
      del sys.path[0]
    C:\Users\acave\Anaconda3\lib\site-packages\ipykernel_launcher.py:14: FutureWarning: set_value is deprecated and will be removed in a future release. Please use .at[] or .iat[] accessors instead
      
    C:\Users\acave\Anaconda3\lib\site-packages\ipykernel_launcher.py:15: FutureWarning: set_value is deprecated and will be removed in a future release. Please use .at[] or .iat[] accessors instead
      from ipykernel import kernelapp as app
    C:\Users\acave\Anaconda3\lib\site-packages\ipykernel_launcher.py:16: FutureWarning: set_value is deprecated and will be removed in a future release. Please use .at[] or .iat[] accessors instead
      app.launch_new_instance()
    C:\Users\acave\Anaconda3\lib\site-packages\ipykernel_launcher.py:17: FutureWarning: set_value is deprecated and will be removed in a future release. Please use .at[] or .iat[] accessors instead
    

    No Data Available
    No Data Available
    No Data Available
    No Data Available
    No Data Available
    No Data Available
    No Data Available
    No Data Available
    No Data Available
    




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
      <th>City</th>
      <th>Country Code</th>
      <th>Latitude</th>
      <th>Longitude</th>
      <th>Temperature</th>
      <th>Humidity</th>
      <th>Cloudiness</th>
      <th>Wind Speed</th>
      <th>URL</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>mataura</td>
      <td>pf</td>
      <td>-79.919873</td>
      <td>-147.289681</td>
      <td>No Data from API</td>
      <td>No Data from API</td>
      <td>No Data from API</td>
      <td>No Data from API</td>
      <td>No Data from API</td>
    </tr>
    <tr>
      <th>1</th>
      <td>nikolskoye</td>
      <td>ru</td>
      <td>51.357554</td>
      <td>173.254728</td>
      <td>19.4</td>
      <td>67</td>
      <td>0</td>
      <td>2.24</td>
      <td>http://api.openweathermap.org/data/2.5/weather...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>gat</td>
      <td>ly</td>
      <td>25.596450</td>
      <td>9.045289</td>
      <td>No Data from API</td>
      <td>No Data from API</td>
      <td>No Data from API</td>
      <td>No Data from API</td>
      <td>No Data from API</td>
    </tr>
    <tr>
      <th>3</th>
      <td>busselton</td>
      <td>au</td>
      <td>-34.282868</td>
      <td>109.165215</td>
      <td>65.03</td>
      <td>100</td>
      <td>12</td>
      <td>18.81</td>
      <td>http://api.openweathermap.org/data/2.5/weather...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>ghogha</td>
      <td>in</td>
      <td>21.825655</td>
      <td>72.427089</td>
      <td>68.54</td>
      <td>51</td>
      <td>0</td>
      <td>4.94</td>
      <td>http://api.openweathermap.org/data/2.5/weather...</td>
    </tr>
  </tbody>
</table>
</div>




```python
#create clean dataframe eliminating entries with no data values for certain variables
clean_cities = cities_df[cities_df["Temperature"]!="No Data from API"]
clean_cities.head()
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
      <th>City</th>
      <th>Country Code</th>
      <th>Latitude</th>
      <th>Longitude</th>
      <th>Temperature</th>
      <th>Humidity</th>
      <th>Cloudiness</th>
      <th>Wind Speed</th>
      <th>URL</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>nikolskoye</td>
      <td>ru</td>
      <td>51.357554</td>
      <td>173.254728</td>
      <td>19.4</td>
      <td>67</td>
      <td>0</td>
      <td>2.24</td>
      <td>http://api.openweathermap.org/data/2.5/weather...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>busselton</td>
      <td>au</td>
      <td>-34.282868</td>
      <td>109.165215</td>
      <td>65.03</td>
      <td>100</td>
      <td>12</td>
      <td>18.81</td>
      <td>http://api.openweathermap.org/data/2.5/weather...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>ghogha</td>
      <td>in</td>
      <td>21.825655</td>
      <td>72.427089</td>
      <td>68.54</td>
      <td>51</td>
      <td>0</td>
      <td>4.94</td>
      <td>http://api.openweathermap.org/data/2.5/weather...</td>
    </tr>
    <tr>
      <th>8</th>
      <td>hobart</td>
      <td>au</td>
      <td>-84.918710</td>
      <td>149.382680</td>
      <td>57.2</td>
      <td>58</td>
      <td>20</td>
      <td>10.29</td>
      <td>http://api.openweathermap.org/data/2.5/weather...</td>
    </tr>
    <tr>
      <th>9</th>
      <td>ushuaia</td>
      <td>ar</td>
      <td>-61.241475</td>
      <td>-46.018544</td>
      <td>44.6</td>
      <td>65</td>
      <td>40</td>
      <td>26.4</td>
      <td>http://api.openweathermap.org/data/2.5/weather...</td>
    </tr>
  </tbody>
</table>
</div>




```python
#create scatter plot showing relationship between latitude and temperature

plt.scatter(clean_cities["Latitude"], clean_cities["Temperature"], marker = "*" , color = "teal")
plt.xlim(-100,100)

# Incorporate the other graph properties
plt.title("Temperature vs. Latitude in World Cities")
plt.ylabel("Temperature (Farenheit)")
plt.xlabel("Latitude")
plt.grid(True)

plt.show()
```


![png](output_8_0.png)



```python
#create scatter plot showing relationship between latitude and humidity

plt.scatter(clean_cities["Latitude"], clean_cities["Humidity"], marker = "*" , color = "lightcoral")
plt.xlim(-100,100)

# Incorporate the other graph properties
plt.title("Humidity vs. Latitude in World Cities")
plt.ylabel("Humidity (%)")
plt.xlabel("Latitude")
plt.grid(True)

```


![png](output_9_0.png)



```python
#create scatter plot showing relationship between latitude and temperature

plt.scatter(clean_cities["Latitude"], clean_cities["Cloudiness"], marker = "*" , color = "aqua")
plt.xlim(-100,100)

# Incorporate the other graph properties
plt.title("Cloudiness vs. Latitude in World Cities")
plt.ylabel("Cloudiness (%)")
plt.xlabel("Latitude")
plt.grid(True)

```


![png](output_10_0.png)



```python
#create scatter plot showing relationship between latitude and temperature

plt.scatter(clean_cities["Latitude"], clean_cities["Wind Speed"], marker = "*" , color = "violet")
plt.xlim(-100,100)

# Incorporate the other graph properties
plt.title("Wind Speed vs. Latitude in World Cities")
plt.ylabel("Wind Speed (MPH)")
plt.xlabel("Latitude")
plt.grid(True)

```


![png](output_11_0.png)


Observations:

- Latitude appears to have the greatest affect on temperature, as the scatterplot is more closely grouped
- The least cloudy cities appear to be closest to the equator, and there appear to be more cloudy cities in the northern hemisphere
- There are slightly more humid cities in the southern hemisphere
