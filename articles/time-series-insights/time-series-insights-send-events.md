---
title: Отправка событий в среду - Исследования серии времени Azure Документы Майкрософт
description: Узнайте, как настроить концентратор событий, запустить пример приложения и отправить события в среду Исследования времени Azure.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/11/2020
ms.custom: seodec18
ms.openlocfilehash: c3c7f59ecb3a06d80012917e2da4425a899859d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254252"
---
# <a name="send-events-to-a-time-series-insights-environment-by-using-an-event-hub"></a>Отправка событий в среду службы "Аналитика временных рядов" с помощью концентратора событий

В этой статье объясняется, как создать и настроить концентратор событий в концентрах событий Azure. В нем также описывается, как запустить пример приложения для продвижения событий в Azure Time Series Insights из концентраторов событий. Если у вас есть существующий концентратор событий с событиями в формате JSON, пропустите этот учебник и просмотрите свою среду в [Azure Time Series Insights.](./time-series-insights-update-create-environment.md)

## <a name="configure-an-event-hub"></a>Настройка концентратора событий

1. Чтобы узнать, как создать концентратор событий, прочитайте [документацию Event Hubs.](https://docs.microsoft.com/azure/event-hubs/)
1. В поле поиска введите **Центры событий**. В возвращенном списке выберите **Центры событий**.
1. Выберите свой концентратор событий.
1. При создании концентратора событий создается пространство имен концентратора событий. Если вы еще не создали концентратор событий в пространстве имен, в меню под **образований**создайте концентратор событий.  

    [![Список концентратор событий](media/send-events/tsi-connect-event-hub-namespace.png)](media/send-events/tsi-connect-event-hub-namespace.png#lightbox)

1. После создания концентратора событий выберите его в списке.
1. В меню, под **образованиями,** выберите **концентраторы событий.**
1. Выберите имя концентратора событий, чтобы настроить его.
1. Под **обзором**, выберите **группы потребителей**, а затем выберите **Группу потребителей.**

    [![Создание группы потребителей](media/send-events/add-event-hub-consumer-group.png)](media/send-events/add-event-hub-consumer-group.png#lightbox)

1. Убедитесь, что вы создаете группу потребителей, которая используется исключительно вашим источником событий Time Series Insights.

    > [!IMPORTANT]
    > Убедитесь, что эта группа потребителей не используется какой-либо другой службой, например заработком Azure Stream Analytics или другой средой Time Series Insights. Если группа объектов-получателей используется другими службами, это негативно скажется на операции чтения в этой среде и в других службах. Использование **$Default** в качестве группы объектов-получателей может потенциально привести к ее повторному использованию другими читателями.

1. В меню, в **настройках,** выберите **общие политики доступа,** а затем выберите **Добавить.**

    [![Выбор элемента "Политики общего доступа" и нажатие кнопки "Добавить"](media/send-events/add-shared-access-policy.png)](media/send-events/add-shared-access-policy.png#lightbox)

1. На панели **Добавить новую политику общего доступа** создайте политику общего доступа с именем **MySendPolicy**. Вы используете эту общую политику доступа для отправки событий в примерах C's позже в этой статье.

    [![Ввод MySendPolicy в поле имени политики](media/send-events/configure-shared-access-policy-confirm.png)](media/send-events/configure-shared-access-policy-confirm.png#lightbox)

1. Под **претензией**выберите флажок **Отправить.**

## <a name="add-a-time-series-insights-instance"></a>Добавление экземпляра службы "Аналитика временных рядов"

При обновлении службы "Аналитика временных рядов" используются экземпляры для добавления контекстных данных ко входящим данным телеметрии. Данные объединяются во время выполнения запроса на основе **идентификатора временного ряда**. **Идентификатор Time Series** для проекта образца ветряных мельниц, который мы используем позже в этой статье, является `id`. Чтобы узнать больше о экземплярах Time Series Insight и **ID Time Series,** прочитайте [модели временных рядов.](./time-series-insights-update-tsm.md)

### <a name="create-a-time-series-insights-event-source"></a>Создание источника событий службы "Аналитика временных рядов"

1. Если вы еще не создали источник событий, создайте его, [выполнив соответствующие шаги](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub).

1. Задайте значение для `timeSeriesId`. Чтобы узнать больше о **Time Series ID,** прочитайте [модели временных рядов](./time-series-insights-update-tsm.md).

### <a name="push-events-to-windmills-sample"></a>Нажмите события на образец ветряных мельниц

1. В поле поиска введите **Центры событий**. В возвращенном списке выберите **Центры событий**.

1. Выберите экземпляр концентратора событий.

1. Перейти к **общим политикам** > доступа**MySendPolicy**. Копирование значения для **основного ключа строки подключения.**

    [![Копирование значения строки подключения первичного ключа](media/send-events/configure-sample-code-connection-string.png)](media/send-events/configure-sample-code-connection-string.png#lightbox)

1. Перейдите к https://tsiclientsample.azurewebsites.net/windFarmGen.html. URL создает и запускает смоделированные устройства ветряной мельницы.
1. В поле **Event Hub Connection String** на веб-странице вставьте строку соединения, которую вы скопировали в поле [ввода ветряной мельницы.](#push-events-to-windmills-sample)
  
    [![Вставка строки подключения первичного ключа в поле Event Hub Connection String (Строка подключения концентратора событий)](media/send-events/configure-wind-mill-sim.png)](media/send-events/configure-wind-mill-sim.png#lightbox)

1. Выберите **Click to start** (Запустить). 

    > [!TIP]
    > Тренажер ветряная мельница также создает JSON вы можете использовать в качестве полезной нагрузки с [Time Series Исследования GA Запрос AIS](https://docs.microsoft.com/rest/api/time-series-insights/ga-query).

    > [!NOTE]
    > Симулятор будет продолжать отправлять данные до тех пор, пока вкладка браузера не будет закрыта.

1. Перейдите к концентратору событий на портале Azure. На странице **«Обзор»** отображаются новые события, полученные концентратором событий.

    [![Страница обзора концентратора событий, отображающая метрики для концентратора событий](media/send-events/review-windmill-telemetry.png)](media/send-events/review-windmill-telemetry.png#lightbox)

## <a name="supported-json-shapes"></a>Поддерживаемые формы JSON

### <a name="example-one"></a>Пример один

* **Вход**: Простой объект JSON.

    ```JSON
    {
        "id":"device1",
        "timestamp":"2016-01-08T01:08:00Z"
    }
    ```

* **Выход**: Одно событие.

    |идентификатор|TIMESTAMP|
    |--------|---------------|
    |device1|2016-01-08T01:08:00Z|

### <a name="example-two"></a>Пример второй

* **Вход**: Массив JSON с двумя объектами JSON. Каждый объект JSON преобразуется в событие.

    ```JSON
    [
        {
            "id":"device1",
            "timestamp":"2016-01-08T01:08:00Z"
        },
        {
            "id":"device2",
            "timestamp":"2016-01-17T01:17:00Z"
        }
    ]
    ```

* **Выход**: Два события.

    |идентификатор|TIMESTAMP|
    |--------|---------------|
    |device1|2016-01-08T01:08:00Z|
    |device2|2016-01-08T01:17:00Z|

### <a name="example-three"></a>Пример третий

* **Вход**: Объект JSON с вложенным массивом JSON, содержащим два объекта JSON.

    ```JSON
    {
        "location":"WestUs",
        "events":[
            {
                "id":"device1",
                "timestamp":"2016-01-08T01:08:00Z"
            },
            {
                "id":"device2",
                "timestamp":"2016-01-17T01:17:00Z"
            }
        ]
    }
    ```

* **Выход**: Два события. Свойство **location** копируется для каждого события.

    |location|events.id|events.timestamp|
    |--------|---------------|----------------------|
    |WestUs|device1|2016-01-08T01:08:00Z|
    |WestUs|device2|2016-01-08T01:17:00Z|

### <a name="example-four"></a>Пример четвертый

* **Вход**: Объект JSON с вложенным массивом JSON, содержащим два объекта JSON. Эти входные данные указывают на то, что глобальные свойства могут быть представлены сложным объектом JSON.

    ```JSON
    {
        "location":"WestUs",
        "manufacturer":{
            "name":"manufacturer1",
            "location":"EastUs"
        },
        "events":[
            {
                "id":"device1",
                "timestamp":"2016-01-08T01:08:00Z",
                "data":{
                    "type":"pressure",
                    "units":"psi",
                    "value":108.09
                }
            },
            {
                "id":"device2",
                "timestamp":"2016-01-17T01:17:00Z",
                "data":{
                    "type":"vibration",
                    "units":"abs G",
                    "value":217.09
                }
            }
        ]
    }
    ```

* **Выход**: Два события.

    |location|manufacturer.name|manufacturer.location|events.id|events.timestamp|events.data.type|events.data.units|events.data.value|
    |---|---|---|---|---|---|---|---|
    |WestUs|manufacturer1|EastUs|device1|2016-01-08T01:08:00Z|pressure|psi|108.09|
    |WestUs|manufacturer1|EastUs|device2|2016-01-08T01:17:00Z|vibration|abs G|217.09|

## <a name="next-steps"></a>Дальнейшие действия

- [Просматривайте свою среду](https://insights.timeseries.azure.com) в Explorer Time Series Insights.

- Подробнее о [сообщениях устройств IoT hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct)
