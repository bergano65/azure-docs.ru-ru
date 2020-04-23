---
title: Запрос данных общественного транспорта в режиме реального времени Карты Microsoft Azure
description: Запрашивайте данные общественного транспорта в режиме реального времени с помощью службы мобильности Microsoft Azure Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 4743fbe84f5d41b4659e13d96868d2f64a473e4b
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086083"
---
# <a name="request-real-time-public-transit-data-using-the-azure-maps-mobility-service"></a>Запрашивайте данные общественного транспорта в режиме реального времени с помощью службы мобильности Azure Maps

В этой статье показано, как использовать [службу мобильности](https://aka.ms/AzureMapsMobilityService) Azure Maps для запроса данных общественного транспорта в режиме реального времени.

В этой статье вы узнаете, как запрашивать следующие прибытия в режиме реального времени для всех линий, прибывающих на заданную остановку

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

## <a name="next-steps"></a>Дальнейшие действия

Узнайте, как запрашивать транзитные данные с помощью службы мобильности:

> [!div class="nextstepaction"]
> [Как запросить транзитные данные](how-to-request-transit-data.md)

Изучите документацию API службы мобильности Azure Maps:

> [!div class="nextstepaction"]
> [Документация API службы мобильности](https://aka.ms/AzureMapsMobilityService)
