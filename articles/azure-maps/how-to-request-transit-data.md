---
title: Запрос передачи данных в Azure Maps | Документация Майкрософт
description: Запросите общедоступные транзитные данные с помощью службы мобильности Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 09/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: b28788ac7b3ce4e1997b71c683f8e0445406a391
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70915617"
---
# <a name="request-public-transit-data-using-the-azure-maps-mobility-service"></a>Запрос общедоступных транзитных данных с помощью службы Azure Maps Mobility Service 

В этой статье показано, как использовать [службу Azure Maps Mobility Service](https://aka.ms/AzureMapsMobilityService) для запроса общедоступных транзитных данных, включая остановки, сведения о маршрутах и оценки времени поездки.

В этой статье вы узнаете, как:

* Получение идентификатора области Metro с помощью [API-интерфейса «получить область Metro](https://aka.ms/AzureMapsMobilityMetro) »
* Запрос, находящихся поблизости, перестает использовать службу [получения ближайших транзитных](https://aka.ms/AzureMapsMobilityNearbyTransit) услуг.
* Запрос на [Получение API транзитных маршрутов](https://aka.ms/AzureMapsMobilityTransitRoute) для планирования маршрута с помощью общедоступного транзита.
* Запросить геометрического пути маршрута и подробное расписание для маршрута с помощью [API маршрута пути получения](https://aka.ms/https://azure.microsoft.com/services/azure-maps/).


## <a name="prerequisites"></a>Предварительные требования

Для выполнения любых вызовов Azure Maps общедоступных транзитных API-интерфейсов требуется учетная запись Maps и ключ. Чтобы получить сведения о создании учетной записи, следуйте инструкциям в статье [Управление учетной записью](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys#create-a-new-account) и выполните действия, описанные в статье [Получение первичного ключа](./tutorial-search-location.md#getkey) для получения первичного ключа подписки для вашей учетной записи.

В этой статье для создания вызовов REST используется [приложение Postman](https://www.getpostman.com/apps). Вы можете использовать любую среду разработки API.


## <a name="get-a-metro-area-id"></a>Получить идентификатор области метро

Чтобы запросить транзитную информацию для определенной области города, потребуется `metroId` для области, для которой нужно запросить транзитные данные. [Интерфейс API для Metro Area](https://aka.ms/AzureMapsMobilityMetro) позволяет запрашивать области метро, в которых доступна Azure Mapsная служба Mobility Service. Ответ включает такие сведения, `metroId` `metroName` как и представление геометрии области Metro в формате геоjson.

Давайте создадим запрос, чтобы получить область Metro для идентификатора области метро "Сиэтл — Tacoma". Чтобы запросить идентификатор для области Metro, выполните следующие действия.

1. Создайте коллекцию для хранения запросов. В приложении POST выберите **создать**. В окне **создать новое** выберите **коллекция**. Присвойте имя коллекции и нажмите кнопку **создать** .

2. Чтобы создать запрос, нажмите кнопку **создать** еще раз. В окне **создать новое** выберите **запрос**. Введите **имя запроса** для запроса, выберите коллекцию, созданную на предыдущем шаге, в качестве расположения для сохранения запроса, а затем нажмите кнопку **сохранить**.
    
    ![Создание запроса в POST](./media/how-to-request-transit-data/postman-new.png)

3. Выберите метод получения HTTP на вкладке Построитель и введите следующий URL-адрес для создания запроса GET.

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

5. `metroId`Скопируйте, чтобы использовать его позже.

## <a name="request-nearby-transit-stops"></a>Запрос нахождения ближайших транзитных точек останавливается

Служба Azure Maps [Получение ближайших транзитных](https://aka.ms/AzureMapsMobilityNearbyTransit) услуг позволяет выполнять поиск по транзитным объектам, например, при общем транзитном транспорте прекращается и общие велосипеды по заданному расположению возвращают сведения о транзитном объекте. Далее мы выполним запрос к службе для поиска ближайших общественных транзитных мест в пределах радиуса 300 метров в указанном расположении. В запросе необходимо включить объект, `metroId` полученный ранее.

Чтобы выполнить запрос на [транзитное](https://aka.ms/AzureMapsMobilityNearbyTransit)подключение, выполните следующие действия:

1. В окне POST щелкните **новый запрос** | **получить запрос** и назовите его **Get поблизости**.

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

При тщательном наблюдении за структурой ответа можно увидеть, что она содержит параметры каждого транзитного объекта, `id`например `type` `stopName`, `mainTransitType` `mainAgencyName` ,, и положения (координат) объекта.

В целях понимания мы будем использовать `id` одну из шин в качестве источника для нашего маршрута в следующем разделе.  


## <a name="request-a-transit-route"></a>Запрос транзитного маршрута

API Azure Maps [получения транзитных маршрутов](https://aka.ms/AzureMapsMobilityTransitRoute) позволяет планировать поездку, возвращая лучшие возможные варианты маршрута между источником и назначением. Служба предоставляет разнообразные режимы поездок, в том числе прохождение, поездкам и общедоступное транзитное перемещение. Далее мы будем искать маршрут от ближайшей позиции шины до пробела в Сиэтле.

### <a name="get-location-coordinates-for-destination"></a>Получение координат расположения для назначения

Для получения координат места расположения стрелки «Стрелка» позволяет использовать Azure Maps [Нечеткие служба поиска](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy).

Чтобы выполнить запрос к службе поиска нечетких запросов, выполните следующие действия:

1. В окне POST щелкните **создать запрос** | **получить запрос** и назовите его « **Получение координат расположения**».

2.  На вкладке Построитель выберите метод **Get** HTTP, введите следующий URL-адрес запроса и нажмите кнопку **Отправить**.
 
    ```HTTP
    https://atlas.microsoft.com/search/fuzzy/json?subscription-key={subscription-key}&api-version=1.0&query=space needle
    ```
    
3. Если взглянуть на ответ внимательно, он содержит несколько расположений в результатах стрелки, а также содержит сведения о координатах расположения для каждого из них в **позиции**. Скопируйте значение `lat` и `lon` из расположения для первого результата.
    
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

1. В окне POST щелкните **создать запрос** | **получить запрос** и назовите его **получить сведения о маршруте**.

2. На вкладке Построитель выберите метод **Get** HTTP, введите следующий URL-адрес запроса для КОНЕЧНОЙ точки API и нажмите кнопку **Отправить**.

    Мы зазапрашиваем открытые транзитные маршруты для шины, указав `modeType` параметры `transitType` и. URL-адрес запроса содержит расположения, полученные в предыдущих разделах. Так `originType` как у нас теперь есть стопид `destionationType` , и, так как у нас есть **Местоположение**.

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

4. Если вы следите за осторожностью, в ответе есть несколько маршрутов **шины** . Каждый маршрут имеет уникальный **идентификатор** и сводку, описывающую каждую часть маршрута. Далее мы будем запрашивать сведения о быстром маршруте `itineraryId` , используя в ответе.

## <a name="request-fastest-route-itinerary"></a>Запрашивать самый быстрый маршрут маршрутов

Служба Azure Maps [получить путевые](https://aka.ms/AzureMapsMobilityTransitItinerary) маршруты позволяет запрашивать данные для конкретного маршрута, используя **идентификатор** маршрута, возвращенный службой [API получения транзитных маршрутов](https://aka.ms/AzureMapsMobilityTransitRoute) . Чтобы выполнить запрос, выполните следующие действия:

1. В окне POST щелкните **создать запрос** | **получить запрос** и назовите его **получить сведения о транзитном транспорте**.

2. На вкладке Построитель выберите метод **Get** HTTP, введите следующий URL-адрес запроса для КОНЕЧНОЙ точки API и нажмите кнопку **Отправить**.

    Мы будем присвоить `detailType` параметру значение **Geometry** , чтобы ответ содержал сведения об ошибке для общедоступного транзита и последующего включения навигации для прохода и нахождения велосипеда маршрута.

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

## <a name="next-steps"></a>Следующие шаги

Узнайте, как запрашивать данные в режиме реального времени с помощью службы Mobility Service:

> [!div class="nextstepaction"]
> [Как запросить данные в режиме реального времени](how-to-request-real-time-data.md)

Изучите документацию по API службы Mobility Service Azure Maps

> [!div class="nextstepaction"]
> [Документация по API службы Mobility Service](https://aka.ms/AzureMapsMobilityService)

