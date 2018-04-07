# WeatherAnalysis
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import plotly.plotly as ply
import json
import urllib
import requests as req
from citipy import citipy as cp

latLon = [] 

x = np.random.uniform(-90,90,1500)      #Latitudes range 
y = np.random.uniform(-180, 180,1500)   #Longitudes range
latLon = zip(x, y)

cityList = []

for i in latLon:
    city = cp.nearest_city(i[0], i[1]).city_name
    if city not in cityList:
        cityList.append(city)
        
apiKey = 'aa2b51e6196a31e385d4c816344885bd'
#apiKey = '26b6db097f30fe66ba9414d1a1f41b17'
apiUrl = "http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=" +apiKey

count = 0
weatherDf = []

for i, city in enumerate(cityList):
    cityURL = apiUrl + "&q=" + urllib.request.pathname2url(city)   
    count += 1
    #print(cityURL)
    try:
        data = req.get(cityURL).json() 
        lati = data["coord"]["lat"]
        lngi = data["coord"]["lon"]
        maxTemp = data["main"]["temp_max"]
        humidity = data["main"]["humidity"]
        clouds = data["clouds"]["all"]
        wind = data["wind"]["speed"]
        country = data["sys"]["country"]
        date = data["dt"]
        #fill in the weather data frame        
        weatherDf.append({"Latitude":lati,"Longitude":lngi, "Humidity":humidity, "Wind":wind, "Clouds":clouds,
                                  "City_name":city, "Temperature":maxTemp})
    except:
        #print("City not found...")
        pass        
        
weatherDfNew =  pd.DataFrame(weatherDf)
weatherDfNew.to_csv("weatherData.csv", sep=',', encoding='utf-8')
weatherDfNew.head()

## Scatter plot (latitude,Wind)
plt.scatter(weatherDfNew['Latitude'], weatherDfNew['Wind'])
plt.title(f"Wind Speed(mph) vs. Latitude")
plt.xlabel("Latitude")
plt.ylabel("Wind Speed(mph)")
plt.style.use('ggplot')
#plt.style.use('presentation')
#plt.style.use(['dark_background', 'presentation'])
plt.savefig("Wind_Speed.png")
plt.show()

## Scatter plot (latitude,Temperature)
plt.scatter(weatherDfNew['Latitude'],weatherDfNew['Temperature'])
plt.title(f"Temperature (F) vs. Latitude {date}")
plt.xlabel("Latitude")
plt.ylabel("Temperature (F)")
plt.style.use('ggplot')
plt.savefig("Temperature.png")
plt.show()

## Scatter plot (latitude,humidity)
plt.scatter(weatherDfNew['Latitude'], weatherDfNew['Humidity'])
plt.title(f"Humidity (%) vs. Latitude {date}")
plt.xlabel("Latitude")
plt.ylabel("Humidity (%)")
plt.style.use('ggplot')
plt.savefig("Humidity.png")
plt.show()

## Scatter plot (latitude,cloudy)
plt.scatter(weatherDfNew['Latitude'], weatherDfNew['Clouds'])
plt.title(f"Cloudiness (%) vs. Latitude")
plt.xlabel("Latitude")
plt.ylabel("Cloudiness (%)")
plt.style.use('ggplot')
plt.savefig("Cloudiness.png")
plt.show()
        
    
