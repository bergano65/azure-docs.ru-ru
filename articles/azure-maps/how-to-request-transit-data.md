---
title: Запрос транзитных данных | Карты Microsoft Azure
description: В этой статье вы узнаете, как запросить общие транзитные данные с помощью службы мобильности Microsoft Azure Maps.
author: philmea
ms.author: philmea
ms.date: 09/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 135da2102b77f1d6a94b4458e10fba8c4f2b8534
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86517507"
---
# <a name="request-public-transit-data-using-the-azure-maps-mobility-service"></a>Запрос общедоступных транзитных данных с помощью службы Azure Maps Mobility Service 

В этой статье показано, как использовать [службу Azure Maps Mobility Service](https://aka.ms/AzureMapsMobilityService) для запроса общедоступных транзитных данных. Транзитные данные включают остановку передачи, сведения о маршруте и оценки времени поездки.

В этой статье вы узнаете, как:

* Получение идентификатора области Metro с помощью [API-интерфейса «получить область Metro](https://aka.ms/AzureMapsMobilityMetro) »
* Запрос, находящихся поблизости, перестает использовать службу [получения ближайших транзитных](https://aka.ms/AzureMapsMobilityNearbyTransit) услуг.
* Запрос [получения API транзитных маршрутов](https://aka.ms/AzureMapsMobilityTransitRoute) для планирования маршрута с использованием общедоступного транзита.
* Запрос на перенаправление геометрического маршрута и подробное расписание для маршрута с помощью [API-интерфейса для получения пути](https://aka.ms/https://azure.microsoft.com/services/azure-maps/).


## <a name="prerequisites"></a>Предварительные требования

Сначала необходимо иметь учетную запись Azure Maps и ключ подписки, чтобы выполнять вызовы Azure Maps общедоступных транзитных API. Для получения сведений следуйте инструкциям в разделе [Создание учетной записи](quick-demo-map-app.md#create-an-azure-maps-account) для создания учетной записи Azure Maps. Выполните действия, описанные в разделе [Получение первичного ключа](quick-demo-map-app.md#get-the-primary-key-for-your-account) , чтобы получить первичный ключ для вашей учетной записи. Дополнительные сведения о проверке подлинности в Azure Maps см. в [этой статье](./how-to-manage-authentication.md).


В этой статье для создания вызовов REST используется [приложение Postman](https://www.getpostman.com/apps). Вы можете использовать любую среду разработки API.


## <a name="get-a-metro-area-id"></a>Получить идентификатор области метро

Чтобы запросить транзитную информацию для определенной области города, вам понадобится эта `metroId` область. [Интерфейс API "получить область метро](https://aka.ms/AzureMapsMobilityMetro) " позволяет запрашивать области метро, в которых доступна Azure Mapsная служба Mobility Service. Ответ содержит такие сведения `metroId` , как, `metroName` и представление геометрии области Metro в формате геоjson.

Давайте создадим запрос на получение области Metro для идентификатора области "Сиэтл — Tacoma". Чтобы запросить идентификатор для области Metro, выполните следующие действия.

1. Откройте приложение POST и создадим коллекцию для хранения запросов. В верхней части приложения Postman выберите элемент **Создать**. В окне **Create New** (Создание) выберите **Collection** (Коллекция).  Присвойте имя коллекции и нажмите кнопку **Создать**.

2. Чтобы создать запрос, нажмите кнопку **Создать** еще раз. В окне **Create New** (Создание) выберите **Request** (Запрос). Введите **Имя запроса** для запроса. Выберите коллекцию, созданную на предыдущем шаге, в качестве расположения для сохранения запроса. Затем нажмите кнопку **Сохранить**.
    
    ![Создание запроса в POST](./media/how-to-request-transit-data/postman-new.png)

3. Выберите метод **получения** HTTP на вкладке Построитель и введите следующий URL-адрес для создания запроса GET. Замените на `{subscription-key}` Azure Maps первичный ключ.

    ```HTTP
    https://atlas.microsoft.com/mobility/metroArea/id/json?subscription-key={subscription-key}&api-version=1.0&query=47.63096,-122.126
    ```

4. После успешного выполнения запроса вы получите следующий ответ:

    ```JSON
    {
        "results": [
            {
                "metroId": 522,
                "metroName": "Seattle–Tacoma–Bellevue, WA",
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 48.5853,
                        "longitude": -124.80934
                    },
                    "btmRightPoint": {
                        "latitude": 46.90534,
                        "longitude": -121.55032
                    }
                },
                "geometry": {
                    "type": "Polygon",
                    "coordinates": [
                        [
                            [
                                -121.99604,
                                47.16147
                            ],
                            [
                                -121.97051,
                                47.17222
                            ],
                            [
                                -121.96308,
                                47.17671
                            ],
                            ...,
                            ...,
                            ...,
                            [
                                -122.01525,
                                47.16008
                            ],
                            [
                                -122.00553,
                                47.15919
                            ],
                            [
                                -121.99604,
                                47.16147
                            ]
                        ]
                    ]
                }
            }
        ]
    }
    ```

5. Скопируйте его `metroId` , его потребуется использовать позже.

## <a name="request-nearby-transit-stops"></a>Запрос нахождения ближайших транзитных точек останавливается

Служба Azure Maps [получить близлежащие транзитные](https://aka.ms/AzureMapsMobilityNearbyTransit) службы позволяет выполнять поиск по транзитным объектам.  API возвращает сведения о транзитном объекте, такие как прекращение общедоступного транзита и передача общих велосипедов по заданному расположению. Далее мы выполним запрос к службе для поиска ближайших общественных транзитных мест в радиусе 300 метров по отношению к заданному расположению. В запросе необходимо включить `metroId` полученный ранее объект.

Чтобы выполнить запрос на [транзитное](https://aka.ms/AzureMapsMobilityNearbyTransit)подключение, выполните следующие действия:

1. В окне POST щелкните **новый запрос**  |  **получить запрос** и назовите его **Get поблизости**.

2. На вкладке Построитель выберите метод **Get** HTTP, введите следующий URL-адрес запроса для КОНЕЧНОЙ точки API и нажмите кнопку **Отправить**.

    ```HTTP
    https://atlas.microsoft.com/mobility/transit/nearby/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&query=47.63096,-122.126&radius=300&objectType=stop
    ```

3. После успешного запроса структура ответа должна выглядеть следующим образом:

    ```JSON
    {
        "results": [
            {
                "id": "522---2060603",
                "type": "stop",
                "objectDetails": {
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "stopCode": "71300",
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                },
                "position": {
                    "latitude": 47.631504,
                    "longitude": -122.125275
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 47.632413,
                        "longitude": -122.12659
                    },
                    "btmRightPoint": {
                        "latitude": 47.630594,
                        "longitude": -122.123959
                    }
                }
            },
            {
                "id": "522---2061020",
                "type": "stop",
                "objectDetails": {
                    "stopKey": "68372",
                    "stopName": "NE 24th St & 160th Ave NE",
                    "stopCode": "68372",
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                },
                "position": {
                    "latitude": 47.631409,
                    "longitude": -122.127136
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 47.632318,
                        "longitude": -122.128451
                    },
                    "btmRightPoint": {
                        "latitude": 47.630499,
                        "longitude": -122.12582
                    }
                }
            },
            {
                "id": "522---2060604",
                "type": "stop",
                "objectDetails": {
                    "stopKey": "71310",
                    "stopName": "NE 24th St & 160th Ave NE",
                    "stopCode": "71310",
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                },
                "position": {
                    "latitude": 47.631565,
                    "longitude": -122.127808
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 47.632474,
                        "longitude": -122.129124
                    },
                    "btmRightPoint": {
                        "latitude": 47.630655,
                        "longitude": -122.126492
                    }
                }
            }
        ]
    }   
    ```

Если вы внимательно следите за структурой ответа, вы увидите, что она содержит параметры для каждого транзитного объекта. Каждый транзитный объект имеет такие параметры `id` , как, `type` , `stopName` ,, `mainTransitType` `mainAgencyName` и положения объекта в координатах.

В целях обучения мы будем использовать точку `id` останова в качестве источника для нашего маршрута в следующем разделе.  


## <a name="request-a-transit-route"></a>Запрос транзитного маршрута

API Azure Maps [получения транзитных маршрутов](https://aka.ms/AzureMapsMobilityTransitRoute) позволяет планировать поездки. Он возвращает наилучшие возможные варианты маршрута от источника к назначению. Служба предоставляет различные режимы поездки, в том числе прохождение, поездкам и общедоступные передачи. Далее мы будем выполнять поиск по маршруту с ближайшей шины на место в Сиэтле.

### <a name="get-location-coordinates-for-destination"></a>Получение координат расположения для назначения

Чтобы получить координаты места расположения стрелки "игольчатый", можно использовать Azure Maps [Нечеткие служба поиска](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy).

Чтобы выполнить запрос к службе поиска нечетких запросов, выполните следующие действия:

1. В окне POST щелкните **создать запрос**  |  **получить запрос** и назовите его « **Получение координат расположения**».

2.  На вкладке Построитель выберите метод **Get** HTTP, введите следующий URL-адрес запроса и нажмите кнопку **Отправить**.
 
    ```HTTP
    https://atlas.microsoft.com/search/fuzzy/json?subscription-key={subscription-key}&api-version=1.0&query=space needle
    ```
    
3. Если взглянуть на ответ внимательно, он содержит несколько расположений в результатах поиска в виде стрелки. Каждый результат содержит координаты расположения под **позицией**. Скопируйте `lat` и `lon` под **позицией** первого результата.
    
   ```JSON
   {
        "summary": {
            "query": "space needle",
            "queryType": "NON_NEAR",
            "queryTime": 35,
            "numResults": 8,
            "offset": 0,
            "totalResults": 11,
            "fuzzyLevel": 1
        },
        "results": [
            {
                "type": "POI",
                "id": "US/POI/p0/6993440",
                "score": 4.67369,
                "info": "search:ta:840539001406144-US",
                "poi": {
                    "name": "Space Needle",
                    "phone": "+(1)-(206)-9052100",
                    "categorySet": [
                        {
                            "id": 7376009
                        }
                    ],
                    "url": "www.spaceneedle.com",
                    "categories": [
                        "important tourist attraction",
                        "tower"
                    ],
                    "classifications": [
                        {
                            "code": "IMPORTANT_TOURIST_ATTRACTION",
                            "names": [
                                {
                                    "nameLocale": "en-US",
                                    "name": "important tourist attraction"
                                },
                                {
                                    "nameLocale": "en-US",
                                    "name": "tower"
                                }
                            ]
                        }
                    ]
                },
                "address": {
                    "streetNumber": "400",
                    "streetName": "Broad St",
                    "municipalitySubdivision": "South Lake Union, Seattle, Lower Queen Anne",
                    "municipality": "Seattle",
                    "countrySecondarySubdivision": "King",
                    "countryTertiarySubdivision": "Seattle",
                    "countrySubdivision": "WA",
                    "postalCode": "98109",
                    "countryCode": "US",
                    "country": "United States",
                    "countryCodeISO3": "USA",
                    "freeformAddress": "400 Broad St, Seattle, WA 98109",
                    "localName": "Seattle",
                    "countrySubdivisionName": "Washington"
                },
                "position": {
                    "lat": 47.62039,
                    "lon": -122.34928
                },
                "viewport": {
                    "topLeftPoint": {
                        "lat": 47.62129,
                        "lon": -122.35061
                    },
                    "btmRightPoint": {
                        "lat": 47.61949,
                        "lon": -122.34795
                    }
                },
                "entryPoints": [
                    {
                        "type": "main",
                        "position": {
                            "lat": 47.61982,
                            "lon": -122.34886
                        }
                    }
                ]
            },
            ...,
            ...,
            ...
        ]
    }
    ``` 
    

### <a name="request-route"></a>Маршрут запроса

Чтобы выполнить запрос маршрута, выполните следующие действия:

1. В окне POST щелкните **создать запрос**  |  **получить запрос** и назовите его **получить сведения о маршруте**.

2. На вкладке Построитель выберите метод **Get** HTTP, введите следующий URL-адрес запроса для КОНЕЧНОЙ точки API и нажмите кнопку **Отправить**.

    Мы зазапрашиваем открытые транзитные маршруты для шины, указав `modeType` Параметры и `transitType` . URL-адрес запроса содержит расположения, полученные в предыдущих разделах. Для у `originType` нас теперь есть **стопид**. И для у `destionationType` нас есть **Расположение**.

    См. [список параметров URI](https://aka.ms/AzureMapsMobilityTransitRoute#uri-parameters) , которые можно использовать в запросе к [API получения транзитных маршрутов](https://aka.ms/AzureMapsMobilityTransitRoute). 
  
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/route/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&originType=stopId&origin=522---2060603&destionationType=position&destination=47.62039,-122.34928&modeType=publicTransit&transitType=bus
    ```

3. После успешного запроса структура ответа должна выглядеть следующим образом:

    ```JSON
    {
        "results": [
            {
                "itineraryId": "cb6b6b6f-5cda-451e-b68d-2e97971dd60c---20190906BBBEC4D2219A436A9D794224978C9BBF:0---522",
                "departureTime": "2019-09-07T01:01:50Z",
                "arrivalTime": "2019-09-07T02:16:33Z",
                "travelTimeInSeconds": 4483,
                "numberOfLegs": 8,
                "legs": [
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T01:01:50Z",
                        "legEndTime": "2019-09-07T01:01:50Z",
                        "caption": "249"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T01:01:50Z",
                        "legEndTime": "2019-09-07T01:26:00Z",
                        "caption": "249",
                        "lengthInMeters": 9139
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T01:26:00Z",
                        "legEndTime": "2019-09-07T01:28:00Z",
                        "caption": "255"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T01:28:00Z",
                        "legEndTime": "2019-09-07T01:57:21Z",
                        "caption": "255",
                        "lengthInMeters": 13136
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-09-07T01:57:22Z",
                        "legEndTime": "2019-09-07T02:01:27Z",
                        "caption": "Denny Way",
                        "lengthInMeters": 308
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T02:01:27Z",
                        "legEndTime": "2019-09-07T02:06:33Z",
                        "caption": "8"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T02:06:33Z",
                        "legEndTime": "2019-09-07T02:12:41Z",
                        "caption": "8",
                        "lengthInMeters": 1060
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-09-07T02:12:42Z",
                        "legEndTime": "2019-09-07T02:16:33Z",
                        "lengthInMeters": 251
                    }
                ]
            },
            ...,
            {
                "itineraryId": "cb6b6b6f-5cda-451e-b68d-2e97971dd60c---20190906BBBEC4D2219A436A9D794224978C9BBF:2---522",
                "departureTime": "2019-09-07T00:49:32Z",
                "arrivalTime": "2019-09-07T02:20:06Z",
                "travelTimeInSeconds": 5434,
                "numberOfLegs": 10,
                "legs": [
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T00:49:32Z",
                        "legEndTime": "2019-09-07T00:49:32Z",
                        "caption": "226"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T00:49:32Z",
                        "legEndTime": "2019-09-07T01:15:00Z",
                        "caption": "226",
                        "lengthInMeters": 6792
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T01:15:00Z",
                        "legEndTime": "2019-09-07T01:20:00Z",
                        "caption": "241"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T01:20:00Z",
                        "legEndTime": "2019-09-07T01:28:00Z",
                        "caption": "241",
                        "lengthInMeters": 3397
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T01:28:00Z",
                        "legEndTime": "2019-09-07T01:33:00Z",
                        "caption": "550"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T01:33:00Z",
                        "legEndTime": "2019-09-07T01:58:00Z",
                        "caption": "550",
                        "lengthInMeters": 12899
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-09-07T01:58:01Z",
                        "legEndTime": "2019-09-07T01:59:21Z",
                        "caption": "4th Avenue South",
                        "lengthInMeters": 99
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T01:59:21Z",
                        "legEndTime": "2019-09-07T02:01:00Z",
                        "caption": "33"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T02:01:00Z",
                        "legEndTime": "2019-09-07T02:13:29Z",
                        "caption": "33,24",
                        "lengthInMeters": 2447
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-09-07T02:13:30Z",
                        "legEndTime": "2019-09-07T02:20:06Z",
                        "lengthInMeters": 457
                    }
                ]
            }
        ]
    }
    ```

4. Если вы следите за осторожностью, в ответе есть несколько маршрутов **шины** . Каждый маршрут имеет уникальный **идентификатор** и сводку, описывающую каждую часть маршрута. Точка маршрута является частью маршрута между двумя вайпоинтсами. Далее мы зазапрашиваем сведения о быстром маршруте, используя `itineraryId` в ответе.

## <a name="request-fastest-route-itinerary"></a>Запрашивать самый быстрый маршрут маршрутов

Служба Azure Maps [получить путевые](https://aka.ms/AzureMapsMobilityTransitItinerary) маршруты позволяет запрашивать данные для конкретного маршрута, используя **идентификатор** маршрута, возвращенный службой [API получения транзитных маршрутов](https://aka.ms/AzureMapsMobilityTransitRoute) . Чтобы выполнить запрос, выполните следующие действия:

1. В окне POST щелкните **создать запрос**  |  **получить запрос** и назовите его **получить сведения о транзитном транспорте**.

2. На вкладке Построитель выберите метод **Get** HTTP. Введите следующий URL-адрес запроса для конечной точки API и нажмите кнопку **Отправить**.

    Мы будем присвоить `detailType` параметру значение **Geometry** , чтобы ответ содержал сведения о недоступности для бесплатного транзитного пути и последующего включения навигации для прохода и нахождения велосипеда маршрута.

    ```HTTP
    https://atlas.microsoft.com/mobility/transit/itinerary/json?api-version=1.0&subscription-key={subscription-key}&query={itineraryId}&detailType=geometry
    ```
    
3. После успешного запроса структура ответа должна выглядеть следующим образом:

    ```JSON
    {
        "departureTime": "2019-09-07T01:01:50Z",
        "arrivalTime": "2019-09-07T02:16:33Z",
        "legs": [
            {
                "legType": "Wait",
                "legStartTime": "2019-09-07T01:01:50Z",
                "legEndTime": "2019-09-07T01:01:50Z",
                "lineGroup": {
                    "lineGroupId": "522---666077",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "249",
                    "caption1": "Overlake TC - South Bellevue P&R",
                    "caption2": "249 Overlake TC - South Bellevue P&R",
                    "color": "347E5D",
                    "transitType": "Bus"
                },
                "line": {
                    "lineId": "522---3760143",
                    "lineGroupId": "522---666077",
                    "direction": "backward",
                    "agencyId": "522---5872",
                    "lineNumber": "249",
                    "lineDestination": "South Bellevue S Kirkland P&R"
                },
                "stops": [
                    {
                        "stopId": "522---2060603",
                        "stopKey": "71300",
                        "stopName": "NE 24th St & 162nd Ave NE",
                        "stopCode": "71300",
                        "position": {
                            "latitude": 47.631504,
                            "longitude": -122.125275
                        },
                        "mainTransitType": "Bus",
                        "mainAgencyId": "522---5872"
                    },
                    {
                        "stopId": "522---2061703",
                        "stopKey": "74450",
                        "stopName": "South Kirkland P&R & 108th Ave NE",
                        "stopCode": "74450",
                        "position": {
                            "latitude": 47.643852,
                            "longitude": -122.196693
                        },
                        "mainTransitType": "Bus",
                        "mainAgencyId": "522---5872"
                    }
                ],
                "waitOnVehicle": false
            },
            {
                "legType": "Bus",
                "legStartTime": "2019-09-07T01:01:50Z",
                "legEndTime": "2019-09-07T01:26:00Z",
                "lineGroup": {
                    "lineGroupId": "522---666077",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "249",
                    "caption1": "Overlake TC - South Bellevue P&R",
                    "caption2": "249 Overlake TC - South Bellevue P&R",
                    "color": "347E5D",
                    "transitType": "Bus"
                },
                "line": {
                    "lineId": "522---3760143",
                    "lineGroupId": "522---666077",
                    "direction": "backward",
                    "agencyId": "522---5872",
                    "lineNumber": "249",
                    "lineDestination": "South Bellevue S Kirkland P&R"
                },
                "stops": [
                    {
                        "stopId": "522---2060603",
                        "stopKey": "71300",
                        "stopName": "NE 24th St & 162nd Ave NE",
                        "stopCode": "71300",
                        "position": {
                            "latitude": 47.631504,
                            "longitude": -122.125275
                        },
                        "mainTransitType": "Bus",
                        "mainAgencyId": "522---5872"
                    },
                    {
                        "stopId": "522---2060604",
                        "stopKey": "71310",
                        "stopName": "NE 24th St & 160th Ave NE",
                        "stopCode": "71310",
                        "position": {
                            "latitude": 47.631565,
                            "longitude": -122.127808
                        },
                        "mainTransitType": "Bus",
                        "mainAgencyId": "522---5872"
                    },
                    ...,
                    ...,
                    {
                        "stopId": "522---2061704",
                        "stopKey": "74451",
                        "stopName": "Northup Way & NE 33rd Pl",
                        "stopCode": "74451",
                        "position": {
                            "latitude": 47.640911,
                            "longitude": -122.194443
                        },
                        "mainTransitType": "Bus",
                        "mainAgencyId": "522---5872"
                    },
                    {
                        "stopId": "522---2061703",
                        "stopKey": "74450",
                        "stopName": "South Kirkland P&R & 108th Ave NE",
                        "stopCode": "74450",
                        "position": {
                            "latitude": 47.643852,
                            "longitude": -122.196693
                        },
                        "mainTransitType": "Bus",
                        "mainAgencyId": "522---5872"
                    }
                ],
                "geometry": {
                    "type": "LineString",
                    "coordinates": [
                        [
                            -122.12527,
                            47.63143
                        ],
                        [
                            -122.12529,
                            47.63143
                        ],
                        [
                            -122.12561,
                            47.63144
                        ],
                        [
                            -122.12701,
                            47.63148
                        ],
                        ...,
                        ...,
                        ...,
                        [
                            -122.19601,
                            47.64304
                        ],
                        [
                            -122.19584,
                            47.64315
                        ],
                        [
                            -122.19677,
                            47.6438
                        ]
                    ]
                }
            },
            ...,
            ...,
            ...,
            {
                "legType": "Walk",
                "legStartTime": "2019-09-07T02:12:42Z",
                "legEndTime": "2019-09-07T02:16:33Z",
                "steps": [
                    {
                        "direction": {
                            "relativeDirection": "depart"
                        },
                        "streetName": "Denny Way"
                    },
                    {
                        "direction": {
                            "relativeDirection": "right"
                        },
                        "streetName": "4th Avenue North"
                    },
                    {
                        "direction": {
                            "relativeDirection": "right"
                        },
                        "streetName": "Broad Street"
                    }
                ],
                "origin": {
                    "position": {
                        "latitude": 47.618578,
                        "longitude": -122.348058
                    }
                },
                "destination": {
                    "position": {
                        "latitude": 47.62039,
                        "longitude": -122.34928
                    }
                },
                "geometry": {
                    "type": "LineString",
                    "coordinates": [
                        [
                            -122.34806,
                            47.61857
                        ],
                        [
                            -122.3481,
                            47.61857
                        ],
                        [
                            -122.34894,
                            47.61858
                        ],
                        [
                            -122.34892,
                            47.61964
                        ],
                        [
                            -122.34877,
                            47.61975
                        ],
                        [
                            -122.3492,
                            47.62001
                        ],
                        [
                            -122.34918,
                            47.62003
                        ],
                        [
                            -122.34917,
                            47.62006
                        ],
                        [
                            -122.34916,
                            47.62008
                        ],
                        [
                            -122.34916,
                            47.62008
                        ],
                        [
                            -122.34916,
                            47.62008
                        ],
                        [
                            -122.34916,
                            47.62008
                        ],
                        [
                            -122.34928,
                            47.62039
                        ]
                    ]
                }
            }
        ]
    }
    ```

## <a name="next-steps"></a>Дальнейшие действия

Узнайте, как запрашивать данные в режиме реального времени с помощью службы Mobility Service:

> [!div class="nextstepaction"]
> [Как запросить данные в режиме реального времени](how-to-request-real-time-data.md)

Изучите документацию по API службы Mobility Service Azure Maps

> [!div class="nextstepaction"]
> [Документация по API службы Mobility Service](https://aka.ms/AzureMapsMobilityService)

