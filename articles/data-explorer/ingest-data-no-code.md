---
title: Руководство по Прием данных журнала диагностики и действий в Azure Data Explorer без необходимости писать код
description: В этом руководстве описано, как принимать данные в Azure Data Explorer без необходимости писать код, а также как создавать к ним запросы.
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: tutorial
ms.date: 04/07/2019
ms.openlocfilehash: 9f4b7ee0dcc87ca03fd051be0dacedf0912b5320
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59262913"
---
# <a name="tutorial-ingest-data-in-azure-data-explorer-without-one-line-of-code"></a>Руководство по Прием данных в Azure Data Explorer без необходимости писать код

Из этого руководства вы узнаете, как принимать данные из журналов диагностики и действий в кластер Azure Data Explorer без необходимости писать код. Этот простой метод приема позволяет вам оперативно начать отправку запросов в Azure Data Explorer для анализа данных.

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * создавать таблицы и сопоставления приема в базе данных Azure Data Explorer;
> * форматировать принятые данные с помощью политики обновления;
> * создавать [концентраторы событий](/azure/event-hubs/event-hubs-about) и подключать их к Azure Data Explorer;
> * выполнять потоковую передачу данных в концентраторы событий из [журналов диагностики Azure Monitor](/azure/azure-monitor/platform/diagnostic-logs-overview) и [журналов действий Azure Monitor](/azure/azure-monitor/platform/activity-logs-overview);
> * создавать запросы к принятым данным с помощью Azure Data Explorer.

> [!NOTE]
> Создавайте все ресурсы в одном расположении или регионе Azure. Это обязательное требование для журналов диагностики Azure Monitor.

## <a name="prerequisites"></a>Предварительные требования

* Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/) Azure, прежде чем начинать работу.
* [Кластер и база данных Azure Data Explorer](create-cluster-database-portal.md). В этом руководстве используется имя базы данных *TestDatabase*.

## <a name="azure-monitor-data-provider-diagnostic-and-activity-logs"></a>Поставщик данных Azure Monitor — журналы диагностики и действий

Просмотрите и проанализируйте приведенные ниже данные, предоставленные журналами диагностики и действий для службы Azure Monitor. На основе этих схем данных мы создадим конвейер приема. Обратите внимание, что для каждого события в журнале имеется массив записей. Позже в этом учебнике этот массив записей будет разделен.

### <a name="diagnostic-logs-example"></a>Примеры журналов диагностики

Журналы диагностики Azure — это метрики, создаваемые службой Azure. Они предоставляют данные о работе службы. Данные агрегируются по интервалам времени 1 минута. Здесь приведен пример схемы события Azure Data Explorer для метрики длительности запроса:

```json
{
    "records": [
    {
        "count": 14,
        "total": 0,
        "minimum": 0,
        "maximum": 0,
        "average": 0,
        "resourceId": "/SUBSCRIPTIONS/F3101802-8C4F-4E6E-819C-A3B5794D33DD/RESOURCEGROUPS/KEDAMARI/PROVIDERS/MICROSOFT.KUSTO/CLUSTERS/KEREN",
        "time": "2018-12-20T17:00:00.0000000Z",
        "metricName": "QueryDuration",
        "timeGrain": "PT1M"
    },
    {
        "count": 12,
        "total": 0,
        "minimum": 0,
        "maximum": 0,
        "average": 0,
        "resourceId": "/SUBSCRIPTIONS/F3101802-8C4F-4E6E-819C-A3B5794D33DD/RESOURCEGROUPS/KEDAMARI/PROVIDERS/MICROSOFT.KUSTO/CLUSTERS/KEREN",
        "time": "2018-12-21T17:00:00.0000000Z",
        "metricName": "QueryDuration",
        "timeGrain": "PT1M"
    }
    ]
}
```

### <a name="activity-logs-example"></a>Пример журналов действий

Журналы действий Azure являются журналами уровня подписки, предоставляющими информацию об операциях, выполненных в ресурсах в подписке. Вот пример события журнала действия для проверки доступа:

```json
{
    "records": [
    {
        "time": "2018-12-26T16:23:06.1090193Z",
        "resourceId": "/SUBSCRIPTIONS/F80EB51C-C534-4F0B-80AB-AEBC290C1C19/RESOURCEGROUPS/CLEANUPSERVICE/PROVIDERS/MICROSOFT.WEB/SITES/CLNB5F73B70-DCA2-47C2-BB24-77B1A2CAAB4D/PROVIDERS/MICROSOFT.AUTHORIZATION",
        "operationName": "MICROSOFT.AUTHORIZATION/CHECKACCESS/ACTION",
        "category": "Action",
        "resultType": "Start",
        "resultSignature": "Started.",
        "durationMs": 0,
        "callerIpAddress": "13.66.225.188",
        "correlationId": "0de9f4bc-4adc-4209-a774-1b4f4ae573ed",
        "identity": {
            "authorization": {
                ...
            },
            "claims": {
                ...
            }
        },
        "level": "Information",
        "location": "global",
        "properties": {
            ...
        }
    },
    {
        "time": "2018-12-26T16:23:06.3040244Z",
        "resourceId": "/SUBSCRIPTIONS/F80EB51C-C534-4F0B-80AB-AEBC290C1C19/RESOURCEGROUPS/CLEANUPSERVICE/PROVIDERS/MICROSOFT.WEB/SITES/CLNB5F73B70-DCA2-47C2-BB24-77B1A2CAAB4D/PROVIDERS/MICROSOFT.AUTHORIZATION",
        "operationName": "MICROSOFT.AUTHORIZATION/CHECKACCESS/ACTION",
        "category": "Action",
        "resultType": "Success",
        "resultSignature": "Succeeded.OK",
        "durationMs": 194,
        "callerIpAddress": "13.66.225.188",
        "correlationId": "0de9f4bc-4adc-4209-a774-1b4f4ae573ed",
        "identity": {
            "authorization": {
                ...
            },
            "claims": {
                ...
            }
        },
        "level": "Information",
        "location": "global",
        "properties": {
            "statusCode": "OK",
            "serviceRequestId": "87acdebc-945f-4c0c-b931-03050e085626"
        }
    }]
}
```

## <a name="set-up-an-ingestion-pipeline-in-azure-data-explorer"></a>Настройка конвейера приема данных в Azure Data Explorer

Настройка конвейера Azure Data Explorer включает несколько этапов, в том числе [создание таблиц и прием данных](/azure/data-explorer/ingest-sample-data#ingest-data). Вы также можете работать с данными, сопоставлять и обновлять их.

### <a name="connect-to-the-azure-data-explorer-web-ui"></a>Подключение к пользовательскому веб-интерфейсу Azure Data Explorer

В базе данных *TestDatabase* для Azure Data Explorer выберите пункт **Запрос**, чтобы открыть пользовательский веб-интерфейс Azure Data Explorer.

![Страница запроса](media/ingest-data-no-code/query-database.png)

### <a name="create-the-target-tables"></a>Создание целевых таблиц

Структура журналов Azure Monitor не является табличной. Вам потребуется обработать данные и развернуть каждое событие в одну или несколько записей. Необработанные данные будут поступать в промежуточную таблицу с именем *ActivityLogsRawRecords* для журналов действий и *DiagnosticLogsRawRecords* для журналов диагностики. При этом данные будут обработаны и развернуты. Затем на основе политики обновления развернутые данные будут поступать в таблицу *ActivityLogsRecords* для журналов действий и *DiagnosticLogsRecords* для журналов диагностики. Это означает, что вам нужно создать две отдельные таблицы для приема журналов действий и две отдельные таблицы для приема журналов диагностики.

С помощью пользовательского веб-интерфейса Azure Data Explorer создайте целевые таблицы в базе данных Azure Data Explorer.

#### <a name="the-diagnostic-logs-table"></a>Таблица журналов диагностики

1. Создайте в базе данных *TestDatabase* таблицу с именем *DiagnosticLogsRecords* для хранения записей журнала диагностики. Для этого используйте следующую команду `.create table`.

    ```kusto
    .create table DiagnosticLogsRecords (Timestamp:datetime, ResourceId:string, MetricName:string, Count:int, Total:double, Minimum:double, Maximum:double, Average:double, TimeGrain:string)
    ```

1. Выберите **Выполнить**, чтобы создать таблицу.

    ![Выполнение запроса](media/ingest-data-no-code/run-query.png)

1. С помощью следующего запроса создайте в базе данных *TestDatabase* промежуточную таблицу данных с именем *DiagnosticLogsRawRecords* для обработки данных: Выберите **Выполнить**, чтобы создать таблицу.

    ```kusto
    .create table DiagnosticLogsRawRecords (Records:dynamic)
    ```

#### <a name="the-activity-logs-tables"></a>Таблицы журналов действий

1. Создайте таблицу с именем *ActivityLogsRecords* в базе данных *TestDatabase*, которая будет принимать записи журнала действий. Выполните следующий запрос Azure Data Explorer, чтобы создать эту таблицу:

    ```kusto
    .create table ActivityLogsRecords (Timestamp:datetime, ResourceId:string, OperationName:string, Category:string, ResultType:string, ResultSignature:string, DurationMs:int, IdentityAuthorization:dynamic, IdentityClaims:dynamic, Location:string, Level:string)
    ```

1. Создайте в базе данных *TestDatabase* промежуточную таблицу данных с именем *ActivityLogsRawRecords* для обработки данных:

    ```kusto
    .create table ActivityLogsRawRecords (Records:dynamic)
    ```

<!--
     ```kusto
     .alter-merge table ActivityLogsRawRecords policy retention softdelete = 0d
    <[Retention](/azure/kusto/management/retention-policy) for an intermediate data table is set at zero retention policy.
-->

### <a name="create-table-mappings"></a>Создание сопоставлений таблиц

 Так как используется формат данных `json`, сопоставление данных обязательно. Сопоставление `json` сопоставляет каждый путь JSON с именем столбца в таблице.

#### <a name="table-mapping-for-diagnostic-logs"></a>Сопоставление таблиц для журналов диагностики

Чтобы сопоставить данные журналов диагностики с таблицей, выполните следующий запрос:

```kusto
.create table DiagnosticLogsRawRecords ingestion json mapping 'DiagnosticLogsRawRecordsMapping' '[{"column":"Records","path":"$.records"}]'
```

#### <a name="table-mapping-for-activity-logs"></a>Сопоставление таблиц для журналов действий

Чтобы сопоставить данные журналов действий с таблицей, выполните следующий запрос:

```kusto
.create table ActivityLogsRawRecords ingestion json mapping 'ActivityLogsRawRecordsMapping' '[{"column":"Records","path":"$.records"}]'
```

### <a name="create-the-update-policy-for-log-data"></a>Создание политики обновления для данных журнала

#### <a name="activity-log-data-update-policy"></a>Политика обновления данных журнала действий

1. Создайте [функцию](/azure/kusto/management/functions), которая развертывает коллекцию записей журнала действий таким образом, чтобы каждое значение в коллекции получало отдельную строку. Используйте оператор [`mv-expand`](/azure/kusto/query/mvexpandoperator):

    ```kusto
    .create function ActivityLogRecordsExpand() {
        ActivityLogsRawRecords
        | mv-expand events = Records
        | project
            Timestamp = todatetime(events["time"]),
            ResourceId = tostring(events["resourceId"]),
            OperationName = tostring(events["operationName"]),
            Category = tostring(events["category"]),
            ResultType = tostring(events["resultType"]),
            ResultSignature = tostring(events["resultSignature"]),
            DurationMs = toint(events["durationMs"]),
            IdentityAuthorization = events.identity.authorization,
            IdentityClaims = events.identity.claims,
            Location = tostring(events["location"]),
            Level = tostring(events["level"])
    }
    ```

2. Добавьте [политику обновления](/azure/kusto/concepts/updatepolicy) для целевой таблицы. Эта политика будет автоматически выполнять запрос для новых данных, принятых в промежуточную таблицу данных *ActivityLogsRawRecords*, и вставлять результаты запроса в таблицу *ActivityLogsRecords*:

    ```kusto
    .alter table ActivityLogsRecords policy update @'[{"Source": "ActivityLogsRawRecords", "Query": "ActivityLogRecordsExpand()", "IsEnabled": "True"}]'
    ```

#### <a name="diagnostic-log-data-update-policy"></a>Политика обновления данных журнала диагностики

1. Создайте [функцию](/azure/kusto/management/functions), которая развертывает коллекцию записей журнала диагностики таким образом, чтобы каждое значение в коллекции получало отдельную строку. Используйте оператор [`mv-expand`](/azure/kusto/query/mvexpandoperator):
     ```kusto
    .create function DiagnosticLogRecordsExpand() {
        DiagnosticLogsRawRecords
        | mv-expand events = Records
        | project
            Timestamp = todatetime(events["time"]),
            ResourceId = tostring(events["resourceId"]),
            MetricName = tostring(events["metricName"]),
            Count = toint(events["count"]),
            Total = todouble(events["total"]),
            Minimum = todouble(events["minimum"]),
            Maximum = todouble(events["maximum"]),
            Average = todouble(events["average"]),
            TimeGrain = tostring(events["timeGrain"])
    }
    ```

2. Добавьте [политику обновления](/azure/kusto/concepts/updatepolicy) для целевой таблицы. Эта политика будет автоматически выполнять запрос для новых данных, принятых в промежуточную таблицу данных *DiagnosticLogsRawRecords*, и вставлять результаты запроса в таблицу *DiagnosticLogsRecords*:

    ```kusto
    .alter table DiagnosticLogsRecords policy update @'[{"Source": "DiagnosticLogsRawRecords", "Query": "DiagnosticLogRecordsExpand()", "IsEnabled": "True"}]'
    ```

## <a name="create-an-azure-event-hubs-namespace"></a>Создание пространства имен Центров событий Azure

Журналы диагностики Azure позволяют экспортировать метрики в учетную запись хранения или в концентратор событий. В этом руководстве мы направим метрики в концентратор событий. На следующих шагах вы создадите пространство имен концентраторов событий и концентратор событий для журналов диагностики. Служба Azure Monitor создаст концентратор событий *insights-operational-logs* для журналов действий.

1. Создайте концентратор событий на портале Azure с помощью шаблона Azure Resource Manager. Чтобы выполнить остальные шаги из этой статьи, щелкните правой кнопкой кнопку **Развеhнуть в Azure** и выберите вариант **Открыть в новом окне**. Нажав кнопку **Развернуть в Azure**, вы перейдете на портал Azure.

    [![Кнопка "Развертывание в Azure"](media/ingest-data-no-code/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

1. Создайте пространство имен концентраторов событий и концентратор событий для журналов диагностики.

    ![Создание концентратора событий](media/ingest-data-no-code/event-hub.png)

1. Заполните форму, указав следующую информацию. Сохраните значения по умолчанию для всех параметров, не указанных в следующей таблице.

    **Параметр** | **Рекомендуемое значение** | **Описание**
    |---|---|---|
    | **Подписка** | *Ваша подписка* | Выберите подписку Azure, которую нужно использовать для своего концентратора событий.|
    | **Группа ресурсов** | *test-resource-group* | Создайте новую группу ресурсов. |
    | **Местоположение.** | Выберите регион в соответствии со своими потребностями. | Создайте пространство имен концентратора событий в том же расположении, в котором находятся другие ресурсы.
    | **Имя пространства имен** | *AzureMonitoringData* | Выберите уникальное имя, идентифицирующее пространство имен.
    | **Имя концентратора событий** | *DiagnosticLogsData* | Концентратор событий находится в пространстве имен, предоставляющем уникальный контейнер области. |
    | **Имя группы потребителей** | *adxpipeline* | Создайте имя группы потребителей. Группы получателей событий позволяют каждому из нескольких получающих события приложений иметь отдельное представление потока событий. |
    | | |

## <a name="connect-azure-monitor-logs-to-your-event-hub"></a>Подключение журналов мониторинга Azure к концентратору событий

Теперь вам следует подключить к концентратору событий журналы диагностики и журналы действий.

### <a name="connect-diagnostic-logs-to-your-event-hub"></a>Подключение журналов диагностики к концентратору событий

Выберите ресурс, из которого следует экспортировать метрики. Экспорт журналов диагностики поддерживают ресурсы нескольких типов, в том числе пространство имен Центров событий, Azure Key Vault, Центр Интернета вещей и кластер Azure Data Explorer. В этом руководстве мы используем в качестве ресурса кластер Azure Data Explorer.

1. Выберите свой кластер Kusto на портале Azure.
1. Выберите элемент **Параметры диагностики**, а затем ссылку **Включить диагностику**. 

    ![Параметры диагностики](media/ingest-data-no-code/diagnostic-settings.png)

1. Откроется панель **Параметры диагностики**. Сделайте следующее:
   1. Присвойте данным журнала диагностики имя *ADXExportedData*.
   1. В поле **METRIC** (МЕТРИКА) установите флажок **AllMetrics** (Все метрики) (необязательно).
   1. Установите флажок **Передать в концентратор событий**.
   1. Нажмите кнопку **Настроить**.

      ![Панель параметров диагностики](media/ingest-data-no-code/diagnostic-settings-window.png)

1. На панели **Выбор концентратора событий** настройте метод экспорта данных из журналов диагностики в созданный вами концентратор событий.
    1. В списке **Выбрать пространство имен концентратора событий** выберите *AzureMonitoringData*.
    1. В списке **Выберите имя концентратора событий** выберите *diagnosticlogsdata*.
    1. В списке **Выбрать имя политики концентратора событий** выберите **RootManagerSharedAccessKey**.
    1. Нажмите кнопку **ОК**.

1. Щелкните **Сохранить**.

### <a name="connect-activity-logs-to-your-event-hub"></a>Подключение журналов действий к концентратору событий

1. В меню слева на портале Azure выберите **Журнал действий**.
1. Откроется окно **Журнал действий**. Щелкните **Экспортировать в Центр событий**.

    ![Окно "Журнал действий"](media/ingest-data-no-code/activity-log.png)

1. Откроется окно **Экспорт журнала действий**:
 
    ![Окно "Экспорт журнала действий"](media/ingest-data-no-code/export-activity-log.png)

1. В окне **Экспортировать журнал действий** выполните следующие шаги.
      1. Выберите свою подписку.
      1. В списке **Регионы** выберите **Выбрать все**.
      1. Установите флажок **Экспорт в концентратор событий**.
      1. Щелкните **Выберите пространство имен служебной шины**, чтобы открыть панель **Выбор концентратора событий**.
      1. На панели **Выбор концентратора событий** выберите нужную подписку.
      1. В списке **Выбрать пространство имен концентратора событий** выберите *AzureMonitoringData*.
      1. В списке **Выбрать имя политики концентратора событий** выберите имя политики концентратора событий по умолчанию.
      1. Нажмите кнопку **ОК**.
      1. В левом верхнем углу этого окна выберите **Сохранить**.
   Будет создан концентратор событий с именем *insights-operational-logs*.

### <a name="see-data-flowing-to-your-event-hubs"></a>Просмотр потока данных к концентраторам событий

1. Подождите несколько минут, пока создается подключение и завершается экспорт журнала действий в концентратор событий. Перейдите к пространству имен концентраторов событий, чтобы просмотреть созданные концентраторы событий.

    ![Созданные концентраторы событий](media/ingest-data-no-code/event-hubs-created.png)

1. Просмотрите потоки данных к вашему концентратору событий:

    ![Данные концентратора событий](media/ingest-data-no-code/event-hubs-data.png)

## <a name="connect-an-event-hub-to-azure-data-explorer"></a>Подключение концентратора событий к Azure Data Explorer

Теперь пора создать подключения к данным для журналов диагностики и журналов действий.

### <a name="create-the-data-connection-for-diagnostic-logs"></a>Создание подключения к данным для журналов диагностики

1. В кластере Azure Data Explorer с именем *kustodocs* выберите в меню слева **Базы данных**.
1. В окне **Базы данных** выберите базу данных *TestDatabase*.
1. В меню слева выберите **Прием данных**.
1. В окне **Прием данных** щелкните **+Добавить подключение к данным**.
1. В окне **Подключение к данным** введите следующую информацию:

    ![Подключение к данным концентратора событий](media/ingest-data-no-code/event-hub-data-connection.png)

    Источник данных:

    **Параметр** | **Рекомендуемое значение** | **Описание поля**
    |---|---|---|
    | **Имя подключения к данным** | *DiagnosticsLogsConnection* | Имя создаваемого подключения к обозревателю данных Azure.|
    | **Пространство имен концентратора событий** | *AzureMonitoringData* | Имя, выбранное ранее и определяющее пространство имен. |
    | **Концентратор событий** | *diagnosticlogsdata* | Созданный концентратор событий. |
    | **Группа потребителей** | *adxpipeline* | Группа получателей событий, определенная в созданном концентраторе событий. |
    | | |

    Целевая таблица

    Существует два варианта маршрутизации: *статическая* и *динамическая*. В этом руководстве используется статическая маршрутизация (по умолчанию), для которой нужно указать имя таблицы, формат данных и сопоставление. Не активируйте флажок **My data includes routing info** (Мои данные содержат сведения о маршрутизации).

     **Параметр** | **Рекомендуемое значение** | **Описание поля**
    |---|---|---|
    | **Таблица** | *DiagnosticLogsRawRecords* | Таблица, которую вы создали в базе данных *TestDatabase*. |
    | **Формат данных** | *JSON* | Формат, используемый в этой таблице. |
    | **Сопоставление столбцов** | *DiagnosticLogsRecordsMapping* | Сопоставление, которое вы создали в базе данных *TestDatabase* между входящими данными JSON и именами столбцов и типами данных в таблице *DiagnosticLogsRecords*.|
    | | |

1. Нажмите кнопку **Создать**.  

### <a name="create-the-data-connection-for-activity-logs"></a>Создание подключения к данным для журналов действий

Повторите шаги, описанные в разделе "Подключение к данным для журналов диагностики", чтобы создать подключение к данным для журналов действий.

1. Примените следующие параметры в окне **Подключение к данным**.

    Источник данных:

    **Параметр** | **Рекомендуемое значение** | **Описание поля**
    |---|---|---|
    | **Имя подключения к данным** | *ActivityLogsConnection* | Имя создаваемого подключения к обозревателю данных Azure.|
    | **Пространство имен концентратора событий** | *AzureMonitoringData* | Имя, выбранное ранее и определяющее пространство имен. |
    | **Концентратор событий** | *insights-operational-logs* | Созданный концентратор событий. |
    | **Группа потребителей** | *$Default* | Группа потребителей по умолчанию. При необходимости вы можете создать другую группу потребителей. |
    | | |

    Целевая таблица

    Существует два варианта маршрутизации: *статическая* и *динамическая*. В этом руководстве используется статическая маршрутизация (по умолчанию), для которой нужно указать имя таблицы, формат данных и сопоставление. Не активируйте флажок **My data includes routing info** (Мои данные содержат сведения о маршрутизации).

     **Параметр** | **Рекомендуемое значение** | **Описание поля**
    |---|---|---|
    | **Таблица** | *ActivityLogsRawRecords* | Таблица, которую вы создали в базе данных *TestDatabase*. |
    | **Формат данных** | *JSON* | Формат, используемый в этой таблице. |
    | **Сопоставление столбцов** | *ActivityLogsRawRecordsMapping* | Сопоставление, которое вы создали в базе данных *TestDatabase* между входящими данными JSON и именами столбцов и типами данных в таблице *ActivityLogsRawRecords*.|
    | | |

1. Нажмите кнопку **Создать**.  

## <a name="query-the-new-tables"></a>Отправка запросов новым таблицам

Теперь у вас есть конвейер, передающий данные. Прием данных через кластер занимает по умолчанию около пяти минут, поэтому подождите несколько минут с момента начала передачи данных, прежде чем начинать отправку запросов.

### <a name="an-example-of-querying-the-diagnostic-logs-table"></a>Пример запроса к таблице журналов диагностики

Приведенный ниже запрос анализирует данные о длительности запросов по записям журнала диагностики в Azure Data Explorer.

```kusto
DiagnosticLogsRecords
| where Timestamp > ago(15m) and MetricName == 'QueryDuration'
| summarize avg(Average)
```

Результаты запроса:

|   |   |
| --- | --- |
|   |  avg_Average |
|   | 00:06.156 |
| | |

### <a name="an-example-of-querying-the-activity-logs-table"></a>Пример запроса к таблице журналов действий

Приведенный ниже запрос анализирует данные в записях журнала действий Azure Data Explorer.

```kusto
ActivityLogsRecords
| where OperationName == 'MICROSOFT.EVENTHUB/NAMESPACES/AUTHORIZATIONRULES/LISTKEYS/ACTION'
| where ResultType == 'Success'
| summarize avg(DurationMs)
```

Результаты запроса:

|   |   |
| --- | --- |
|   |  avg(DurationMs) |
|   | 768.333 |
| | |

## <a name="next-steps"></a>Дополнительная информация

Чтобы узнать, как создавать многие другие запросы к данным, которые вы извлекли из Azure Data Explorer, изучите следующую статью.

> [!div class="nextstepaction"]
> [Написание запросов для обозревателя данных Azure](write-queries.md)
