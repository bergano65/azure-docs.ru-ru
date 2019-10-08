---
title: Маршрутизация электрических транспортных средств с помощью Записных книжек Azure (Python) | Документация Майкрософт
description: Маршрутизация электрических транспортных средств с помощью API маршрутизации Azure Maps и Записных книжек Azure.
author: walsehgal
ms.author: v-musehg
ms.date: 10/01/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 30751bebd397b378924453987462c9e2b3b55ebf
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803898"
---
# <a name="electric-vehicle-routing-using-azure-notebooks-python"></a>Маршрутизация электрических транспортных средств с помощью Записных книжек Azure (Python)

Azure Maps — это набор API геопространственной службы, изначально интегрированных в Azure, которые позволят разработчикам, предприятиям и независимым поставщикам ПО создавать приложения с учетом расположения, а также решения для Интернета вещей, мобильных устройств, логистики и отслеживания ресурсов. Интерфейсы REST API Azure Maps можно вызывать с помощью таких языков, как Python и R, для включения анализа геопространственных данных и сценариев машинного обучения. Azure Maps предоставляет надежный набор [API маршрутизации]([https://docs.microsoft.com/rest/api/maps/route), с помощью которого пользователи могут рассчитывать маршруты между несколькими точками данных на основе различных данных, таких как тип транспортного средства или доступная область. В рамках этого руководства мы рассмотрим сценарий, с помощью которого водитель транспортного средства с низким уровнем заряда батареи сможет найти ближайшую зарядную станцию с учетом времени поездки.

Изучив данный учебник, вы научитесь:

> [!div class="checklist"]
> * Создавать и запускать Jupyter Notebook в службе [Записные книжки Azure](https://docs.microsoft.com/azure/notebooks) в облаке.
> * Вызывать REST API Azure Maps с помощью Python.
> * Находить доступный диапазон с учетом модели использования электрического транспортного средства.
> * Находить зарядные станции для электрических транспортных средств в пределах доступного диапазона (или изохроны).
> * Отрисовывать границы доступного диапазона и зарядные станции на карте.
> * Находить и визуализировать маршрут к ближайшей зарядной станции для электрических транспортных средств с учетом времени.


## <a name="prerequisites"></a>Предварительные требования 

Чтобы выполнить действия, описанные в этом руководстве, сначала создайте учетную запись Azure Maps и получите первичный ключ (ключ подписки). Следуйте инструкциям [по управлению учетной записью](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys#create-a-new-account), чтобы создать подписку Azure Maps в ценовой категорией S1, и выполните инструкции из раздела [Получение первичного ключа для учетной записи](./tutorial-search-location.md#getkey).

## <a name="create-an-azure-notebook"></a>Создание записной книжки Azure

Для работы с этим руководством вам необходимо создать проект службы "Записные книжки Azure", а также скачать и запустить файл записной книжки Jupyter. Файл записной книжки содержит код Python, который реализует сценарий, описанный в этом руководстве. Выполните приведенные ниже шаги, чтобы создать проект службы "Записные книжки Azure" и загрузить в него документ записной книжки Jupyter.

1. Перейдите в службу [Записные книжки Azure](https://notebooks.azure.com) и выполните вход. Дополнительные сведения см. в [этом кратком руководстве](https://docs.microsoft.com/azure/notebooks/quickstart-sign-in-azure-notebooks).
2. Вверху на общедоступной странице профиля щелкните **Мои проекты**.

    ![мои проекты](./media/tutorial-ev-routing/myproject.png)

3. На странице **Мои проекты** щелкните **Создать проект**.
 
   ![создание проекта](./media/tutorial-ev-routing/create-project.png)

4. В появившемся всплывающем окне **Создание проекта** введите или укажите приведенные ниже сведения, а затем щелкните **Создать**:
    * Имя проекта
    * Идентификатор проекта
 
    ![создание проекта](./media/tutorial-ev-routing/create-project-window.png)

5. После создания проекта скачайте [файл документа записной книжки Jupyter](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/blob/master/AzureMapsJupyterSamples/Tutorials/EV%20Routing%20and%20Reachable%20Range/EVrouting.ipynb) из [репозитория Azure Maps Jupyter Notebook](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook). 

6. Выберите свой проект в списке проектов на странице **Мои проекты** и щелкните **Отправить**, чтобы отправить файл документа записной книжки Jupyter. Отправьте файл с компьютера и нажмите кнопку **Готово**.

    ![отправка записной книжки](./media/tutorial-ev-routing/upload-notebook.png)

7. После успешной отправки на странице проекта появится ваш файл. Щелкните файл записной книжки, чтобы открыть его в виде Jupyter Notebook.

Чтобы лучше понять функциональность файла записной книжки, рекомендуем выполнять код в одной ячейке записной книжки за раз. Вы можете выполнять код в каждой ячейке, нажимая кнопку **Запустить** в верхней части приложения записной книжки.

  ![Запустить](./media/tutorial-ev-routing/run.png)

## <a name="install-project-level-packages"></a>Установка пакетов на уровне проекта

Чтобы выполнить код в записной книжке, необходимо установить пакеты на уровне проекта. Чтобы установить необходимые пакеты, выполните следующие действия:

1. Скачайте файл [requirements.txt](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/blob/master/AzureMapsJupyterSamples/Tutorials/EV%20Routing%20and%20Reachable%20Range/requirements.txt) из [репозитория Azure Maps Jupyter Notebook](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook) и отправьте его в проект.
2. На панели мониторинга проекта выберите **Project Settings** (Параметры проекта). 
3. В появившемся всплывающем окне выберите вкладку **Среда**, а затем щелкните **+Добавить**.
4. В разделе **Environment Setup Steps** (Шаги настройки среды): 
    * в первом раскрывающемся списке выберите **Requirements.txt**;
    * во втором раскрывающемся списке выберите requirements.txt;
    * в третьем раскрывающемся списке выберите Python Version 3.6 в качестве версии Python.
7. Щелкните **Сохранить**.

    ![установка пакетов](./media/tutorial-ev-routing/install-packages.png)

## <a name="load-required-modules-and-frameworks"></a>Загрузка необходимых модулей и платформ

Выполните приведенный ниже скрипт, чтобы загрузить все необходимые модули и платформы.

```python
import time
import aiohttp
import urllib.parse
from IPython.display import Image, display
```

## <a name="request-for-reachable-range-boundary"></a>Запрос границы доступного диапазона

В нашем сценарии компания доставки располагает несколькими электрическими транспортными средствами. В течение дня эти средства необходимо подзаряжать в дороге за пределами склада. Каждый раз при низком уровне заряда электрического транспортного средства (заряд, которого хватает лишь на час езды) нам необходимо искать набор зарядных станций, расположенных в пределах доступного диапазона, и получать сведения о границах этого диапазона. Так как компания предпочтительно использует маршруты, сбалансированные с учетом экономичности и скорости, запрашиваемым типом маршрута (routeType) является eco (экономичный). Приведенный ниже скрипт вызывает [API получения диапазона маршрута](https://docs.microsoft.com/rest/api/maps/route/getrouterange) службы маршрутизации Azure Maps с параметрами для модели использования электрического транспортного средства и анализирует ответ для создания объекта многоугольника в формате geojson, представляющего максимально доступный диапазон для автомобиля.

Выполните скрипт в ячейке, чтобы получить границы для доступного диапазона электрического транспортного средства.

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


# Get bounds for the electric vehicle's reachable range.
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

## <a name="search-electric-vehicle-charging-stations-within-reachable-range"></a>Поиск зарядной станции для электрического транспортного средства в пределах доступного диапазона

После получения доступного диапазона (изохроны) для электрического транспортного средства можно выполнить поиск зарядных станций в этом диапазоне. Приведенный ниже скрипт вызывает [API запроса Post на поиск внутри геометрии](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry) Azure Maps для поиска зарядных станций для электрического транспортного средства в пределах максимально доступного диапазона для автомобиля, а затем анализирует ответ по массиву доступных расположений.

Выполните приведенный ниже скрипт, чтобы найти зарядные станции для электрических транспортных средств в пределах доступного диапазона.

```python
# Search for EV stations within reachable range.
searchPolyResponse = await (await session.post(url = "https://atlas.microsoft.com/search/geometry/json?subscription-key={}&api-version=1.0&query=electric vehicle station&idxSet=POI&limit=50".format(subscriptionKey), json = boundsData)).json() 

reachableLocations = []
for loc in range(len(searchPolyResponse["results"])):
                location = list(searchPolyResponse["results"][loc]["position"].values())
                location[0], location[1] = location[1], location[0]
                reachableLocations.append(location)
```

## <a name="upload-reachable-range-and-charging-points-to-azure-maps-data-service"></a>Загрузка доступного диапазона и точек зарядки в службу данных Azure Maps

Чтобы визуализировать на карте зарядные станции и границу для максимально доступного диапазона для электрического транспортного средства, нам нужно загрузить данные о границах и зарядных станциях в виде объектов geojson в службу данных Azure Maps, используя [API загрузки данных](https://docs.microsoft.com/rest/api/maps/data/uploadpreview). 

Выполните приведенные ниже скрипты в двух ячейках, чтобы загрузить данные о границах и зарядных точках в службу данных Azure Maps.

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

# Upload range data to Azure Maps data service.
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

# Upload EV charging stations data to Azure Maps data service.
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

## <a name="render-charging-stations-and-reachable-range-on-map"></a>Отрисовка зарядных станций и доступного диапазона на карте

После загрузки данных в службу данных необходимо запустить приведенный ниже скрипт, чтобы вызвать [службу получения изображения карты](https://docs.microsoft.com/rest/api/maps/render/getmapimage) Azure Maps для отрисовки точек зарядки и максимально доступной границы на статическом изображении карты.

```python
# Get bounds for bounding box.
def getBounds(polyBounds):
    maxLon = max(map(lambda x: x[0], polyBounds))
    minLon = min(map(lambda x: x[0], polyBounds))

    maxLat = max(map(lambda x: x[1], polyBounds))
    minLat = min(map(lambda x: x[1], polyBounds))
    
    # Buffer the bounding box by 10% to account for the pixel size of pins at the ends of the route.
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

# Render range and EV charging points on the map.
staticMapResponse =  await session.get("https://atlas.microsoft.com/map/static/png?api-version=1.0&subscription-key={}&pins={}&path={}&bbox={}&zoom=12".format(subscriptionKey,encodedPins,path,str(minLon)+", "+str(minLat)+", "+str(maxLon)+", "+str(maxLat)))

poiRangeMap = await staticMapResponse.content.read()

display(Image(poiRangeMap))
```

![диапазон расположения](./media/tutorial-ev-routing/location-range.png)


## <a name="find-the-optimal-charging-station-to-stop"></a>Поиск оптимальной зарядной станции ​​для остановки

После получения списка потенциальных зарядных станций в пределах доступного диапазона нам необходимо определить, до какой из станций можно добраться за минимальное время. Приведенный ниже скрипт вызывает [API матричной маршрутизации Azure Maps ](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview), возвращая значение времени в пути с учетом расположения заданного транспортного средства и расстояние до расположения каждой заданной зарядной станции. Скрипт, указанный в следующей ячейке, анализирует ответ для определения расположения ближайшей доступной зарядной станции с учетом времени.

Запустите следующий скрипт, чтобы найти ближайшую доступную зарядную станцию, до которой можно добраться за минимальное время.

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

# Get the travel time and distance to every given charging station location.
searchPolyRes = await (await session.post(url = "https://atlas.microsoft.com/route/matrix/json?subscription-key={}&api-version=1.0&routeType=shortest&waitForResults=true".format(subscriptionKey), json = locationData)).json()

distances = []
for dist in range(len(reachableLocations)):
    distances.append(searchPolyRes["matrix"][0][dist]["response"]["routeSummary"]["travelTimeInSeconds"])

minDistLoc = []
minDistIndex = distances.index(min(distances))
minDistLoc.extend([reachableLocations[minDistIndex][1], reachableLocations[minDistIndex][0]])
closestChargeLoc = ",".join(str(i) for i in minDistLoc)
```

## <a name="calculate-route-to-the-closest-charging-station"></a>Расчет маршрута до ближайшей зарядной станции

Теперь, когда мы нашли ближайшую зарядную станцию, необходимо вызвать [API получения направлений маршрута](https://docs.microsoft.com/rest/api/maps/route/getroutedirections), чтобы запросить подробные сведения о маршруте от текущего расположения электрического транспортного средства до зарядной станции.

Запустите скрипт, указанный в следующей ячейке, чтобы получить маршрут, и проанализируйте ответ, чтобы создать объект geojson, представляющий маршрут.

```python
# Get route from current location to the closest charging station. 
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

Чтобы выполнить визуализацию маршрута, мы сначала отправим данные маршрута в виде объекта geojson в службу данных Azure Maps с помощью [API загрузки данных](https://docs.microsoft.com/rest/api/maps/data/uploadpreview) Azure Maps. Затем необходимо вызвать службу отрисовки, [API получения изображения карты](https://docs.microsoft.com/rest/api/maps/render/getmapimage), чтобы отрисовать маршрут на карте и визуализировать его.

Запустите приведенный ниже скрипт, чтобы получить изображение для отрисованного маршрута на карте.

```python
# Upload route data to Azure data service.
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


# Get bounds for bounding box.
minLat, maxLat = (float(destination[0]),currentLocation[0]) if float(destination[0])<currentLocation[0] else (currentLocation[0], float(destination[0]))
minLon, maxLon = (float(destination[1]),currentLocation[1]) if float(destination[1])<currentLocation[1] else (currentLocation[1], float(destination[1]))

#Buffer the bounding box by 10% to account for the pixel size of pins at the ends of the route.
lonBuffer = (maxLon-minLon)*0.1
minLon -= lonBuffer
maxLon += lonBuffer

latBuffer = (maxLat-minLat)*0.1
minLat -= latBuffer
maxLat += latBuffer

# Render route on the map.
staticMapResponse = await session.get("https://atlas.microsoft.com/map/static/png?api-version=1.0&subscription-key={}&&path={}&pins={}&bbox={}&zoom=16".format(subscriptionKey,path,pins,str(minLon)+", "+str(minLat)+", "+str(maxLon)+", "+str(maxLat)))

staticMapImage = await staticMapResponse.content.read()

await session.close()
display(Image(staticMapImage))
```

![маршрут](./media/tutorial-ev-routing/route.png)

## <a name="next-steps"></a>Дополнительная информация

В рамках этого руководства вы узнали, как напрямую вызывать REST API Azure Maps и визуализировать данные Azure Maps с помощью Python.

Для изучения API-интерфейсов Azure Maps, используемых в этом руководстве, прочтите следующие статьи.

* [Route — Get Route Range](https://docs.microsoft.com/rest/api/maps/route/getrouterange) (Маршрут — получение диапазона маршрута)
* [Search — Post Search Inside Geometry](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry) (Поиск — запрос Post на поиск внутри геометрии)
* [Data — Upload Preview](https://docs.microsoft.com/rest/api/maps/data/uploadpreview) (Данные — предварительная версия отправки)
* [Render — Get Map Image](https://docs.microsoft.com/rest/api/maps/render/getmapimage) (Отрисовка — получение изображения карты)
* [Route — Post Route Matrix Preview](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview) (Маршрут — предварительная версия запроса Post матрицы маршрута)
* [Route — Get Route Directions](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) (Маршрут — получение направлений маршрута)

Полный список API-интерфейсов Azure Maps вы найдете здесь:

* [Интерфейсы REST API для Azure Maps](https://docs.microsoft.com/azure/azure-maps/#reference)

Дополнительные сведения о службе "Записные книжки Azure" см. в следующей статье:

* [Записные книжки Azure](https://docs.microsoft.com/azure/notebooks)