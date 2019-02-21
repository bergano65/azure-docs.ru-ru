---
title: Руководство. Прием данных журнала диагностики и действий в Azure Data Explorer без необходимости писать код
description: В этом руководстве описано, как принимать данные в Azure Data Explorer без необходимости писать код, а также как создавать к ним запросы.
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: tutorial
ms.date: 2/5/2019
ms.openlocfilehash: 145a56bee857debdbf028834a3ed378efd8671c8
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/20/2019
ms.locfileid: "56447503"
---
# <a name="tutorial-ingest-data-in-azure-data-explorer-without-one-line-of-code"></a>Руководство по Прием данных в Azure Data Explorer без необходимости писать код

Из этого руководства вы узнаете, как принимать данные журнала диагностики и действий в кластер Azure Data Explorer без необходимости писать код. Этот простой метод приема позволяет вам оперативно начать отправку запросов в Azure Data Explorer для анализа данных.

Из этого руководства вы узнаете, как:
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
* [Кластер и база данных Azure Data Explorer](create-cluster-database-portal.md). В этом руководстве используется имя базы данных *AzureMonitoring*.

## <a name="azure-monitoring-data-provider---diagnostic-and-activity-logs"></a>Поставщик данных для службы мониторинга Azure — журналы диагностики и действий

Просмотрите и проанализируйте данные, предоставляемые журналами диагностики и действий для службы мониторинга Azure. Мы создадим конвейер приема на основе этих схем данных.

### <a name="diagnostic-logs-example"></a>Примеры журналов диагностики

Журналы диагностики Azure — это метрики, создаваемые службой Azure. Они предоставляют данные о работе службы. Данные агрегируются с интервалом в 1 минуту. Каждое событие журналов диагностики содержит одну запись. Ниже приведен пример схемы события для метрики Azure Data Explorer (на основе длительности запроса):

```json
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
}
```

### <a name="activity-logs-example"></a>Пример журналов действий

Журналы действий Azure — это журналы уровня подписки, содержащие коллекции записей. В журналах хранятся данные об операциях, выполняемых с ресурсами в вашей подписке. В отличие от журналов диагностики событие в журналах действий содержит массив записей. Позже в этом руководстве нам потребуется разделить этот массив записей. Ниже приведен пример события журнала действия для проверки доступа:

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

## <a name="set-up-ingestion-pipeline-in-azure-data-explorer"></a>Настройка конвейера приема в Azure Data Explorer 

Настройка конвейера Azure Data Explorer состоит из нескольких шагов, включая [создание таблицы и прием данных](/azure/data-explorer/ingest-sample-data#ingest-data). Вы также можете работать с данными, сопоставлять и обновлять их.

### <a name="connect-to-azure-data-explorer-web-ui"></a>Подключение к пользовательскому веб-интерфейсу Azure Data Explorer

1. В базе данных *AzureMonitoring* Azure Data Explorer выберите пункт **Запрос**, чтобы открыть пользовательский веб-интерфейс Azure Data Explorer.

    ![Запрос](media/ingest-data-no-code/query-database.png)

### <a name="create-target-tables"></a>Создание целевых таблиц

С помощью пользовательского веб-интерфейса Azure Data Explorer создайте целевые таблицы в базе данных Azure Data Explorer.

#### <a name="diagnostic-logs-table"></a>Таблица журналов диагностики

1. Создайте таблицу *DiagnosticLogsRecords* в базе данных *AzureMonitoring*, в которую будут направляться записи журнала диагностики с помощью управляющей команды `.create table`:

    ```kusto
    .create table DiagnosticLogsRecords (Timestamp:datetime, ResourceId:string, MetricName:string, Count:int, Total:double, Minimum:double, Maximum:double, Average:double, TimeGrain:string)
    ```

1. Выберите **Выполнить**, чтобы создать таблицу.

    ![Выполнение запроса](media/ingest-data-no-code/run-query.png)

#### <a name="activity-logs-tables"></a>Таблицы журналов действий

Так как структура журналов действий не является табличной, вам потребуется обработать данные и развернуть каждое событие в одну или несколько записей. Необработанные данные будут приняты промежуточной таблицей *ActivityLogsRawRecords*. При этом данные будут обработаны и развернуты. Развернутые данные затем будут приняты таблицей *ActivityLogsRecords* с помощью политики обновления. То есть вам потребуется создать две отдельных таблицы для приема журналов действий.

1. Создайте таблицу *ActivityLogsRecords* в базе данных *AzureMonitoring*, в которую будут направляться записи журнала действий. Выполните следующий запрос Azure Data Explorer, чтобы создать таблицу:

    ```kusto
    .create table ActivityLogsRecords (Timestamp:datetime, ResourceId:string, OperationName:string, Category:string, ResultType:string, ResultSignature:string, DurationMs:int, IdentityAuthorization:dynamic, IdentityClaims:dynamic, Location:string, Level:string)
    ```

1. Создайте промежуточную таблицу данных *ActivityLogsRawRecords* в базе данных *AzureMonitoring*, чтобы обработать данные:

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

#### <a name="diagnostic-logs-table-mapping"></a>Сопоставление таблицы журналов диагностики

Используйте следующий запрос, чтобы сопоставить данные с таблицей:

```kusto
.create table DiagnosticLogsRecords ingestion json mapping 'DiagnosticLogsRecordsMapping' '[{"column":"Timestamp","path":"$.time"},{"column":"ResourceId","path":"$.resourceId"},{"column":"MetricName","path":"$.metricName"},{"column":"Count","path":"$.count"},{"column":"Total","path":"$.total"},{"column":"Minimum","path":"$.minimum"},{"column":"Maximum","path":"$.maximum"},{"column":"Average","path":"$.average"},{"column":"TimeGrain","path":"$.timeGrain"}]'
```

#### <a name="activity-logs-table-mapping"></a>Сопоставление таблиц журналов действий

Используйте следующий запрос, чтобы сопоставить данные с таблицей:

```kusto
.create table ActivityLogsRawRecords ingestion json mapping 'ActivityLogsRawRecordsMapping' '[{"column":"Records","path":"$.records"}]'
```

### <a name="create-update-policy"></a>Создание политики обновления

1. Создайте [функцию](/azure/kusto/management/functions), которая разворачивает коллекцию записей таким образом, чтобы каждое значение в коллекции получало отдельную строку. Используйте оператор [`mvexpand`](/azure/kusto/query/mvexpandoperator):

    ```kusto
    .create function ActivityLogRecordsExpand() {
        ActivityLogsRawRecords
        | mvexpand events = Records
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

2. Добавьте [политику обновления](/azure/kusto/concepts/updatepolicy) в целевую таблицу. Она будет автоматически выполнять запрос для новых принятых данных в промежуточной таблице данных *ActivityLogsRawRecords* и вставлять его результаты в таблицу *ActivityLogsRecords*:

    ```kusto
    .alter table ActivityLogsRecords policy update @'[{"Source": "ActivityLogsRawRecords", "Query": "ActivityLogRecordsExpand()", "IsEnabled": "True"}]'
    ```

## <a name="create-an-event-hub-namespace"></a>Создание пространства имен концентратора событий

Журналы диагностики Azure позволяют экспортировать метрики в учетную запись хранения в концентраторе событий. В этом руководстве описано, как направлять метрики через концентратор событий. Далее вы создадите пространство имен концентраторов событий и концентратор событий для журналов диагностики. Служба мониторинга Azure создаст концентратор событий *insights-operational-logs* для журналов действий.

1. Создайте концентратор событий с помощью шаблона Azure Resource Manager на портале Azure. Нажмите следующую кнопку, чтобы начать развертывание. Чтобы закончить выполнение инструкций из этой статьи, щелкните правой кнопкой мыши и выберите ссылку **Открыть в новом окне**. Нажав кнопку **Развернуть в Azure**, вы перейдете на портал Azure.

    [![Развертывание в Azure](media/ingest-data-no-code/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

1. Создайте пространство имен концентратора событий и концентратор событий для журналов диагностики.

    ![Создание концентратора событий](media/ingest-data-no-code/event-hub.png)

    Заполните форму, указав следующую информацию. Используйте значения по умолчанию для всех параметров, отсутствующих в следующей таблице.

    **Параметр** | **Рекомендуемое значение** | **Описание поля**
    |---|---|---|
    | Подписка | Ваша подписка | Выберите подписку Azure, которую нужно использовать для своего концентратора событий.|
    | Группа ресурсов | *test-resource-group* | Создайте новую группу ресурсов. |
    | Расположение | Выберите регион в соответствии со своими потребностями. | Создайте пространство имен концентратора событий в том же расположении, в котором находятся другие ресурсы.
    | Имя пространства имен | *AzureMonitoringData* | Выберите уникальное имя, идентифицирующее пространство имен.
    | Имя концентратора событий | *DiagnosticLogsData* | Концентратор событий находится в пространстве имен, предоставляющем уникальный контейнер области. |
    | Имя группы потребителей | *adxpipeline* | Создайте имя группы потребителей. Так каждое из нескольких получающих события приложений будет иметь отдельное представление потока событий. |
    | | |

## <a name="connect-azure-monitoring-logs-to-event-hub"></a>Подключение журналов мониторинга Azure к концентратору событий

### <a name="diagnostic-logs-connection-to-event-hub"></a>Подключение журналов диагностики к концентратору событий

Выберите ресурс, из которого следует экспортировать метрики. Есть несколько типов ресурсов, которые позволяют экспортировать журналы диагностики, в том числе пространство имен концентратора событий, хранилище ключей, центр Интернета вещей и кластер Azure Data Explorer. В этом руководстве мы используем в качестве ресурса кластер Azure Data Explorer.

1. Выберите свой кластер Kusto на портале Azure.

    ![Параметры диагностики](media/ingest-data-no-code/diagnostic-settings.png)

1. Выберите **Параметры диагностики** в меню слева.
1. Щелкните ссылку **Включить диагностику**. Откроется окно **Параметры диагностики**.

    ![Окно "Параметры диагностики"](media/ingest-data-no-code/diagnostic-settings-window.png)

1. На панели **Параметры диагностики**:
    1. Присвойте данным журнала диагностики имя: *ADXExportedData*.
    1. Установите флажок **Все метрики** (необязательно).
    1. Установите флажок **Передать в концентратор событий**.
    1. Щелкните **Настроить**.

1. На панели **Выбор концентратора событий** настройте экспорт в созданный вами концентратор событий:
    1. **Выберите пространство имен концентратора событий** *AzureMonitoringData* в раскрывающемся списке.
    1. **Выберите пространство имен концентратора событий** *diagnosticlogsdata* в раскрывающемся списке.
    1. **Выберите имя политики концентратора событий** в раскрывающемся списке.
    1. Последовательно выберите **ОК**.

1. Выберите команду **Сохранить**. Пространство имен концентратора событий, имя и имя политики отобразятся в окне.

    ![Сохранение параметров диагностики](media/ingest-data-no-code/save-diagnostic-settings.png)

### <a name="activity-logs-connection-to-event-hub"></a>Подключение журналов действий к концентратору событий

1. В меню портала Azure слева выберите **Журнал действий**.
1. Откроется окно **Журнал действий**. **Щелкните "Экспортировать в концентратор событий"**.

    ![Журнал действий](media/ingest-data-no-code/activity-log.png)

1. В окне **Экспорт журнала действий**:
 
    ![Экспорт журнала действий](media/ingest-data-no-code/export-activity-log.png)

    1. Выберите свою подписку.
    1. В раскрывающемся списке **Регионы** выберите **Выбрать все**.
    1. Установите флажок **Экспорт в концентратор событий**.
    1. Щелкните **Выбрать пространство имен служебной шины**, чтобы открыть панель **Выбор концентратора событий**.
    1. На панели **Выбор концентратора событий** выберите следующие элементы в раскрывающихся списках: подписку, пространство имен событий *AzureMonitoringData* и имя политики концентратора событий по умолчанию.
    1. Последовательно выберите **ОК**.
    1. Нажмите кнопку **Сохранить** в правой верхней части окна. Будет создан концентратор событий с именем *insights-operational-logs*.

### <a name="see-data-flowing-to-your-event-hubs"></a>Просмотр потока данных к вашим концентраторам событий

1. Подождите несколько минут, чтобы подключение определилось, а экспорт журнала действий в концентратор событий был завершен. Перейдите к пространству имен концентратора событий, чтобы просмотреть созданные вами концентраторы событий.

    ![Созданные концентраторы событий](media/ingest-data-no-code/event-hubs-created.png)

1. Просмотрите потоки данных к вашему концентратору событий:

    ![Данные концентраторов событий](media/ingest-data-no-code/event-hubs-data.png)

## <a name="connect-event-hub-to-azure-data-explorer"></a>Подключение концентратора событий к Azure Data Explorer

### <a name="diagnostic-logs-data-connection"></a>Подключение к данным журналов диагностики

1. В кластере Azure Data Explorer *kustodocs* выберите **Базы данных** в меню слева.
1. В окне **Базы данных** выберите имя базы данных *AzureMonitoring*.
1. В меню слева выберите **Прием данных**.
1. В окне **Прием данных** щелкните **+Добавить подключение к данным**.
1. В окне **Подключение к данным** введите следующую информацию:

    ![Подключение к концентратору событий](media/ingest-data-no-code/event-hub-data-connection.png)

    Источник данных

    **Параметр** | **Рекомендуемое значение** | **Описание поля**
    |---|---|---|
    | Имя подключения к данным | *DiagnosticsLogsConnection* | Имя создаваемого подключения к обозревателю данных Azure.|
    | Пространство имен концентратора событий | *AzureMonitoringData* | Имя, выбранное ранее и определяющее пространство имен. |
    | концентратор событий; | *diagnosticlogsdata* | Созданный концентратор событий. |
    | Группа потребителей | *adxpipeline* | Группа получателей событий, определенная в созданном концентраторе событий. |
    | | |

    Целевая таблица

    Существует два варианта маршрутизации: *статическая* и *динамическая*. В этом руководстве используется статическая маршрутизация (по умолчанию), для которой можно указать имя таблицы, формат файла и сопоставление. Поэтому не устанавливайте флажок **Мои данные содержат сведения о маршрутизации**.

     **Параметр** | **Рекомендуемое значение** | **Описание поля**
    |---|---|---|
    | Таблица | *DiagnosticLogsRecords* | Таблица, созданная вами в базе данных *AzureMonitoring*. |
    | Формат данных | *JSON* | Форматирование в таблице. |
    | Сопоставление столбцов | *DiagnosticLogsRecordsMapping* | Сопоставление, созданное вами в базе данных *AzureMonitoring*, которое сопоставляет входящие данные JSON с именами столбцов и типами данных таблицы *DiagnosticLogsRecords*.|
    | | |

1. Нажмите кнопку **Создать**.  

### <a name="activity-logs-data-connection"></a>Подключение к данным журналов действий

Повторите шаги, описанные в разделе [Подключение к данным журналов диагностики](#diagnostic-logs-data-connection), чтобы создать подключение к данным журналов действий.

1. Настройте следующие параметры в окне **Подключение к данным**:

    Источник данных

    **Параметр** | **Рекомендуемое значение** | **Описание поля**
    |---|---|---|
    | Имя подключения к данным | *ActivityLogsConnection* | Имя создаваемого подключения к обозревателю данных Azure.|
    | Пространство имен концентратора событий | *AzureMonitoringData* | Имя, выбранное ранее и определяющее пространство имен. |
    | концентратор событий; | *insights-operational-logs* | Созданный концентратор событий. |
    | Группа потребителей | *$Default* | Группа потребителей по умолчанию. При необходимости вы можете создать другую группу потребителей. |
    | | |

    Целевая таблица

    Существует два варианта маршрутизации: *статическая* и *динамическая*. В этом руководстве используется статическая маршрутизация (по умолчанию), для которой можно указать имя таблицы, формат файла и сопоставление. Поэтому не устанавливайте флажок **Мои данные содержат сведения о маршрутизации**.

     **Параметр** | **Рекомендуемое значение** | **Описание поля**
    |---|---|---|
    | Таблица | *ActivityLogsRawRecords* | Таблица, созданная вами в базе данных *AzureMonitoring*. |
    | Формат данных | *JSON* | Форматирование в таблице. |
    | Сопоставление столбцов | *ActivityLogsRawRecordsMapping* | Сопоставление, созданное вами в базе данных *AzureMonitoring*, которое сопоставляет входящие данные JSON с именами столбцов и типами данных таблицы *ActivityLogsRawRecords*.|
    | | |

1. Нажмите кнопку **Создать**.  

## <a name="query-the-new-tables"></a>Отправка запросов новым таблицам

Вам доступен конвейер с потоками данных. На прием через кластер требуется пять минут (по умолчанию), поэтому подождите, пока поток данных завершит передачу, прежде чем начинать отправку запросов.

### <a name="diagnostic-logs-table-query-example"></a>Пример запроса к таблице журналов диагностики

Приведенный ниже запрос анализирует данные длительности запроса в записях журнала диагностики Azure Data Explorer:

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

### <a name="activity-logs-table-query-example"></a>Пример запроса к таблице журналов действий

Приведенный ниже запрос анализирует данные в записях журнала действий Azure Data Explorer:

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

Чтобы узнать, как создавать запросы к данным, которые вы извлекли из Azure Data Explorer, см. следующую статью:

> [!div class="nextstepaction"]
> [Написание запросов для обозревателя данных Azure](write-queries.md)
