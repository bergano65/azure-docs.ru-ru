---
title: Анализ журналов и метрик в Azure Веснного облака | Документация Майкрософт
description: Узнайте, как анализировать диагностические данные в Azure с пружинным облаком
services: spring-cloud
author: jpconnock
manager: gwallace
editor: ''
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/06/2019
ms.author: jeconnoc
ms.openlocfilehash: e6b90fb09c536f68bee7fd5d57507fe3920bcf1e
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038890"
---
# <a name="analyze-logs-and-metrics-with-diagnostic-settings"></a>Анализ журналов и метрик с помощью параметров диагностики

Функция диагностики в Azure Веснного облака позволяет анализировать журналы и метрики с помощью одной из следующих служб:

* Проанализируйте их с помощью Azure Log Analytics, где данные немедленно записываются в Azure Log Analytics без необходимости предварительной записи данных в хранилище.
* Сохраните их в учетной записи хранения для аудита или проверки вручную. Можно указать время хранения (в днях).
* Потоковая передача в концентраторы событий для приема сторонними службами или решениями пользовательской аналитики.

Чтобы приступить к работе, необходимо включить одну из этих служб для получения данных.  Чтобы узнать о настройке Log Analytics, ознакомьтесь с [этим руководством](../azure-monitor/log-query/get-started-portal.md).  

## <a name="configure-diagnostic-settings"></a>Настройка параметров диагностики

1. Перейдите к облачному экземпляру Azure весны в портал Azure.
1. Выберите пункт меню " **параметры диагностики** ".
1. Нажмите кнопку **Добавить параметр диагностики** .
1. Введите имя для параметра и выберите, куда вы хотите отправить журналы. Можно выбрать любое сочетание следующих трех параметров.
    * Архивировать в учетной записи хранения
    * "Передать в концентратор событий";
    * Отправка в Log Analytics

1. Выберите категорию журнала и категорию метрик, которые требуется отслеживать, и укажите время хранения (в днях). Время хранения применяется только к учетной записи хранения.
1. Чтобы применить настройку, нажмите кнопку **Сохранить**.

> [!NOTE]
> При выдаче журналов или метрик и их появлении в учетной записи хранения, концентраторе событий или Log Analytics может возникать до 15 минут.

## <a name="viewing-logs"></a>Просмотр журналов

### <a name="using-log-analytics"></a>Использование Log Analytics

1. В портал Azure выберите Log Analytics в меню навигации слева.
1. Выберите рабочую область Log Analytics, которую вы выбрали при добавлении параметров диагностики.
1. Выберите `Logs`, чтобы открыть колонку поиска по журналам.
1. Введите простой запрос в поле поиска по журналам.  Пример:

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```

1. Выберите `Run`, чтобы просмотреть результаты поиска.
1. Можно выполнить поиск в журналах конкретного приложения или экземпляра, задав условие фильтра:

    ```sql
    AppPlatformLogsforSpring
    | where ServiceName == "YourServiceName" and AppName == "YourAppName" and InstanceName == "YourInstanceName"
    | limit 50
    ```

Дополнительные сведения о языке запросов, используемом в Log Analytics [в этой статье](../azure-monitor/log-query/query-language.md)

### <a name="using-logs-and-metrics-in-storage-account"></a>Использование журналов и метрик в учетной записи хранения

1. В портал Azure выберите учетные записи хранения в меню навигации слева.
1. Выберите учетную запись хранения, выбранную при добавлении параметров диагностики.
1. Выберите запись `Blobs`, чтобы открыть колонку контейнера больших двоичных объектов.
1. Найдите контейнер с именем `insights-logs-applicationconsole` для просмотра журналов приложений.
1. Найдите контейнер с именем `insights-metrics-pt1m`, чтобы просмотреть метрики приложения.

[Дополнительные сведения об отправке диагностических данных в учетную запись хранения.](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-to-storage)

### <a name="using-event-hubs"></a>Использование концентраторов событий

1. В портал Azure выберите концентраторы событий в меню навигации слева.
1. Выберите концентраторы событий, выбранные при добавлении параметров диагностики.
1. Выберите `Event Hubs`, чтобы открыть колонку "список концентраторов событий".
1. Найдите концентратор событий с именем `insights-logs-applicationconsole` для просмотра журналов приложений.
1. Найдите концентратор событий с именем `insights-metrics-pt1m`, чтобы просмотреть метрики приложения.

Дополнительные сведения [об отправке диагностических данных в концентратор событий.](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-stream-event-hubs)

## <a name="analyzing-logs"></a>анализ журналов;

Azure Log Analytics предоставляет Kusto, чтобы вы могли запрашивать журналы для анализа.  Краткие сведения о запросах журналов с помощью Kusto см. в [руководстве по log Analytics](../azure-monitor/log-query/get-started-portal.md) .

Журналы приложений предоставляют важную информацию о работоспособности, производительности и других приложениях приложения.  Ниже приведены некоторые простые запросы, помогающие понять текущее и предыдущее состояния приложения.

### <a name="show-application-logs-from-azure-spring-cloud"></a>Отображение журналов приложений из Azure Веснного облака

Чтобы просмотреть список журналов приложений из Azure Веснного облака, отсортированный по времени с самыми последними журналами, показанными первыми:

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| sort by TimeGenerated desc
```

### <a name="show-logs-entries-containing-errors-or-exceptions"></a>Отображение записей журналов, содержащих ошибки или исключения

Этот запрос позволяет просматривать записи журнала, в которых упоминается ошибка или исключение.  Результаты не сортируются.

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| where Log contains "error" or Log contains "exception"
```

Используйте этот запрос для поиска ошибок или изменения условий запроса для поиска конкретных кодов ошибок или исключений.  

### <a name="show-the-number-of-errors-and-exceptions-reported-by-your-application-over-the-last-hour"></a>Отображение количества ошибок и исключений, о которых сообщило приложение за последний час

Этот запрос создает круговую диаграмму, отображающую количество ошибок и исключений, зарегистрированных приложением за последний час:

```sql
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h)
| where Log contains "error" or Log contains "exception"
| summarize count_per_app = count() by AppName
| sort by count_per_app desc
| render piechart
```

### <a name="learn-more-about-querying-application-logs"></a>Дополнительные сведения о запросах журналов приложений

Azure Monitor предоставляет обширную поддержку для запросов журналов приложений с помощью Log Analytics.  Чтобы узнать больше об этой службе, изучите учебник по [запросам журналов](../azure-monitor/log-query/get-started-queries.md) с помощью Azure Monitor. [Обзор запросов журналов в Azure Monitor](../azure-monitor/log-query/log-query-overview.md) содержит дополнительные сведения о создании запросов для анализа журналов приложений.
