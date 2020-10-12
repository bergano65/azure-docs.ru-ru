---
title: Запрос общедоступных транзитных данных в реальном времени | Карты Microsoft Azure
description: Узнайте, как запрашивать общедоступные транзитные данные в режиме реального времени, например прибытия на транзитном пути. Сведения об использовании службы мобильности Azure Maps для этой цели.
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/06/2019
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 6f0cf663b42c8487495602e4cdbf1a88427f9daf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91310940"
---
# <a name="request-real-time-public-transit-data-using-the-azure-maps-mobility-service"></a>Запрос общедоступных транзитных данных в реальном времени с помощью службы Azure Maps Mobility Service

В этой статье показано, как использовать [службу Azure Maps Mobility Service](https://aka.ms/AzureMapsMobilityService) для запроса общедоступных транзитных данных в режиме реального времени.

В этой статье вы узнаете, как запросить следующие поступления в реальном времени для всех строк, поступающих на заданную точку.

## <a name="prerequisites"></a>Предварительные требования

Сначала необходимо иметь учетную запись Azure Maps и ключ подписки, чтобы выполнять вызовы Azure Maps общедоступных транзитных API. Для получения сведений следуйте инструкциям в разделе [Создание учетной записи](quick-demo-map-app.md#create-an-azure-maps-account) для создания учетной записи Azure Maps. Выполните действия, описанные в разделе [Получение первичного ключа](quick-demo-map-app.md#get-the-primary-key-for-your-account) , чтобы получить первичный ключ для вашей учетной записи. Дополнительные сведения о проверке подлинности в Azure Maps см. в [этой статье](./how-to-manage-authentication.md).

В этой статье для создания вызовов REST используется [приложение Postman](https://www.getpostman.com/apps). Вы можете использовать любую среду разработки API.

## <a name="request-real-time-arrivals-for-a-stop"></a>Запрос прибытия в режиме реального времени для завершения

Чтобы запросить данные о поступлении конкретного открытого транзитного пути в режиме реального времени, необходимо выполнить запрос к [API прибытия в реальном времени](https://aka.ms/AzureMapsMobilityRealTimeArrivals) для [службы Azure Maps Mobility Service](https://aka.ms/AzureMapsMobilityService). Для выполнения запроса потребуются **Метроид** и **стопид** . Чтобы узнать больше о том, как запросить эти параметры, ознакомьтесь с нашим руководством по [запросу общих транзитных маршрутов](https://aka.ms/AMapsHowToGuidePublicTransitRouting).

Давайте будем использовать "522" в качестве идентификатора Metro, который является ИДЕНТИФИКАТОРом Metro для области "Сиэтл – Tacoma – Бельвью, WA". В качестве идентификатора завершения используйте "522---2060603". Эта шина останавливается в "NE 24 St & 162nd Ave NE, Бельвью WA". Чтобы запросить следующие пять данных о поступлении в реальном времени, для следующих прямых появления при этой ошибке выполните следующие действия.

1. Откройте приложение POST и создадим коллекцию для хранения запросов. В верхней части приложения Postman выберите элемент **Создать**. В окне **Create New** (Создание) выберите **Collection** (Коллекция).  Присвойте имя коллекции и нажмите кнопку **Создать**.

2. Чтобы создать запрос, нажмите кнопку **Создать** еще раз. В окне **Create New** (Создание) выберите **Request** (Запрос). Введите **Имя запроса** для запроса. Выберите коллекцию, созданную на предыдущем шаге, в качестве расположения для сохранения запроса. Затем нажмите кнопку **Сохранить**.

    ![Создание запроса в POST](./media/how-to-request-transit-data/postman-new.png)

3. Выберите метод **получения** HTTP на вкладке Построитель и введите следующий URL-адрес для создания запроса GET. Замените на `{subscription-key}` Azure Maps первичный ключ.

    ```HTTP
    https://atlas.microsoft.com/mobility/realtime/arrivals/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&query=522---2060603&transitType=bus
    ```

4. После успешного выполнения запроса вы получите следующий ответ.  Обратите внимание, что параметр "scheduleType" определяет, основывается ли предполагаемое время прибытия на основе данных в режиме реального времени или статических.

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

## <a name="next-steps"></a>Дальнейшие шаги

Узнайте, как запросить транзитные данные с помощью службы Mobility Service:

> [!div class="nextstepaction"]
> [Запрос передачи данных](how-to-request-transit-data.md)

Изучите документацию по API службы Mobility Service Azure Maps.

> [!div class="nextstepaction"]
> [Документация по API службы Mobility Service](https://aka.ms/AzureMapsMobilityService)
