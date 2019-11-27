---
title: Руководство по Маршрутизация электрических транспортных средств с помощью Записных книжек Azure (Python)
description: Руководство по Маршрутизация электрических транспортных средств с помощью API маршрутизации Azure Maps и Записных книжек Azure.
author: walsehgal
ms.author: v-musehg
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 0f5964472b738bd3868bbf361b85fa4ad1f13b5c
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/15/2019
ms.locfileid: "74109603"
---
# <a name="tutorial-route-electric-vehicles-by-using-azure-notebooks-python"></a>Руководство по Маршрутизация электрических транспортных средств с помощью Записных книжек Azure (Python)

Azure Maps — это набор API-интерфейсов геопространственных служб, изначально интегрированных в Azure. С помощью этих API разработчики, предприятия и независимые поставщики ПО могут создавать приложения с учетом расположения, а также решения для Интернета вещей, мобильных служб, логистики и отслеживания ресурсов. 

Интерфейсы REST API Azure Maps можно вызывать с помощью таких языков, как Python и R, для включения анализа геопространственных данных и сценариев машинного обучения. Azure Maps предоставляет надежный набор [API маршрутизации](https://docs.microsoft.com/rest/api/maps/route), с помощью которого пользователи могут рассчитывать маршруты между несколькими точками данных. Расчеты базируются на различных данных, таких как тип транспортного средства или доступная область. 

В рамках этого руководства рассматривается сценарий, с помощью которого водитель транспортного средства с низким уровнем заряда батареи сможет найти ближайшую зарядную станцию с учетом времени езды от расположения средства.

Изучив данный учебник, вы научитесь:

> [!div class="checklist"]
> * Создавать и запускать Jupyter Notebook в службе [Записные книжки Azure](https://docs.microsoft.com/azure/notebooks) в облаке.
> * Вызывать REST API Azure Maps с помощью Python.
> * Находить доступный диапазон с учетом модели использования электрического транспортного средства.
> * Находить зарядные станции для электрических транспортных средств в пределах доступного диапазона или изохроны.
> * Отрисовывать границы доступного диапазона и зарядные станции на карте.
> * Находить и визуализировать маршрут к ближайшей зарядной станции для электрических транспортных средств с учетом времени поездки.


## <a name="prerequisites"></a>Предварительные требования 

Чтобы выполнить действия, описанные в этом руководстве, сначала создайте учетную запись Azure Maps и получите первичный ключ (ключ подписки). 

Следуйте инструкциям [по управлению учетной записью](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys#create-a-new-account), чтобы создать подписку Azure Maps с ценовой категорией S1. 

Чтобы получить первичный ключ для учетной записи, выполните инструкции из раздела [Создание учетной записи и получение ключа](https://docs.microsoft.com/azure/azure-maps/quick-demo-map-app#create-an-account-and-get-your-key).

## <a name="create-an-azure-notebook"></a>Создание записной книжки Azure

Для работы с этим руководством вам необходимо создать проект службы "Записные книжки Azure", а также скачать и запустить файл записной книжки Jupyter. Файл записной книжки содержит код Python, который реализует сценарий, описанный в этом руководстве. Чтобы создать проект службы "Записные книжки Azure" и отправить в него документ записной книжки Jupyter, выполните следующие действия:

1. Перейдите в службу [Записные книжки Azure](https://notebooks.azure.com) и выполните вход. Дополнительные сведения см. в [кратком руководстве о входе и настройке идентификатора пользователя](https://docs.microsoft.com/azure/notebooks/quickstart-sign-in-azure-notebooks).
1. Вверху на общедоступной странице профиля щелкните **Мои проекты**.

    ![Кнопка "Мои проекты"](./media/tutorial-ev-routing/myproject.png)

1. На странице **Мои проекты** щелкните **Создать проект**.
 
   ![Кнопка "Создать проект"](./media/tutorial-ev-routing/create-project.png)

1. В области **Создание проекта** введите имя и идентификатор проекта.
 
    ![Область "Создание проекта"](./media/tutorial-ev-routing/create-project-window.png)

1. Нажмите кнопку **Создать**.

1. После создания проекта скачайте [файл документа записной книжки Jupyter](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/blob/master/AzureMapsJupyterSamples/Tutorials/EV%20Routing%20and%20Reachable%20Range/EVrouting.ipynb) из [репозитория Azure Maps Jupyter Notebook](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook). 

1. В списке проектов на странице **Мои проекты** выберите свой проект и щелкните **Отправить**, чтобы отправить файл документа записной книжки Jupyter. 

    ![отправка записной книжки](./media/tutorial-ev-routing/upload-notebook.png)

1. Отправьте файл с компьютера и нажмите кнопку **Готово**.

1. После успешной отправки на странице проекта появится ваш файл. Выберите файл, чтобы открыть его в виде записной книжки Jupyter.

Чтобы лучше понять функциональность файла записной книжки, рекомендуем выполнять код в одной ячейке записной книжки за раз. Вы можете выполнять код в каждой ячейке, нажимая кнопку **Запустить** в верхней части приложения записной книжки.

  ![Кнопка "Запустить"](./media/tutorial-ev-routing/run.png)

## <a name="install-project-level-packages"></a>Установка пакетов на уровне проекта

Чтобы выполнить код в записной книжке, необходимо установить пакеты на уровне проекта, сделав следующее:

1. Скачайте файл [*requirements.txt*](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/blob/master/AzureMapsJupyterSamples/Tutorials/EV%20Routing%20and%20Reachable%20Range/requirements.txt) из репозитория [Azure Maps Jupyter Notebook](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook), а затем отправьте его в проект.
1. На панели мониторинга проекта выберите **Project Settings** (Параметры проекта). 
1. В области **Project Settings** (Параметры проекта) перейдите на вкладку **Среда** и выберите **Добавить**.
1. В разделе **Environment Setup Steps** (Шаги настройки среды) выполните следующие действия:   
    a. В первом раскрывающемся списке выберите **Requirements.txt**.  
    b. Во втором раскрывающемся списке выберите файл *requirements.txt*.  
    c. В третьем раскрывающемся списке выберите версию **Python Version 3.6**.
1. Щелкните **Сохранить**.

    ![Установка пакетов](./media/tutorial-ev-routing/install-packages.png)

## <a name="load-the-required-modules-and-frameworks"></a>Загрузка необходимых модулей и платформ

Чтобы загрузить все необходимые модули и платформы, выполните приведенный ниже скрипт:

```python
import time
import aiohttp
import urllib.parse
from IPython.display import Image, display
```

## <a name="request-the-reachable-range-boundary"></a>Запрос границы доступного диапазона

В нашем сценарии компания доставки располагает несколькими электрическими транспортными средствами. В течение дня эти средства необходимо подзаряжать в дороге за пределами склада. Каждый раз при низком уровне заряда (заряд, которого хватает лишь на час езды) вам необходимо будет искать набор зарядных станций, расположенных в пределах доступного диапазона, и получать сведения о границах этого диапазона. 

Так как компания предпочтительно использует маршруты, сбалансированные с учетом экономичности и скорости, запрашиваемым типом маршрута (routeType) является *eco* (экономичный). Приведенный ниже скрипт вызывает [API получения диапазона маршрута](https://docs.microsoft.com/rest/api/maps/route/getrouterange) службы маршрутизации Azure Maps с помощью параметров для модели использования электрического транспортного средства. Затем он анализирует ответ для создания объекта многоугольника в формате geojson, представляющего максимально доступный диапазон для автомобиля.

Чтобы определить границы для доступного диапазона электрического транспортного средства, выполните скрипт в следующей ячейке:

```python
subscriptionKey = "Your Azure Maps primary subscription key"
currentLocation = [34.028115,-118.5184279]
session = aiohttp.ClientSession()

# Parameters for the vehicle consumption model 
travelMode = "car"
vehicleEngineType = "electric"
currentChargeInkWh=45
maxChargeInkWh=80
timeBudgetInSec=550
routeType="eco"
constantSpeedConsumptionInkWhPerHundredkm="50,8.2:130,21.3"


# Get boundaries for the electric vehicle's reachable range.
routeRangeResponse = await (await session.get("https://atlas.microsoft.com/route/range/json?subscription-key={}&api-version=1.0&query={}&travelMode={}&vehicleEngineType={}&currentChargeInkWh={}&maxChargeInkWh={}&timeBudgetInSec={}&routeType={}&constantSpeedConsumptionInkWhPerHundredkm={}"
                                              .format(subscriptionKey,str(currentLocation[0])+","+str(currentLocation[1]),travelMode, vehicleEngineType, currentChargeInkWh, maxChargeInkWh, timeBudgetInSec, routeType, constantSpeedConsumptionInkWhPerHundredkm))).json()

polyBounds = routeRangeResponse["reachableRange"]["boundary"]

for i in range(len(polyBounds)):
    coordList = list(polyBounds[i].values())
    coordList[0], coordList[1] = coordList[1], coordList[0]
    polyBounds[i] = coordList

polyBounds.pop()
polyBounds.append(polyBounds[0])

boundsData = {
               "geometry": {
                 "type": "Polygon",
                 "coordinates": 
                   [
                      polyBounds
                   ]
                }
             }
```

## <a name="search-for-electric-vehicle-charging-stations-within-the-reachable-range"></a>Поиск зарядных станций для электрических транспортных средств в пределах доступного диапазона

После определения доступного диапазона (изохроны) для электрического транспортного средства можно выполнить поиск зарядных станций в этом диапазоне. 

Приведенный ниже скрипт вызывает [API запроса Post на поиск внутри геометрии](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry) Azure Maps. Он ищет зарядные станции для электрического транспортного средства в пределах максимально доступного диапазона для автомобиля, а затем анализирует ответ по массиву доступных расположений.

Чтобы найти зарядные станции для электрических транспортных средств в пределах доступного диапазона, выполните приведенный ниже скрипт:

```python
# Search for electric vehicle stations within reachable range.
searchPolyResponse = await (await session.post(url = "https://atlas.microsoft.com/search/geometry/json?subscription-key={}&api-version=1.0&query=electric vehicle station&idxSet=POI&limit=50".format(subscriptionKey), json = boundsData)).json() 

reachableLocations = []
for loc in range(len(searchPolyResponse["results"])):
                location = list(searchPolyResponse["results"][loc]["position"].values())
                location[0], location[1] = location[1], location[0]
                reachableLocations.append(location)
```

## <a name="upload-the-reachable-range-and-charging-points-to-azure-maps-data-service"></a>Загрузка доступного диапазона и точек зарядки в службу данных Azure Maps

Вам необходимо визуализировать на карте зарядные станции и границу для максимально доступного диапазона для электрического транспортного средства. Для этого загрузите данные о границах и зарядных станциях в виде объектов geojson в службу данных Azure Maps, используя [API отправки данных](https://docs.microsoft.com/rest/api/maps/data/uploadpreview). 

Чтобы отправить данные о границах и зарядных точках в службу данных Azure Maps, выполните приведенные ниже скрипты в двух ячейках:

```python
rangeData = {
  "type": "FeatureCollection",
  "features": [
    {
      "type": "Feature",
      "properties": {},
      "geometry": {
        "type": "Polygon",
        "coordinates": [
          polyBounds
        ]
      }
    }
  ]
}

# Upload the range data to Azure Maps Data Service.
uploadRangeResponse = await session.post("https://atlas.microsoft.com/mapData/upload?subscription-key={}&api-version=1.0&dataFormat=geojson".format(subscriptionKey), json = rangeData)

rangeUdidRequest = uploadRangeResponse.headers["Location"]+"&subscription-key={}".format(subscriptionKey)

while True:
    getRangeUdid = await (await session.get(rangeUdidRequest)).json()
    if 'udid' in getRangeUdid:
        break
    else:
        time.sleep(0.2)
rangeUdid = getRangeUdid["udid"]
```

```python
poiData = {
    "type": "FeatureCollection",
    "features": [
      {
        "type": "Feature",
        "properties": {},
        "geometry": {
            "type": "MultiPoint",
            "coordinates": reachableLocations
        }
    }
  ]
}

# Upload the electric vehicle charging station data to Azure Maps Data Service.
uploadPOIsResponse = await session.post("https://atlas.microsoft.com/mapData/upload?subscription-key={}&api-version=1.0&dataFormat=geojson".format(subscriptionKey), json = poiData)

poiUdidRequest = uploadPOIsResponse.headers["Location"]+"&subscription-key={}".format(subscriptionKey)

while True:
    getPoiUdid = await (await session.get(poiUdidRequest)).json()
    if 'udid' in getPoiUdid:
        break
    else:
        time.sleep(0.2)
poiUdid = getPoiUdid["udid"]
```

## <a name="render-the-charging-stations-and-reachable-range-on-a-map"></a>Отрисовка зарядных станций и доступного диапазона на карте

После загрузки данных в службу данных вызовите [службу получения изображения карты](https://docs.microsoft.com/rest/api/maps/render/getmapimage) Azure Maps для отрисовки точек зарядки и максимально доступной границы на статическом изображении карты, запустив приведенный ниже скрипт:

```python
# Get boundaries for the bounding box.
def getBounds(polyBounds):
    maxLon = max(map(lambda x: x[0], polyBounds))
    minLon = min(map(lambda x: x[0], polyBounds))

    maxLat = max(map(lambda x: x[1], polyBounds))
    minLat = min(map(lambda x: x[1], polyBounds))
    
    # Buffer the bounding box by 10 percent to account for the pixel size of pins at the ends of the route.
    lonBuffer = (maxLon-minLon)*0.1
    minLon -= lonBuffer
    maxLon += lonBuffer

    latBuffer = (maxLat-minLat)*0.1
    minLat -= latBuffer
    maxLat += latBuffer
    
    return [minLon, maxLon, minLat, maxLat]

minLon, maxLon, minLat, maxLat = getBounds(polyBounds)

path = "lcff3333|lw3|la0.80|fa0.35||udid-{}".format(rangeUdid)
pins = "custom|an15 53||udid-{}||https://raw.githubusercontent.com/Azure-Samples/AzureMapsCodeSamples/master/AzureMapsCodeSamples/Common/images/icons/ev_pin.png".format(poiUdid)

encodedPins = urllib.parse.quote(pins, safe='')

# Render the range and electric vehicle charging points on the map.
staticMapResponse =  await session.get("https://atlas.microsoft.com/map/static/png?api-version=1.0&subscription-key={}&pins={}&path={}&bbox={}&zoom=12".format(subscriptionKey,encodedPins,path,str(minLon)+", "+str(minLat)+", "+str(maxLon)+", "+str(maxLat)))

poiRangeMap = await staticMapResponse.content.read()

display(Image(poiRangeMap))
```

![Карта с диапазоном расположения](./media/tutorial-ev-routing/location-range.png)


## <a name="find-the-optimal-charging-station"></a>Поиск оптимальной зарядной станции

После определения всех потенциальных зарядных станций в пределах доступного диапазона необходимо определить, до какой станций можно добраться за минимальное время. 

Приведенный ниже скрипт вызывает [API матричной маршрутизации Azure Maps](https://docs.microsoft.com/rest/api/maps/route/postroutematrix), который возвращает значение времени в пути с учетом указанного расположения транспортного средства и расстояние до каждой зарядной станции. Скрипт, указанный в следующей ячейке, анализирует ответ для определения расположения ближайшей доступной зарядной станции с учетом времени.

Чтобы найти ближайшую доступную зарядную станцию, до которой можно добраться за минимальное время, запустите следующий скрипт:

```python
locationData = {
            "origins": {
              "type": "MultiPoint",
              "coordinates": [[currentLocation[1],currentLocation[0]]]
            },
            "destinations": {
              "type": "MultiPoint",
              "coordinates": reachableLocations
            }
         }

# Get the travel time and distance to each specified charging station.
searchPolyRes = await (await session.post(url = "https://atlas.microsoft.com/route/matrix/json?subscription-key={}&api-version=1.0&routeType=shortest&waitForResults=true".format(subscriptionKey), json = locationData)).json()

distances = []
for dist in range(len(reachableLocations)):
    distances.append(searchPolyRes["matrix"][0][dist]["response"]["routeSummary"]["travelTimeInSeconds"])

minDistLoc = []
minDistIndex = distances.index(min(distances))
minDistLoc.extend([reachableLocations[minDistIndex][1], reachableLocations[minDistIndex][0]])
closestChargeLoc = ",".join(str(i) for i in minDistLoc)
```

## <a name="calculate-the-route-to-the-closest-charging-station"></a>Расчет маршрута до ближайшей зарядной станции

Теперь, когда вы нашли ближайшую зарядную станцию, можно вызвать [API получения направлений маршрута](https://docs.microsoft.com/rest/api/maps/route/getroutedirections), чтобы запросить подробные сведения о маршруте от текущего расположения электрического транспортного средства до зарядной станции.

Чтобы получить маршрут к зарядной станции и проанализировать ответ для создания объекта geojson, представляющего маршрут, запустите скрипт, указанный в следующей ячейке:

```python
# Get the route from the electric vehicle's current location to the closest charging station. 
routeResponse = await (await session.get("https://atlas.microsoft.com/route/directions/json?subscription-key={}&api-version=1.0&query={}:{}".format(subscriptionKey, str(currentLocation[0])+","+str(currentLocation[1]), closestChargeLoc))).json()

route = []
for loc in range(len(routeResponse["routes"][0]["legs"][0]["points"])):
                location = list(routeResponse["routes"][0]["legs"][0]["points"][loc].values())
                location[0], location[1] = location[1], location[0]
                route.append(location)

routeData = {
         "type": "LineString",
         "coordinates": route
     }
```

## <a name="visualize-the-route"></a>Визуализация маршрута

Чтобы выполнить визуализацию маршрута, сначала отправьте данные маршрута в виде объекта geojson в службу данных Azure Maps с помощью [API отправки данных](https://docs.microsoft.com/rest/api/maps/data/uploadpreview) Azure Maps. Затем вызовите службу отрисовки, [API получения изображения карты](https://docs.microsoft.com/rest/api/maps/render/getmapimage), чтобы отрисовать маршрут на карте и визуализировать его.

Чтобы получить изображение для отрисованного маршрута на карте, запустите приведенный ниже скрипт:

```python
# Upload the route data to Azure Maps Data Service.
routeUploadRequest = await session.post("https://atlas.microsoft.com/mapData/upload?subscription-key={}&api-version=1.0&dataFormat=geojson".format(subscriptionKey), json = routeData)

udidRequestURI = routeUploadRequest.headers["Location"]+"&subscription-key={}".format(subscriptionKey)

while True:
    udidRequest = await (await session.get(udidRequestURI)).json()
    if 'udid' in udidRequest:
        break
    else:
        time.sleep(0.2)

udid = udidRequest["udid"]

destination = route[-1]

destination[1], destination[0] = destination[0], destination[1]

path = "lc0f6dd9|lw6||udid-{}".format(udid)
pins = "default|codb1818||{} {}|{} {}".format(str(currentLocation[1]),str(currentLocation[0]),destination[1],destination[0])


# Get boundaries for the bounding box.
minLat, maxLat = (float(destination[0]),currentLocation[0]) if float(destination[0])<currentLocation[0] else (currentLocation[0], float(destination[0]))
minLon, maxLon = (float(destination[1]),currentLocation[1]) if float(destination[1])<currentLocation[1] else (currentLocation[1], float(destination[1]))

# Buffer the bounding box by 10 percent to account for the pixel size of pins at the ends of the route.
lonBuffer = (maxLon-minLon)*0.1
minLon -= lonBuffer
maxLon += lonBuffer

latBuffer = (maxLat-minLat)*0.1
minLat -= latBuffer
maxLat += latBuffer

# Render the route on the map.
staticMapResponse = await session.get("https://atlas.microsoft.com/map/static/png?api-version=1.0&subscription-key={}&&path={}&pins={}&bbox={}&zoom=16".format(subscriptionKey,path,pins,str(minLon)+", "+str(minLat)+", "+str(maxLon)+", "+str(maxLat)))

staticMapImage = await staticMapResponse.content.read()

await session.close()
display(Image(staticMapImage))
```

![Карта с маршрутом](./media/tutorial-ev-routing/route.png)

## <a name="next-steps"></a>Дополнительная информация

В рамках этого руководства вы узнали, как напрямую вызывать REST API Azure Maps и визуализировать данные Azure Maps с помощью Python.

Для изучения API-интерфейсов Azure Maps, используемых в этом руководстве, прочтите следующие статьи:

* [Route — Get Route Range](https://docs.microsoft.com/rest/api/maps/route/getrouterange) (Маршрут — получение диапазона маршрута)
* [Search — Post Search Inside Geometry](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry) (Поиск — запрос Post на поиск внутри геометрии)
* [Data — Upload Preview](https://docs.microsoft.com/rest/api/maps/data/uploadpreview) (Данные — предварительная версия отправки)
* [Render — Get Map Image](https://docs.microsoft.com/rest/api/maps/render/getmapimage) (Отрисовка — получение изображения карты)
* [Route — Post Route Matrix Preview](https://docs.microsoft.com/rest/api/maps/route/postroutematrix) (Маршрут — предварительная версия запроса Post матрицы маршрута)
* [Route — Get Route Directions](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) (Маршрут — получение направлений маршрута)

Полный список API-интерфейсов Azure Maps вы найдете на [этой странице](https://docs.microsoft.com/azure/azure-maps/#reference).

Дополнительные сведения о службе "Записные книжки Azure" см. в [этой статье](https://docs.microsoft.com/azure/notebooks).
