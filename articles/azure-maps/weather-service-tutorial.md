---
title: Руководство по объединению данных от датчиков с прогнозом погоды с помощью Записных книжек Azure (Python) | Документация Майкрософт
description: Руководство по объединению данных от датчиков с прогнозом погоды из Azure Maps с помощью Записных книжек Azure (Python).
author: walsehgal
ms.author: v-musehg
ms.date: 12/09/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 8f641640ff6cf4174e2e1374404d47fc0760f79f
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74979580"
---
# <a name="tutorial-join-sensor-data-with-weather-forecast-data-by-using-azure-notebooks-python"></a>Руководство по Объединение данных от датчиков с прогнозом погоды с помощью Записных книжек Azure (Python)

Сила ветра является одним из источников энергии, способных заменить ископаемое топливо и остановить изменение климата. В силу низкой стабильности ветров операторам ветряных электростанций потребуются модели машинного обучения, позволяющие прогнозировать мощность ветра, сопоставлять ее со спросом на электричество и обеспечивать стабильность электросети. Из этого руководства мы узнаем, как можно объединить данные прогноза погоды из Azure Maps с демонстрационным набором данных, полученных от датчиков окружающей среды. Данные о прогнозе погоды извлекаются с помощью запроса к службе погоды Azure Maps.

Изучив данный учебник, вы научитесь:

> [!div class="checklist"]
> * Работать с файлами данных в [Записных книжках Azure](https://docs.microsoft.com/azure/notebooks) в облаке.
> * Загружать примеры данных из файла.
> * Вызывать REST API Azure Maps с помощью Python.
> * Отображать на карте данные о местоположении.
> * Обогащать пример данных, используя [дневные прогнозы](https://aka.ms/AzureMapsWeatherDailyForecast) из Azure Maps.
> * Наносить данные прогнозов погоды на графики.


## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством вам потребуется следующее:

1. Создайте подписку Azure Maps с ценовой категорией S0, следуя инструкциям [по управлению учетной записью](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys#create-a-new-account).
2. Получите первичный ключ подписки для учетной записи, выполнив инструкции из [этой статьи](./tutorial-search-location.md#getkey).

Ознакомьтесь с работой записных книжек Azure и узнайте, как их запускать, по инструкциям из [этой статьи](https://docs.microsoft.com/azure/azure-maps/tutorial-ev-routing#create-an-azure-notebook).

> [!Note]
> Файл записной книжки Jupyter для этого проекта можно скачать из [репозитория записной книжки Jupyter для Azure Maps](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/tree/master/AzureMapsJupyterSamples/Tutorials/Analyze%20Weather%20Data).

## <a name="load-the-required-modules-and-frameworks"></a>Загрузка необходимых модулей и платформ

Чтобы загрузить все необходимые модули и платформы, выполните приведенный ниже скрипт:

```python
import aiohttp
import pandas as pd
import datetime
from IPython.display import Image, display
```

## <a name="import-weather-data"></a>Импорт данных о погоде

Для целей этого руководства мы будем использовать показания датчиков погоды, установленных на четырех разных ветрогенераторах. Этот пример данных включает сведения о погоде за 30 дней, собранные от метеорологических станций, расположенных рядом с этими ветрогенераторами. Пример данных включает сведения о температуре, скорости и направлении ветра. Скачать эти демонстрационные данные можно [здесь](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/tree/master/AzureMapsJupyterSamples/Tutorials/Analyze%20Weather%20Data/data). Следующий скрипт импортирует демонстрационные данные в записную книжку Azure.

```python
df = pd.read_csv("./data/weather_dataset_demo.csv")
```

## <a name="request-daily-forecast-data"></a>Запрос данных прогноза по дням

В нашем сценарии мы будем получать прогноз по дням для местоположений каждого датчика. Следующий скрипт вызывает [API прогнозов по дням](https://aka.ms/AzureMapsWeatherDailyForecast) службы погоды Azure Maps, чтобы получить прогноз погоды для каждого ветрогенератора по дням на 15 дней от текущей даты.


```python
subscription_key = "Your Azure Maps primary subscription key"

# Get a lists of unique station IDs and their coordinates 
station_ids = pd.unique(df[['StationID']].values.ravel())
coords = pd.unique(df[['latitude','longitude']].values.ravel())

years,months,days = [],[],[]
dates_check=set()
wind_speeds, wind_direction = [], []

# Call azure maps weather service to get daily forecast data for 15 days from current date
session = aiohttp.ClientSession()
j=-1
for i in range(0, len(coords), 2):
    wind_speeds.append([])
    wind_direction.append([])
    
    query = str(coords[i])+', '+str(coords[i+1])
    forecast_response = await(await session.get("https://atlas.microsoft.com/weather/forecast/daily/json?query={}&api-version=1.0&subscription-key={}&duration=15".format(query, subscription_key))).json()
    j+=1
    for day in range(len(forecast_response['forecasts'])):
            date = forecast_response['forecasts'][day]['date'][:10]
            wind_speeds[j].append(forecast_response['forecasts'][day]['day']['wind']['speed']['value'])
            wind_direction[j].append(forecast_response['forecasts'][day]['day']['windGust']['direction']['degrees'])
            
            if date not in dates_check:
                year,month,day= date.split('-')
                years.append(year)
                months.append(month)
                days.append(day)
                dates_check.add(date)
            
await session.close()
```

Следующий скрипт наносит на карту расположения ветрогенераторов, обращаясь к [службе получения изображения карты](https://docs.microsoft.com/rest/api/maps/render/getmapimage) в Azure Maps.

```python
# Render the turbine locations on the map by calling the Azure Maps Get Map Image service
session = aiohttp.ClientSession()

pins="default|la-25+60|ls12|lc003C62|co9B2F15||'Location A'{} {}|'Location B'{} {}|'Location C'{} {}|'Location D'{} {}".format(coords[1],coords[0],coords[3],coords[2],coords[5],coords[4], coords[7],coords[6])

image_response = "https://atlas.microsoft.com/map/static/png?subscription-key={}&api-version=1.0&layer=basic&style=main&zoom=6&center={},{}&pins={}".format(subscription_key,coords[7],coords[6],pins)

static_map_response = await session.get(image_response)

poi_range_map = await static_map_response.content.read()

await session.close()

display(Image(poi_range_map))
```

![Расположения ветрогенераторов](./media/weather-service-tutorial/location-map.png)


Чтобы дополнить наш пример данных сведениями о прогнозе погоды, мы сгруппируем эти наборы данных по идентификаторам метеостанций.

```python
# Group forecasted data for all locations
df = df.reset_index(drop=True)
forecast_data = pd.DataFrame(columns=['StationID','latitude','longitude','Year','Month','Day','DryBulbCelsius','WetBulbFarenheit','WetBulbCelsius','DewPointFarenheit','DewPointCelsius','RelativeHumidity','WindSpeed','WindDirection'])

for i in range(len(station_ids)):
    loc_forecast = pd.DataFrame({'StationID':station_ids[i], 'latitude':coords[0], 'longitude':coords[1], 'Year':years, 'Month':months, 'Day':days, 'WindSpeed':wind_speeds[i], 'WindDirection':wind_direction[i]})
    forecast_data = pd.concat([forecast_data,loc_forecast], axis=0, sort=False)
    
combined_weather_data = pd.concat([df,forecast_data])
grouped_weather_data = combined_weather_data.groupby(['StationID'])
```

В следующей таблице показан объединенный набор исторических и прогнозируемых данных по расположению одного из ветрогенераторов.

```python
# Display data for first location
grouped_weather_data.get_group(station_ids[0]).reset_index()
```

<center>

![Сгруппированные данные](./media/weather-service-tutorial/grouped-data.png)</center>

## <a name="plot-forecast-data"></a>График прогнозируемых данных

Чтобы отобразить данные о скорости и направлении ветра на ближайшие 15 дней, мы нанесем прогнозируемые знначения на график, построенный по датам соответствующих прогнозов.

```python
# Plot wind speed
curr_date = datetime.datetime.now().date()
windsPlot_df = pd.DataFrame({ 'Location A': wind_speeds[0], 'Location B': wind_speeds[1], 'Location C': wind_speeds[2], 'Location D': wind_speeds[3]}, index=pd.date_range(curr_date,periods=15))
windsPlot = windsPlot_df.plot.line()
windsPlot.set_xlabel("Date")
windsPlot.set_ylabel("Wind speed")
```

```python
#Plot wind direction 
windsPlot_df = pd.DataFrame({ 'Location A': wind_direction[0], 'Location B': wind_direction[1], 'Location C': wind_direction[2], 'Location D': wind_direction[3]}, index=pd.date_range(curr_date,periods=15))
windsPlot = windsPlot_df.plot.line()
windsPlot.set_xlabel("Date")
windsPlot.set_ylabel("Wind direction")
```

На представленных ниже графиках визуализированы данные о прогнозах скорости (левый график) и направления ветра (правый график) на ближайшие 15 дней от текущей даты.

<center>

![График скорости ветра](./media/weather-service-tutorial/speed-date-plot.png) ![График направления ветра](./media/weather-service-tutorial/direction-date-plot.png)</center>


## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как вызывать REST API для Azure Maps для получения данных прогнозов и визуализации этих данных на графиках.

Дополнительные сведения о вызове REST API для Azure Maps из Записных книжек Azure вы можете получить в [этой статье](https://docs.microsoft.com/azure/azure-maps/tutorial-ev-routing).

Для изучения API-интерфейсов Azure Maps, используемых в этом руководстве, прочтите следующие статьи:

* [Прогноз по дням](https://aka.ms/AzureMapsWeatherDailyForecast)
* [Render — Get Map Image](https://docs.microsoft.com/rest/api/maps/render/getmapimage) (Отрисовка — получение изображения карты)

Полный список API-интерфейсов Azure Maps вы найдете на [этой странице](https://docs.microsoft.com/azure/azure-maps/#reference).

Дополнительные сведения о службе "Записные книжки Azure" см. в [этой статье](https://docs.microsoft.com/azure/notebooks).
