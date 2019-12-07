---
title: Отправка событий в среду — служба "аналитика временных рядов Azure" | Документация Майкрософт
description: Узнайте, как настроить концентратор событий, запустить пример приложения и отправить события в среду службы "аналитика временных рядов Azure".
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 12/05/2019
ms.custom: seodec18
ms.openlocfilehash: 1dfd9a8d2723136ef68d983eb99bf2391fb87879
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/06/2019
ms.locfileid: "74894900"
---
# <a name="send-events-to-a-time-series-insights-environment-by-using-an-event-hub"></a>Отправка событий в среду службы "Аналитика временных рядов" с помощью концентратора событий

В этой статье объясняется, как создать и настроить концентратор событий в концентраторах событий Azure. Здесь также описывается, как запустить пример приложения для отправки событий в службу "аналитика временных рядов Azure" из концентраторов событий. Если у вас есть концентратор событий с событиями в формате JSON, пропустите этот учебник и просмотрите свою среду в [службе "аналитика временных рядов Azure](./time-series-insights-update-create-environment.md)".

## <a name="configure-an-event-hub"></a>Настройка концентратора событий

1. Чтобы создать концентратор событий, следуйте инструкциям из [документации по Центрам событий](https://docs.microsoft.com/azure/event-hubs/).
1. В поле поиска введите **Центры событий**. В возвращенном списке выберите **Центры событий**.
1. Выберите свой концентратор событий.
1. При создании концентратора событий создается пространство имен концентратора событий. Если вы еще не создали концентратор событий в пространстве имен, в меню в разделе **сущности**создайте концентратор событий.  

    [![списка концентраторов событий](media/send-events/tsi-connect-event-hub-namespace.png)](media/send-events/tsi-connect-event-hub-namespace.png#lightbox)

1. После создания концентратора событий выберите его в списке.
1. В меню **сущности**выберите **концентраторы событий**.
1. Выберите имя концентратора событий, чтобы настроить его.
1. В разделе **Обзор**выберите **группы потребителей**, а затем выберите **Группа потребителей**.

    [![создания группы потребителей](media/send-events/add-event-hub-consumer-group.png)](media/send-events/add-event-hub-consumer-group.png#lightbox)

1. Убедитесь, что создана группа потребителей, которая используется исключительно источником событий "аналитика временных рядов".

    > [!IMPORTANT]
    > Убедитесь, что эта группа потребителей не используется какой-либо другой службой, например Azure Stream Analytics заданием или другой средой "аналитика временных рядов". Если группа объектов-получателей используется другими службами, это негативно скажется на операции чтения в этой среде и в других службах. Использование **$Default** в качестве группы объектов-получателей может потенциально привести к ее повторному использованию другими читателями.

1. В меню в разделе **Параметры**выберите **политики общего доступа**, а затем нажмите кнопку **добавить**.

    [![выберите политики общего доступа, а затем нажмите кнопку Добавить.](media/send-events/add-shared-access-policy.png)](media/send-events/add-shared-access-policy.png#lightbox)

1. На панели **Добавить новую политику общего доступа** создайте политику общего доступа с именем **MySendPolicy**. Эта политика общего доступа используется для отправки событий в C# примерах далее в этой статье.

    [![в поле Имя политики введите MySendPolicy.](media/send-events/configure-shared-access-policy-confirm.png)](media/send-events/configure-shared-access-policy-confirm.png#lightbox)

1. В разделе **утверждение**установите флажок **Отправить** .

## <a name="add-a-time-series-insights-instance"></a>Добавление экземпляра службы "Аналитика временных рядов"

При обновлении службы "Аналитика временных рядов" используются экземпляры для добавления контекстных данных ко входящим данным телеметрии. Данные объединяются во время выполнения запроса на основе **идентификатора временного ряда**. **Идентификатор временных рядов** для примера проекта Виндмиллс, который мы используем далее в этой статье, `id`. Дополнительные сведения об экземплярах Time Series Insights и **идентификаторах временных рядов**см. в статье [модели временных рядов](./time-series-insights-update-tsm.md).

### <a name="create-a-time-series-insights-event-source"></a>Создание источника событий службы "Аналитика временных рядов"

1. Если вы еще не создали источник событий, создайте его, [выполнив соответствующие шаги](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub).

1. Задайте значение для `timeSeriesId`. Дополнительные сведения об **идентификаторе временного ряда** см. в статье [Time Series Model](./time-series-insights-update-tsm.md) (Модель временных рядов).

### <a name="push-events-to-windmills-sample"></a>Пример принудительной отправки событий в Виндмиллс

1. В поле поиска введите **Центры событий**. В возвращенном списке выберите **Центры событий**.

1. Выберите экземпляр концентратора событий.

1. Перейдите к **политикам общего доступа** > **MySendPolicy**. Скопируйте значение для параметра **строка подключения — первичный ключ**.

    [![скопировать значение для строки подключения по первичному ключу](media/send-events/configure-sample-code-connection-string.png)](media/send-events/configure-sample-code-connection-string.png#lightbox)

1. Перейдите на сайт https://tsiclientsample.azurewebsites.net/windFarmGen.html. После перехода по этому URL-адресу будут запущены имитированные устройства ветряных мельниц.
1. В поле **строка подключения концентратора событий** на веб-странице вставьте строку подключения, скопированную в [поле ввода Виндмилл](#push-events-to-windmills-sample).
  
    [![вставить строку подключения первичного ключа в поле Строка подключения к концентратору событий](media/send-events/configure-wind-mill-sim.png)](media/send-events/configure-wind-mill-sim.png#lightbox)

1. Выберите **Click to start** (Запустить). Симулятор создаст JSON-файл экземпляра, который вы можете использовать напрямую.

1. Перейдите к концентратору событий на портале Azure. На странице **Обзор** отображаются новые события, получаемые концентратором событий.

    [![страницы обзора концентратора событий, в которой отображаются метрики для концентратора событий.](media/send-events/review-windmill-telemetry.png)](media/send-events/review-windmill-telemetry.png#lightbox)

## <a name="supported-json-shapes"></a>Поддерживаемые формы JSON

### <a name="example-one"></a>Пример 1

* **Входные данные**: простой объект JSON.

    ```JSON
    {
        "id":"device1",
        "timestamp":"2016-01-08T01:08:00Z"
    }
    ```

* **Выходные данные**: одно событие.

    |id|Timestamp|
    |--------|---------------|
    |device1|2016-01-08T01:08:00Z|

### <a name="example-two"></a>Пример 2

* **Входные данные**: массив JSON с двумя объектами JSON. Каждый объект JSON преобразуется в событие.

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

* **Выходные данные**: два события.

    |id|Timestamp|
    |--------|---------------|
    |device1|2016-01-08T01:08:00Z|
    |device2|2016-01-08T01:17:00Z|

### <a name="example-three"></a>Пример 3

* **Входные данные**: объект JSON с вложенным массивом JSON, который содержит два объекта JSON.

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

* **Выходные данные**: два события. Свойство **location** копируется для каждого события.

    |location|events.id|events.timestamp|
    |--------|---------------|----------------------|
    |WestUs|device1|2016-01-08T01:08:00Z|
    |WestUs|device2|2016-01-08T01:17:00Z|

### <a name="example-four"></a>Пример 4

* **Входные данные**: объект JSON с вложенным массивом JSON, который содержит два объекта JSON. Эти входные данные указывают на то, что глобальные свойства могут быть представлены сложным объектом JSON.

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

* **Выходные данные**: два события.

    |location|manufacturer.name|manufacturer.location|events.id|events.timestamp|events.data.type|events.data.units|events.data.value|
    |---|---|---|---|---|---|---|---|
    |WestUs|manufacturer1|EastUs|device1|2016-01-08T01:08:00Z|pressure|psi|108.09|
    |WestUs|manufacturer1|EastUs|device2|2016-01-08T01:17:00Z|vibration|abs G|217.09|

## <a name="next-steps"></a>Дальнейшие действия

- [Просмотр среды](https://insights.timeseries.azure.com) в обозревателе "аналитика временных рядов".

- Дополнительные сведения об [сообщениях устройств центра Интернета вещей](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct)
