---
title: Запрос транзитных данных Карты Microsoft Azure
description: В этой статье вы узнаете, как запрашивать данные общественного транспорта с помощью службы мобильности Microsoft Azure Maps.
author: philmea
ms.author: philmea
ms.date: 09/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: f60b66790342874620971c8f15a1e8ace9a3c7cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335456"
---
# <a name="request-public-transit-data-using-the-azure-maps-mobility-service"></a>Запрос данных общественного транспорта с помощью службы мобильности Azure Maps 

В этой статье показано, как использовать [службу мобильности](https://aka.ms/AzureMapsMobilityService) Azure Maps для запроса данных общественного транспорта. Транзитные данные включают транзитные остановки, информацию о маршрутах и оценку времени в пути.

В этой статье вы узнаете, как:

* Получить идентификатор области метро с помощью [API Get Metro Area](https://aka.ms/AzureMapsMobilityMetro)
* Запрос близлежащих транзитных остановок с помощью [Get Nearby Transit](https://aka.ms/AzureMapsMobilityNearbyTransit) службы.
* Запрос [Получить транзитных маршрутов API](https://aka.ms/AzureMapsMobilityTransitRoute) для планирования маршрута с помощью общественного транспорта.
* Запрос геометрии транзитного маршрута и подробного расписания маршрута с помощью [API Get Transit.](https://aka.ms/https://azure.microsoft.com/services/azure-maps/)


## <a name="prerequisites"></a>Предварительные требования

Сначала необходимо иметь учетную запись Azure Maps и ключ подписки для любых звонков в APIs общественного транспорта Azure Maps. Для получения информации следуйте инструкциям в [создании учетной записи](quick-demo-map-app.md#create-an-account-with-azure-maps) для создания учетной записи Azure Maps. Выполните шаги в [получить основной ключ,](quick-demo-map-app.md#get-the-primary-key-for-your-account) чтобы получить основной ключ для вашей учетной записи. Дополнительные сведения о проверке подлинности в Azure Maps см. в [этой статье](./how-to-manage-authentication.md).


В этой статье для создания вызовов REST используется [приложение Postman](https://www.getpostman.com/apps). Вы можете использовать любую среду разработки API.


## <a name="get-a-metro-area-id"></a>Получить идентификатор зоны метро

Для того, чтобы запросить информацию о транзите `metroId` для конкретной столичной области, вам понадобится этот район. [API Get Metro Area](https://aka.ms/AzureMapsMobilityMetro) позволяет запрашивать области метро, в которых доступна служба мобильности Azure Maps. Ответ включает в себя `metroId`такие `metroName`детали, как , и представление геометрии области метро в формате GeoJSON.

Давайте сделаем запрос, чтобы получить Metro Area для Сиэтл-Такома метро области ID. Чтобы запросить id для области метро, выполнить следующие шаги:

1. Откройте приложение Почтальон и создадим коллекцию для хранения запросов. В верхней части приложения Почтальон, выберите **новый**. В **окне «Создайте новое»** выберите **Collection**.  Назовите коллекцию и выберите кнопку **«Создание».**

2. Чтобы создать запрос, выберите **Новый** снова. В окне **«Создать новое»** выберите **Запрос**. Введите **имя запроса.** Выберите созданную на предыдущем этапе коллекцию в качестве местоположения, в котором можно сохранить запрос. Затем выберите **Сохранить**.
    
    ![Создать запрос в Postman](./media/how-to-request-transit-data/postman-new.png)

3. Выберите метод **GET** HTTP на вкладке builder и введите следующий URL для создания запроса GET. Замените `{subscription-key}`основной ключ Azure Maps.

    ```HTTP
    https://atlas.microsoft.com/mobility/metroArea/id/json?subscription-key={subscription-key}&api-version=1.0&query=47.63096,-122.126
    ```

4. После успешного запроса вы получите следующий ответ:

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

5. Копировать `metroId`, мы должны использовать его позже.

## <a name="request-nearby-transit-stops"></a>Запрос близлежащих транзитных остановок

Служба Azure Maps [Get Nearby Transit](https://aka.ms/AzureMapsMobilityNearbyTransit) позволяет осуществлять поиск транзитных объектов.  API возвращает детали транзитного объекта, такие как остановки общественного транспорта и общие велосипеды вокруг данного местоположения. Далее мы сделаем запрос в службу для поиска близлежащих остановок общественного транспорта в радиусе 300 метров вокруг данного места. В запросе мы должны `metroId` включить извлеченные ранее.

Чтобы сделать запрос на [Get Nearby Transit,](https://aka.ms/AzureMapsMobilityNearbyTransit)выполните ниже:

1. В Postman, нажмите **Новый запрос** | **GET запрос** и назвать его Получить **Рядом останавливается**.

2. На вкладке Builder выберите метод **GET** HTTP, введите URL-адрес следующего запроса для своей конечной точки API и нажмите **«Отправить».**

    ```HTTP
    https://atlas.microsoft.com/mobility/transit/nearby/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&query=47.63096,-122.126&radius=300&objectType=stop
    ```

3. После успешного запроса структура ответа должна выглядеть как приведенная ниже:

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

Если внимательно наблюдать за структурой ответа, то можно увидеть, что она содержит параметры для каждого транзитного объекта. Каждый транзитный объект `id`имеет `type` `stopName`такие `mainTransitType` `mainAgencyName`параметры, как, , , , и положение, в координатах, объекта.

Для обучения, мы будем использовать `id` автобусных остановок в качестве источника, для нашего маршрута в следующем разделе.  


## <a name="request-a-transit-route"></a>Запрос транзитного маршрута

Azure Maps [Get Transit Routes API](https://aka.ms/AzureMapsMobilityTransitRoute) позволяет планировать поездки. Он возвращает наилучшие возможные варианты маршрута от происхождения к месту назначения. Услуга предоставляет различные виды поездок, в том числе пешком, езда на велосипеде, и общественный транспорт. Далее мы будем искать маршрут от ближайшей автобусной остановки до башни Space Needle в Сиэтле.

### <a name="get-location-coordinates-for-destination"></a>Получить координаты местоположения для назначения

Чтобы получить координаты местоположения башни Space Needle, позволяет использовать службу поиска Azure Maps [Fuzzy.](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)

Чтобы сделать запрос в поисковую службу Fuzzy, выполните ниже:

1. В Postman, нажмите **Новый запрос** | **GET запрос** и назвать его Получить **координаты местоположения**.

2.  На вкладке Builder выберите метод **GET** HTTP, введите URL-адрес следующего запроса и нажмите **«Отправить».**
 
    ```HTTP
    https://atlas.microsoft.com/search/fuzzy/json?subscription-key={subscription-key}&api-version=1.0&query=space needle
    ```
    
3. Если вы внимательно посмотрите на ответ, он содержит несколько местоположений в результатах поиска Space Needle. Каждый результат содержит координаты местоположения под **положением.** Копировать `lat` и `lon` под **положением** первого результата.
    
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

Чтобы сделать запрос маршрута, выполните приведенные ниже шаги:

1. В Postman, нажмите **Новый запрос** | **GET запрос** и назвать его Получить Маршрут **информации**.

2. На вкладке Builder выберите метод **GET** HTTP, введите URL-адрес следующего запроса для своей конечной точки API и нажмите **«Отправить».**

    Мы будем просить маршруты общественного транспорта `modeType` для `transitType` автобуса, указав и параметры. URL-адрес запроса содержит места, извлеченные в предыдущих разделах. Для `originType`, у нас теперь есть **stopId**. А для `destionationType`, у нас есть **позиция**.

    Ознакомьтесь со [списком параметров URI, которые](https://aka.ms/AzureMapsMobilityTransitRoute#uri-parameters) можно использовать в запросе на [API Get Transit Routes.](https://aka.ms/AzureMapsMobilityTransitRoute) 
  
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/route/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&originType=stopId&origin=522---2060603&destionationType=position&destination=47.62039,-122.34928&modeType=publicTransit&transitType=bus
    ```

3. По успешному запросу структура ответа должна выглядеть как приведенная ниже:

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

4. Если вы внимательно наблюдаете, есть несколько **автобусных** маршрутов в ответ. Каждый маршрут имеет уникальный **идентификатор маршрута** и резюме, которое описывает каждый этап маршрута. Трасса является частью маршрута между двумя стоп-пойнтами. Далее мы запросим подробную информацию `itineraryId` о самом быстром маршруте, используя ответ.

## <a name="request-fastest-route-itinerary"></a>Запрос быстрый маршрут маршрута

Служба Azure Maps [Get Transit Itinerary](https://aka.ms/AzureMapsMobilityTransitItinerary) позволяет запрашивать данные по определенному маршруту, используя **идентификатор маршрута** маршрута, возвращенный службой Get Transit Routes [API.](https://aka.ms/AzureMapsMobilityTransitRoute) Чтобы сделать запрос, выполните приведенные ниже шаги:

1. В Postman, нажмите **Новый запрос** | **GET запрос** и назвать его Получить **Транзитные информации**.

2. На вкладке Builder выберите метод **GET** HTTP. Введите следующий URL-адрес запроса для своей конечной точки API и нажмите **Отправить**.

    Мы установим `detailType` параметр **геометрии** так, чтобы ответ содержит информацию о остановке для общественного транспорта и пошаговую навигацию для прогулок и велосипедных ножей маршрута.

    ```HTTP
    https://atlas.microsoft.com/mobility/transit/itinerary/json?api-version=1.0&subscription-key={subscription-key}&query={itineraryId}&detailType=geometry
    ```
    
3. По успешному запросу структура ответа должна выглядеть как приведенная ниже:

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

Узнайте, как запрашивать данные в режиме реального времени с помощью Службы мобильности:

> [!div class="nextstepaction"]
> [Как запросить данные в режиме реального времени](how-to-request-real-time-data.md)

Изучите документацию API службы мобильности Карт Azure

> [!div class="nextstepaction"]
> [Документация API службы мобильности](https://aka.ms/AzureMapsMobilityService)

