---
title: Мониторинг операций приема обозреватель данных Azure с помощью журналов диагностики
description: Узнайте, как настроить журналы диагностики для обозреватель данных Azure, чтобы отслеживать операции приема.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: 3e10979e26cacdc0c2071a6030c945adad21a51c
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/19/2020
ms.locfileid: "76277422"
---
# <a name="monitor-azure-data-explorer-ingestion-operations-using-diagnostic-logs-preview"></a>Мониторинг операций приема обозреватель данных Azure с помощью журналов диагностики (Предварительная версия)

Azure Data Explorer — это быстрая и полностью управляемая служба для анализа большого объема потоковых данных в реальном времени из приложений, с веб-сайтов, с устройств Интернета вещей и т. д. Чтобы использовать обозреватель данных Azure, сначала нужно создать кластер и одну или несколько баз данных в этом кластере. Затем вы принимаете (загружаете) данные в таблицу базы данных, чтобы выполнять запросы к ней. [Azure Monitor журналы диагностики](/azure/azure-monitor/platform/diagnostic-logs-overview) содержат данные о работе ресурсов Azure. Azure обозреватель данных использует журналы диагностики для анализа успешных и неудачных приемов приема. Вы можете экспортировать журналы операций в службу хранилища Azure, концентратор событий или Log Analytics для отслеживания состояния приема. Журналы из службы хранилища Azure и концентратора событий Azure можно направлять в таблицу в кластере Azure обозреватель данных для дальнейшего анализа.

## <a name="prerequisites"></a>Технические условия

* Если у вас нет подписки Azure, создайте [бесплатную учетную запись Azure](https://azure.microsoft.com/free/).
* Создайте [кластер и базу данных](create-cluster-database-portal.md).

## <a name="sign-in-to-the-azure-portal"></a>Вход на портал Azure

Войдите на [портал Azure](https://portal.azure.com/).

## <a name="set-up-diagnostic-logs-for-an-azure-data-explorer-cluster"></a>Настройка журналов диагностики для кластера Azure обозреватель данных

Журналы диагностики можно использовать для настройки сбора следующих данных журнала:
* Успешные операции приема: эти журналы содержат сведения об успешно завершенных операциях приема.
* Неудачные операции приема: эти журналы содержат подробные сведения о неудачных операциях приема, включая сведения об ошибке. 

Затем данные архивируются в учетную запись хранения, передаются в концентратор событий или отправляются в Log Analytics в соответствии со своими спецификациями.

### <a name="enable-diagnostic-logs"></a>Включение журналов диагностики

По умолчанию журналы диагностики отключены. Чтобы включить журналы диагностики, выполните следующие действия.

1. В [портал Azure](https://portal.azure.com)выберите ресурс кластера Azure обозреватель данных, который требуется отслеживать.
1. В разделе **Мониторинг** выберите **Параметры диагностики**.
  
    ![Добавление журналов диагностики](media/using-diagnostic-logs/add-diagnostic-logs.png)

1. Выберите **Добавить параметр диагностики**.
1. В окне " **параметры диагностики** ":
 
    ![Конфигурация параметров диагностики](media/using-diagnostic-logs/configure-diagnostics-settings.png) 

    1. Выберите **имя** для параметра диагностики.
    1. Выберите один или несколько целевых объектов: учетная запись хранения, концентратор событий или Log Analytics.
    1. Выберите журналы для сбора: `SucceededIngestion` или `FailedIngestion`.
    1. Выберите [метрики](using-metrics.md#supported-azure-data-explorer-metrics) для сбора (необязательно).  
    1. Нажмите кнопку **сохранить** , чтобы сохранить новые параметры и метрики журналов диагностики.
    1. Создайте **новый запрос в службу поддержки** в портал Azure, чтобы запросить активацию журналов диагностики.

Новые параметры будут установлены через несколько минут. Журналы затем отображаются в настроенном целевом объекте архивации (учетная запись хранения, концентратор событий или Log Analytics). 

## <a name="diagnostic-logs-schema"></a>Схема журналов диагностики

Все [журналы диагностики Azure Monitor используют общую схему верхнего уровня](/azure/azure-monitor/platform/diagnostic-logs-schema). Обозреватель данных Azure имеет уникальные свойства для собственных событий. Все журналы хранятся в формате JSON.

### <a name="ingestion-logs-schema"></a>Схема журналов приема

Строки JSON журнала включают элементы, перечисленные в следующей таблице.

|Имя               |Description
|---                |---
|time               |Время отчета
|resourceId         |Идентификатор ресурса Azure Resource Manager
|operationName      |Имя операции: "MICROSOFT. KUSTO/CLUSTERS/ПРИЕМ/ДЕЙСТВИЕ "
|operationVersion   |Версия схемы: "1,0" 
|категория           |Категория операции. `SucceededIngestion` или `FailedIngestion`. Свойства различаются для [успешной операции](#successful-ingestion-operation-log) или [неудачной операции](#failed-ingestion-operation-log).
|properties         |Подробные сведения об операции.

#### <a name="successful-ingestion-operation-log"></a>Журнал успешных операций приема

**Пример**.

```json
{
    "time": "",
    "resourceId": "",
    "operationName": "MICROSOFT.KUSTO/CLUSTERS/INGEST/ACTION",
    "operationVersion": "1.0",
    "category": "SucceededIngestion",
    "properties":
    {
        "succeededOn": "2019-05-27 07:55:05.3693628",
        "operationId": "b446c48f-6e2f-4884-b723-92eb6dc99cc9",
        "database": "Samples",
        "table": "StormEvents",
        "ingestionSourceId": "66a2959e-80de-4952-975d-b65072fc571d",
        "ingestionSourcePath": "https://kustoingestionlogs.blob.core.windows.net/sampledata/events8347293.json",
        "rootActivityId": "d0bd5dd3-c564-4647-953e-05670e22a81d"
    }
}
```
**Свойства журнала диагностики успешных операций**

|Имя               |Description
|---                |---
|сукцеедедон        |Время завершения приема
|operationId        |ИДЕНТИФИКАТОР операции приема обозреватель данных Azure
|база данных           |Имя целевой базы данных
|table              |Имя целевой таблицы
|инжестионсаурцеид  |Идентификатор источника данных приема
|инжестионсаурцепас|Путь к источнику данных приема или URI BLOB-объекта
|rootActivityId     |Идентификатор действия

#### <a name="failed-ingestion-operation-log"></a>Ошибка при приеме журнала операций приема

**Пример**.

```json
{
    "time": "",
    "resourceId": "",
    "operationName": "MICROSOFT.KUSTO/CLUSTERS/INGEST/ACTION",
    "operationVersion": "1.0",
    "category": "FailedIngestion",
    "properties":
    {
        "failedOn": "2019-05-27 08:57:05.4273524",
        "operationId": "5956515d-9a48-4544-a514-cf4656fe7f95",
        "database": "Samples",
        "table": "StormEvents",
        "ingestionSourceId": "eee56f8c-2211-4ea4-93a6-be556e853e5f",
        "ingestionSourcePath": "https://kustoingestionlogs.blob.core.windows.net/sampledata/events5725592.json",
        "rootActivityId": "52134905-947a-4231-afaf-13d9b7b184d5",
        "details": "Permanent failure downloading blob. URI: ..., permanentReason: Download_SourceNotFound, DownloadFailedException: 'Could not find file ...'",
        "errorCode": "Download_SourceNotFound",
        "failureStatus": "Permanent",
        "originatesFromUpdatePolicy": false,
        "shouldRetry": false
    }
}
```

**Свойства журнала диагностики невыполненной операции**

|Имя               |Description
|---                |---
|фаиледон           |Время завершения приема
|operationId        |ИДЕНТИФИКАТОР операции приема обозреватель данных Azure
|база данных           |Имя целевой базы данных
|table              |Имя целевой таблицы
|инжестионсаурцеид  |Идентификатор источника данных приема
|инжестионсаурцепас|Путь к источнику данных приема или URI BLOB-объекта
|rootActivityId     |Идентификатор действия
|подробности            |Подробное описание ошибки и сообщения об ошибке
|errorCode          |Код ошибки 
|фаилурестатус      |`Permanent` или `Transient`. Повторная попытка временного сбоя может быть выполнена успешно.
|оригинатесфромупдатеполици|True, если ошибка возникла из политики обновления
|шаулдретри        |Значение true, если повторная попытка может быть выполнена

## <a name="next-steps"></a>Дальнейшие действия

* [Учебник. прием и запрос данных мониторинга в Azure обозреватель данных](ingest-data-no-code.md)
* [Monitor Azure Data Explorer performance, health, and usage with metrics](using-metrics.md) (Мониторинг производительности, работоспособности и использования Azure Data Explorer с помощью метрик)

