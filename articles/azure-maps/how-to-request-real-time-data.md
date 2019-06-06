---
title: Как запросить данные в реальном времени в "карты Azure" | Документация Майкрософт
description: Запрос данных в реальном времени с помощью службы Azure Maps Mobility service.
author: walsehgal
ms.author: v-musehg
ms.date: 06/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 4d8d34d8dec52dd9173cea80c39097f46d32bff5
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/06/2019
ms.locfileid: "66735486"
---
# <a name="request-real-time-data-using-the-azure-maps-mobility-service"></a>Запрос данных в реальном времени с помощью службы Azure Maps Mobility Service

В этой статье показано, как использовать карты Azure [службы Mobility Service](https://aka.ms/AzureMapsMobilityService) для данных, передаваемых в режиме реального времени запроса.

В этой статье вы узнаете, как:


 * Запрос далее поступившие запросы в режиме реального времени, для всех строк, поступающих в заданной stop
 * Запрос в режиме реального времени сведения для заданного bike стыковочного узла.


## <a name="prerequisites"></a>Технические условия

Чтобы сделать все вызовы службы карт Azure общественного API-интерфейсов, необходимо Maps учетной записи и ключ. Дополнительные сведения о создании учетной записи и получении ключа см. в статье об [управлении ключами и учетной записью службы "Карты Azure"](how-to-manage-account-keys.md).

В этой статье для создания вызовов REST используется [приложение Postman](https://www.getpostman.com/apps). Вы можете использовать любую среду разработки API.


## <a name="request-real-time-arrivals-for-a-stop"></a>В режиме реального времени поступления запросов для остановки

Чтобы запросить данные в режиме реального времени прибытия для остановки определенного общественного, нужно будет сделать запрос на [API в реальном времени прибытия](https://aka.ms/AzureMapsMobilityRealTimeArrivals) карт Azure [службы Mobility Service](https://aka.ms/AzureMapsMobilityService). Необходимо будет **metroID** и **stopID** для выполнения запроса. Дополнительные сведения о том, как эти параметры запроса, см. в разделе наших инструкций руководство по [запроса маршрутов общественного](https://aka.ms/AMapsHowToGuidePublicTransitRouting). 

«522» в качестве используем наши metro идентификатор, который представляет metro, идентификатор для области «Сиэтл – Tacoma – Бельвью, штат Вашингтон», а идентификатор остановки «2060603», который является шины остановиться на «Ne 24-й St с & охранить 162nd Ne, Bellevue WA». Чтобы запросить следующих пяти данных поступившие запросы в режиме реального времени для всех Далее динамической прибытий на этом stop, выполните следующие действия:

1. Создайте коллекцию, в которой будут храниться запросы. В приложении Postman, выберите **New**. В **Create New** выберите **коллекции**. Имя коллекции и выберите **создать** кнопки.

2. Чтобы создать запрос, выберите **New** еще раз. В **Create New** выберите **запроса**. Введите **имя запроса** для запроса, выберите коллекцию, созданную на предыдущем шаге, как расположение, в котором следует сохранить запрос, а затем выберите **Сохранить**.

    ![Создайте запрос в Postman](./media/how-to-request-transit-data/postman-new.png)

3. Выберите метод GET HTTP на вкладку «Конструктор» и введите следующий URL-адрес, чтобы создать запрос GET.

    ```HTTP
    https://atlas.microsoft.com/mobility/realtime/arrivals/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&query=2060603&transitType=bus
    ```

4. После успешного запроса вы получите следующий ответ.  Обратите внимание, что этот параметр «scheduleType» определяет ли предполагаемое временем основывается на данных в режиме реального времени или статический.

    ```JSON
    {
        "results": [
            {
                "arrivalMinutes": 4,
                "scheduleType": "realTime",
                "patternId": 3860436,
                "line": {
                    "lineId": 2756599,
                    "lineGroupId": 666063,
                    "direction": "forward",
                    "agencyId": 5872,
                    "agencyName": "Metro Transit",
                    "lineNumber": "226",
                    "lineDestination": "Bellevue Transit Center Crossroads",
                    "transitType": "Bus"
                },
                "stop": {
                    "stopId": 2060603,
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "position": {
                        "latitude": 47.631504,
                        "longitude": -122.125275
                    },
                    "mainTransitType": "Bus",
                    "mainAgencyId": 5872,
                    "mainAgencyName": "Metro Transit"
                }
            },
            {
                "arrivalMinutes": 30,
                "scheduleType": "scheduledTime",
                "patternId": 3860436,
                "line": {
                    "lineId": 2756599,
                    "lineGroupId": 666063,
                    "direction": "forward",
                    "agencyId": 5872,
                    "agencyName": "Metro Transit",
                    "lineNumber": "226",
                    "lineDestination": "Bellevue Transit Center Crossroads",
                    "transitType": "Bus"
                },
                "stop": {
                    "stopId": 2060603,
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "position": {
                        "latitude": 47.631504,
                        "longitude": -122.125275
                    },
                    "mainTransitType": "Bus",
                    "mainAgencyId": 5872,
                    "mainAgencyName": "Metro Transit"
                }
            }
        ]
    }


## Real-time availability and vacancy information for bike docking station

The [Get Transit Dock Info API](https://aka.ms/AzureMapsMobilityTransitDock) of the Azure Maps Mobility Service, allows to request static and real-time information for a given bike or scooter docking station. We will make a request to get real-time data for a docking station for bikes. 

In order to make a request to the Get Transit Dock Info API, you will need the **dockId** for that station. You can get the dock ID by making a search request to the [Get Nearby Transit API](https://aka.ms/AzureMapsMobilityNearbyTransit) and setting the **objectType** parameter to "bikeDock". Follow the steps below to get real-time data of a docking station for bikes.


### Get dock ID

To get **dockID**, follow the steps below to make a request to the Get Nearby Transit API:

1. In Postman, click **New Request** | **GET request** and name it **Get dock ID**.

2.  On the Builder tab, select the **GET** HTTP method, enter the following request URL, and click **Send**.
 
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/nearby/json?subscription-key={subscription-key}&api-version=1.0&metroId=121&query=40.7663753,-73.9627498&radius=100&objectType=bikeDock
    ```

3. После успешного запроса вы получите следующий ответ. Обратите внимание, что теперь у нас есть **идентификатор** в ответе, который может использоваться позже как параметр запроса в запросе к API получения сведений о закрепления пути.

    ```JSON
    {
        "results": [
            {
                "id": "121---4640799",
                "type": "bikeDock",
                "objectDetails": {
                    "availableVehicles": 0,
                    "vacantLocations": 30,
                    "lastUpdated": "2019-05-21T20:06:59-04:00",
                    "operatorInfo": {
                        "id": "80",
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


### <a name="get-real-time-bike-dock-status"></a>Получение состояния закрепления велосипеда в реальном времени

Выполните следующие действия, чтобы выполнить запрос к передаче закрепить Info API получения для получения данных в реальном времени для выбранной закрепления.

1. В Postman щелкните **новый запрос** | **запроса GET** и назовите его **получение данных в режиме реального времени закрепления**.

2.  На вкладке Builder, выберите **получить** метод HTTP, введите следующий URL-адрес запроса и нажмите кнопку **отправки**.
 
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/dock/json?subscription-key={subscription-key}&api-version=1.0&query=121---4640799
    ```

3. После успешного запроса вы получите ответ со следующей структурой:

    ```JSON
    {
        "availableVehicles": 1,
        "vacantLocations": 29,
        "position": {
            "latitude": 40.767128,
            "longitude": -73.962246
        },
        "lastUpdated": "2019-05-21T20:26:47-04:00",
        "operatorInfo": {
            "id": "80",
            "name": "Citi Bike"
        }
    }
    ```


## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о запросе данных, передаваемых с помощью службы Mobility Service:

> [!div class="nextstepaction"]
> [Для запроса данных, передаваемых](how-to-request-transit-data.md)

Изучите документацию по API службы Azure Maps мобильности:

> [!div class="nextstepaction"]
> [Документация по API службы мобильности](https://aka.ms/AzureMapsMobilityService)
