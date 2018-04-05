# WeatherAnalysis
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import plotly.plotly as ply
import json
import requests as req
import random
from citipy import citipy as cp


apiKey = 'aa2b51e6196a31e385d4c816344885bd'
apiUrl = "http://api.openweathermap.org/data/2.5/forecast?id=524901&APPID="+apiKey
#apiUrl = "http://api.openweathermap.org/data/2.5/weather"

cols = ['Latitude', 'Temperature', 'Hummidity', 'Wind', 'Clouds','City_name','Longitude']
weatherDf = pd.DataFrame(columns = cols)
cities = []
count = 0

#for x in range(800) :
for x in range(800) :
    x = random.randint(-90,90)      #Latitudes range 
    y = random.randint(-180, 180)   #Longitudes range
    apiUrl = "http://api.openweathermap.org/data/2.5/weather?lat="+str(x)+"&lon="+str(y)+"&APPID="+apiKey
    response = req.get(apiUrl)
    data = response.json()
    city = cp.nearest_city(x, y)
    
    city = data['name']
    temp = data['main']
    wind = data['wind']
    clouds = data['clouds']

    weatherDf = weatherDf.append({cols[0]:x,cols[1]:temp['temp'], cols[2]:temp['humidity'], cols[3]:wind['speed'], cols[4]:clouds['all'], cols[5]:city, cols[6]:y}, ignore_index=True)
    
#removing duplicate cities
weatherDfNew = weatherDf.drop_duplicates(subset=['Latitude' ,'Longitude','City_name'], keep = False)
weatherDfNew = weatherDfNew[weatherDfNew.City_name.notnull()]

#scatter plot (Latitude, Wind)
plt.scatter(weatherDfNew['Latitude'], weatherDfNew['Wind'])
plt.title(f"Wind Speed(mph) vs. Latitude")
plt.xlabel("Latitude")
plt.ylabel("Wind Speed(mph)")
#plt.style.use('ggplot')
plt.style.use('presentation')
#plt.style.use(['dark_background', 'presentation'])
plt.savefig("Wind_Speed.png")
plt.show()

#scatter plot (Latitude, Humidity)
plt.scatter(weatherDfNew['Latitude'],weatherDfNew['Temperature'])
plt.title(f"Temperature (F) vs. Latitude {date}")
plt.xlabel("Latitude")
plt.ylabel("Temperature (F)")
plt.style.use('ggplot')
plt.savefig("Temperature.png")
plt.show()

#scatter plot (latitude,humidity)
plt.scatter(weatherDfNew['Latitude'], weatherDfNew['Hummidity'])
plt.title(f"Humidity (%) vs. Latitude {date}")
plt.xlabel("Latitude")
plt.ylabel("Humidity (%)")
plt.style.use('ggplot')
plt.savefig("Humidity.png")
plt.show()

#scatter plot (latitude,cloudy)
plt.scatter(weatherDfNew['Latitude'], weatherDfNew['Clouds'])
plt.title(f"Cloudiness (%) vs. Latitude")
plt.xlabel("Latitude")
plt.ylabel("Cloudiness (%)")
plt.style.use('ggplot')
plt.savefig("Cloudiness.png")
plt.show()


