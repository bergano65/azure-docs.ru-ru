---
title: Запрос транзитных данных в режиме реального времени Карты Microsoft Azure
description: Запрашивайте данные в режиме реального времени с помощью службы мобильности Карт Microsoft Azure.
author: philmea
ms.author: philmea
ms.date: 09/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: d3145181a863bf8188dd0b0bb52cd2efc662ce2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335483"
---
# <a name="request-real-time-data-using-the-azure-maps-mobility-service"></a>Запрос данных в режиме реального времени с помощью службы мобильности Azure Maps

В этой статье показано, как использовать [службу мобильности](https://aka.ms/AzureMapsMobilityService) Azure Maps для запроса транзитных данных в режиме реального времени.

Из этой статьи вы узнаете о следующем.


 * Запрос следующего прибытия в режиме реального времени для всех линий, прибывающих на заданную остановку
 * Запросить информацию в режиме реального времени для данной стыковочного стыковки велосипедов станции.


## <a name="prerequisites"></a>Предварительные требования

Сначала необходимо иметь учетную запись Azure Maps и ключ подписки для любых звонков в APIs общественного транспорта Azure Maps. Для получения информации следуйте инструкциям в [создании учетной записи](quick-demo-map-app.md#create-an-account-with-azure-maps) для создания учетной записи Azure Maps. Выполните шаги в [получить основной ключ,](quick-demo-map-app.md#get-the-primary-key-for-your-account) чтобы получить основной ключ для вашей учетной записи. Дополнительные сведения о проверке подлинности в Azure Maps см. в [этой статье](./how-to-manage-authentication.md).


В этой статье для создания вызовов REST используется [приложение Postman](https://www.getpostman.com/apps). Вы можете использовать любую среду разработки API.


## <a name="request-real-time-arrivals-for-a-stop"></a>Запрос прибытия в режиме реального времени для остановки

Для того, чтобы запросить данные о прибытии в режиме реального времени о конкретной остановке общественного транспорта, необходимо сделать запрос в [API прибытия в режиме реального времени](https://aka.ms/AzureMapsMobilityRealTimeArrivals) [службы мобильности](https://aka.ms/AzureMapsMobilityService)Azure Maps. Для завершения запроса вам понадобится **metroID** и **stopID.** Чтобы узнать больше о том, как запросить эти параметры, смотрите наше руководство о том, как [запросить маршруты общественного транспорта](https://aka.ms/AMapsHowToGuidePublicTransitRouting). 

Давайте использовать "522" в качестве нашего метро ID, который является метро ID для "Сиэтл-Такома-Bellevue, штат Вашингтон" области. Используйте "522---2060603" в качестве стоп-идентификатора, эта автобусная остановка находится на "Ne 24-й Санкт-& 162nd Ave Ne, Bellevue WA". Чтобы запросить следующие пять данных о прибытии в реальном времени, для всех следующих живых прибытий на этой остановке, выполните следующие шаги:

1. Откройте приложение Почтальон и создадим коллекцию для хранения запросов. В верхней части приложения Почтальон, выберите **новый**. В **окне «Создайте новое»** выберите **Collection**.  Назовите коллекцию и выберите кнопку **«Создание».**

2. Чтобы создать запрос, выберите **Новый** снова. В окне **«Создать новое»** выберите **Запрос**. Введите **имя запроса.** Выберите созданную на предыдущем этапе коллекцию в качестве места, в котором можно сохранить запрос. Затем выберите **Сохранить**.

    ![Создать запрос в Postman](./media/how-to-request-transit-data/postman-new.png)

3. Выберите метод **GET** HTTP на вкладке builder и введите следующий URL для создания запроса GET. Замените `{subscription-key}`основной ключ Azure Maps.

    ```HTTP
    https://atlas.microsoft.com/mobility/realtime/arrivals/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&query=522---2060603&transitType=bus
    ```

4. После успешного запроса вы получите следующий ответ.  Обратите внимание, что параметр 'scheduleType' определяет, основано ли предполагаемое время прибытия на основе данных в реальном времени или статических данных.

    ```JSON
    {
        "results": [
            {
                "arrivalMinutes": 8,
                "scheduleType": "realTime",
                "patternId": "522---4143196",
                "line": {
                    "lineId": "522---3760143",
                    "lineGroupId": "522---666077",
                    "direction": "backward",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "249",
                    "lineDestination": "South Bellevue S Kirkland P&R",
                    "transitType": "Bus"
                },
                "stop": {
                    "stopId": "522---2060603",
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "stopCode": "71300",
                    "position": {
                        "latitude": 47.631504,
                        "longitude": -122.125275
                    },
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                }
            },
            {
                "arrivalMinutes": 25,
                "scheduleType": "realTime",
                "patternId": "522---3510227",
                "line": {
                    "lineId": "522---2756599",
                    "lineGroupId": "522---666063",
                    "direction": "forward",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "226",
                    "lineDestination": "Bellevue Transit Center Crossroads",
                    "transitType": "Bus"
                },
                "stop": {
                    "stopId": "522---2060603",
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "stopCode": "71300",
                    "position": {
                        "latitude": 47.631504,
                        "longitude": -122.125275
                    },
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                }
            }
        ]
    }
    ```


## <a name="real-time-data-for-bike-docking-station"></a>Данные в режиме реального времени для стыковочного стыковки велосипедов

[API Get Transit Dock Info](https://aka.ms/AzureMapsMobilityTransitDock) позволяет пользователям запрашивать статическую информацию и информацию в режиме реального времени. Например, пользователи могут запросить информацию о наличии и вакансиях для велосипеда или док-станции для скутеров. [API Get Transit Dock Info](https://aka.ms/AzureMapsMobilityTransitDock) также является частью [службы мобильности](https://aka.ms/AzureMapsMobilityService)Azure Maps.

Для того, чтобы сделать запрос на [Get Transit Dock Info API,](https://aka.ms/AzureMapsMobilityTransitDock)вам понадобится **dockId** для этой станции. Вы можете получить идентификатор док-станции, сделав запрос на поиск в [Get Nearby Transit API](https://aka.ms/AzureMapsMobilityNearbyTransit) с параметром **objectType,** назначенным "bikeDock". Следуйте ниже, чтобы получить данные в режиме реального времени док-станции для велосипедов.


### <a name="get-dock-id"></a>Получить идентификатор док-станции

Чтобы получить **dockID,** выполните ниже шаги, чтобы сделать запрос на Get Nearby Transit API:

1. В Postman, нажмите **Новый запрос** | **GET запрос** и назовите его Получить ID **док**.

2.  На вкладке Builder выберите метод **GET** HTTP, введите URL-адрес следующего запроса и нажмите **«Отправить».**
 
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/nearby/json?subscription-key={subscription-key}&api-version=1.0&metroId=121&query=40.7663753,-73.9627498&radius=100&objectType=bikeDock
    ```

3. После успешного запроса вы получите следующий ответ. Обратите внимание, что теперь у нас есть **идентификатор** в ответе, который может быть использован позже в качестве параметра запроса в Get Transit Dock Info API.

    ```JSON
    {
        "results": [
            {
                "id": "121---4640799",
                "type": "bikeDock",
                "objectDetails": {
                    "availableVehicles": 0,
                    "vacantLocations": 31,
                    "lastUpdated": "2019-09-07T00:55:19Z",
                    "operatorInfo": {
                        "id": "121---80",
                        "name": "Citi Bike"
                    }
                },
                "position": {
                    "latitude": 40.767128,
                    "longitude": -73.962243
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 40.768039,
                        "longitude": -73.963413
                    },
                    "btmRightPoint": {
                        "latitude": 40.766216,
                        "longitude": -73.961072
                    }
                }
            }
        ]
    }
    ```


### <a name="get-real-time-bike-dock-status"></a>Получить статус причала в режиме реального времени

Выполните ниже шаги, чтобы сделать запрос на Get Transit Dock Info API, чтобы получить данные в режиме реального времени для выбранной док-станции.

1. В Postman, нажмите **Новый запрос** | **GET запрос** и назвать его Получить в **режиме реального времени док-данных**.

2.  На вкладке Builder выберите метод **GET** HTTP, введите URL-адрес следующего запроса и нажмите **«Отправить».**
 
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/dock/json?subscription-key={subscription-key}&api-version=1.0&query=121---4640799
    ```

3. После успешного запроса вы получите ответ следующей структуры:

    ```JSON
    {
        "availableVehicles": 0,
        "vacantLocations": 31,
        "position": {
            "latitude": 40.767128,
            "longitude": -73.962246
        },
        "lastUpdated": "2019-09-07T00:55:19Z",
        "operatorInfo": {
            "id": "121---80",
            "name": "Citi Bike"
        }
    }
    ```


## <a name="next-steps"></a>Дальнейшие действия

Узнайте, как запрашивать транзитные данные с помощью службы мобильности:

> [!div class="nextstepaction"]
> [Как запросить транзитные данные](how-to-request-transit-data.md)

Изучите документацию API службы мобильности Azure Maps:

> [!div class="nextstepaction"]
> [Документация API службы мобильности](https://aka.ms/AzureMapsMobilityService)
