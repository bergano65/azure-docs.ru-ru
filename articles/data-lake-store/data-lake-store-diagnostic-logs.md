---
title: Просмотр журналов диагностики Azure Data Lake Storage 1-го поколения | Документация Майкрософт
description: 'Узнайте, как настроить журналы диагностики для Azure Data Lake Storage 1-го поколения и просматривать их '
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: f6e75eb1-d0ae-47cf-bdb8-06684b7c0a94
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: twooley
ms.openlocfilehash: d200f72b3c0e5634c3dca8f60a4754a14351110a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60878755"
---
# <a name="accessing-diagnostic-logs-for-azure-data-lake-storage-gen1"></a>Доступ к журналам диагностики Azure Data Lake Storage 1-го поколения
Узнайте, как включить ведение журнала диагностики для учетной записи Azure Data Lake Storage 1-го поколения и просматривать журналы, собранные для этой учетной записи.

Организации могут позволить диагностической регистрации для своей учетной записи Azure Data Lake Storage Gen1 собирать аудиторские следы доступа к данным, которые предоставляют такие сведения, как список пользователей, обращающихся к данным, как часто доступ к данным, сколько данных хранится в учетной записи и т.д. При включении диагностика и/или запросы регистрируются на основе наилучших усилий. Записи запросов и диагностики журнала создаются только при получении запроса к конечной точке службы.

## <a name="prerequisites"></a>Предварительные требования
* **Подписка Azure**. См. страницу [бесплатной пробной версии Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Учетная запись Azure Data Lake Storage Gen1**. Следуйте инструкциям в [Get started с Azure Data Lake Storage 1 с помощью портала Azure.](data-lake-store-get-started-portal.md)

## <a name="enable-diagnostic-logging-for-your-data-lake-storage-gen1-account"></a>Включение ведения журнала диагностики для учетной записи Data Lake Storage 1-го поколения
1. Подпишитесь на новый [портал Azure](https://portal.azure.com).
2. Откройте свою учетную запись Data Lake Storage 1-го поколения. В колонке учетной записи Data Lake Storage 1-го поколения щелкните **Параметры диагностики**.
3. В колонке **Параметры диагностики** выберите **Включить диагностику**.

    ![Включение ведения журналов диагностики](./media/data-lake-store-diagnostic-logs/turn-on-diagnostics.png "Включение журналов диагностики")

3. В колонке **Параметры диагностики** внесите следующие изменения, чтобы настроить ведение журнала диагностики.
   
    ![Включение ведения журналов диагностики](./media/data-lake-store-diagnostic-logs/enable-diagnostic-logs.png "Включение журналов диагностики")
   
   * В поле **Имя** введите значение конфигурации журнала диагностики.
   * Хранить и обрабатывать данные можно разными способами.
     
        * Выберите параметр **Archive to a storage account** (Архивация в учетную запись хранения), чтобы сохранять журналы в учетную запись хранения Azure. Используйте этот параметр, если вы хотите архивировать данные для последующей пакетной обработки. Если выбран этот параметр, то необходимо указать учетную запись хранения Azure для сохранения журналов.
        
        * Выберите параметр **Stream to an event hub** (Потоковая передача в концентратор событий), чтобы передавать поток данных журнала в концентратор событий Azure. Чаще всего этот параметр используется, если имеется конвейер последующей обработки для анализа входящих журналов в режиме реального времени. При выборе этого параметра необходимо указать сведения о концентраторе событий Azure, который вы хотите использовать.

        * Выберите опцию **отправки в журнал Analytics** для использования службы Azure Monitor для анализа генерируемых данных журнала. При выборе этого параметра необходимо указать сведения для рабочей области Log Analytics, которая будет использоваться для анализа журнала. Просматривайте [или анализируя данные, собранные с помощью журналов Azure Monitor, поиск](../azure-monitor/learn/tutorial-viewdata.md) сведений об использовании журналов Azure Monitor.
     
   * Укажите, что вы хотите получать: журналы аудита, журналы запросов либо и те, и другие журналы.
   * Укажите число дней, в течение которых должны храниться данные. Период удержания применяется, только если учетная запись хранения Azure используется для архивации данных журнала.
   * Нажмите **Сохранить**.

Включив параметры диагностики, вы сможете просматривать журналы на вкладке **Журналы диагностики** .

## <a name="view-diagnostic-logs-for-your-data-lake-storage-gen1-account"></a>Просмотр журналов диагностики для учетной записи Data Lake Storage 1-го поколения
Существует два способа просмотра данных журнала для учетной записи Data Lake Storage 1-го поколения:

* из представления параметров учетной записи Data Lake Storage 1-го поколения;
* из учетной записи хранения Azure, в которой хранятся данные.

### <a name="using-the-data-lake-storage-gen1-settings-view"></a>Использование представления параметров Data Lake Storage 1-го поколения
1. В колонке **Параметры** учетной записи Data Lake Storage 1-го поколения щелкните **Журналы диагностики**.
   
    ![Просмотр диагностических журналов](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs.png "просмотр журналов диагностики;") 
2. В колонке **Журналы диагностики** должны отображаться журналы, упорядоченные по категориям **Журналы аудита** и **Журналы запросов**.
   
   * В журналы запросов записываются все запросы API к учетной записи Data Lake Storage 1-го поколения.
   * Журналы аудита похожи на журналы запросов, но содержат более подробную статистику операций с учетной записью Data Lake Storage 1-го поколения. Например, вызов API для одной передачи в журналах запросов может породить несколько операций добавления в журналах аудита.
3. Щелкните ссылку **Скачать** для каждой записи журнала, чтобы скачать журналы.

### <a name="from-the-azure-storage-account-that-contains-log-data"></a>Использование учетной записи хранения Azure, в которой хранятся данные
1. Откройте колонку учетной записи хранения Azure, связанную с Data Lake Storage 1-го поколения для ведения журнала, и щелкните "BLOB-объекты". В колонке **Служба BLOB-объектов** отображается два контейнера.
   
    ![Просмотр ведения журнала диагностики](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account.png "просмотр журналов диагностики;")
   
   * В контейнере **insights-logs-audit** содержатся журналы аудита.
   * В контейнере **insights-logs-requests** содержатся журналы запросов.
2. Журналы в этих контейнерах хранятся с использованием следующей структуры:
   
    ![Просмотр ведения журнала диагностики](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account-structure.png "просмотр журналов диагностики;")
   
    Например, полный путь к журналу аудита может выглядеть таким образом: `https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestorage/y=2016/m=07/d=18/h=04/m=00/PT1H.json`
   
    Аналогично, полный путь к журналу запросов может выглядеть так `https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestorage/y=2016/m=07/d=18/h=14/m=00/PT1H.json`

## <a name="understand-the-structure-of-the-log-data"></a>Описание структуры данных журнала
В журналах аудита и запросов используется формат JSON. В этом разделе мы рассмотрим структуру JSON для журналов запросов и аудита.

### <a name="request-logs"></a>Журналы запросов
Ниже приведен пример записи журнала запросов в формате JSON. Каждый большой двоичный объект имеет один корневой объект под названием **records** , который содержит массив объектов журнала.

    {
    "records": 
      [        
        . . . .
        ,
        {
             "time": "2016-07-07T21:02:53.456Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/<data_lake_storage_gen1_account_name>",
             "category": "Requests",
             "operationName": "GETCustomerIngressEgress",
             "resultType": "200",
             "callerIpAddress": "::ffff:1.1.1.1",
             "correlationId": "4a11c709-05f5-417c-a98d-6e81b3e29c58",
             "identity": "1808bd5f-62af-45f4-89d8-03c5e81bac30",
             "properties": {"HttpMethod":"GET","Path":"/webhdfs/v1/Samples/Outputs/Drivers.csv","RequestContentLength":0,"ClientRequestId":"3b7adbd9-3519-4f28-a61c-bd89506163b8","StartTime":"2016-07-07T21:02:52.472Z","EndTime":"2016-07-07T21:02:53.456Z"}
        }
        ,
        . . . .
      ]
    }

#### <a name="request-log-schema"></a>Схема журнала запросов
| name | Тип | Описание |
| --- | --- | --- |
| time |Строка |Метка времени журнала (в формате UTC). |
| resourceId |Строка |Идентификатор ресурса, с которым была выполнена операция. |
| категория |Строка |Категория журнала. Например, **Requests**. |
| operationName |Строка |Имя операции, добавленной в журнал. Например, getfilestatus. |
| resultType |Строка |Состояние операции. Например, 200. |
| callerIpAddress |Строка |IP-адрес клиента, отправившего запрос. |
| correlationId |Строка |Идентификатор журнала, который можно использовать для формирования набора связанных записей журнала. |
| identity |Объект |Идентификатор, для которого был создан журнал. |
| properties |JSON |Дополнительные сведения см. ниже. |

#### <a name="request-log-properties-schema"></a>Схема свойств журнала запросов
| name | Тип | Описание |
| --- | --- | --- |
| HttpMethod |Строка |Метод HTTP, использованный для операции. Например, GET. |
| путь |Строка |Путь выполнения операции. |
| RequestContentLength |INT |Длина содержимого HTTP-запроса. |
| ClientRequestId |Строка |Идентификатор, однозначно определяющий данный запрос. |
| StartTime |Строка |Время получения запроса сервером. |
| EndTime |Строка |Время отправки ответа сервером. |

### <a name="audit-logs"></a>Журналы аудита
Ниже приведен пример записи журнала аудита в формате JSON. Каждая капля имеет один корневой объект, называемый **записями,** который содержит массив объектов журнала

    {
    "records": 
      [        
        . . . .
        ,
        {
             "time": "2016-07-08T19:08:59.359Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/<data_lake_storage_gen1_account_name>",
             "category": "Audit",
             "operationName": "SeOpenStream",
             "resultType": "0",
             "resultSignature": "0",
             "correlationId": "381110fc03534e1cb99ec52376ceebdf;Append_BrEKAmg;25.66.9.145",
             "identity": "A9DAFFAF-FFEE-4BB5-A4A0-1B6CBBF24355",
             "properties": {"StreamName":"adl://<data_lake_storage_gen1_account_name>.azuredatalakestore.net/logs.csv"}
        }
        ,
        . . . .
      ]
    }

#### <a name="audit-log-schema"></a>Схема журнала аудита
| name | Тип | Описание |
| --- | --- | --- |
| time |Строка |Метка времени журнала (в формате UTC). |
| resourceId |Строка |Идентификатор ресурса, с которым была выполнена операция. |
| категория |Строка |Категория журнала. Например, **Audit**. |
| operationName |Строка |Имя операции, добавленной в журнал. Например, getfilestatus. |
| resultType |Строка |Состояние операции. Например, 200. |
| resultSignature |Строка |Дополнительные сведения об операции. |
| correlationId |Строка |Идентификатор журнала, который можно использовать для формирования набора связанных записей журнала. |
| identity |Объект |Идентификатор, для которого был создан журнал. |
| properties |JSON |Дополнительные сведения см. ниже. |

#### <a name="audit-log-properties-schema"></a>Схема свойств журнала аудита
| name | Тип | Описание |
| --- | --- | --- |
| StreamName |Строка |Путь выполнения операции. |

## <a name="samples-to-process-the-log-data"></a>Примеры обработки данных журнала
При отправке журналов из журналов Azure Data Lake Storage Gen1 в журналы Azure Monitor (см. [Просмотр или анализ данных, собранных с помощью журналов Azure Monitor, ищут](../azure-monitor/learn/tutorial-viewdata.md) сведения об использовании журналов Azure Monitor) следующий запрос возвращает таблицу, содержащую список имен отображений пользователей, время событий и подсчет событий за время события вместе с визуальной диаграммой. Его можно легко изменить, чтобы отображался идентификатор GUID для пользователя или другие атрибуты:

```
search *
| where ( Type == "AzureDiagnostics" )
| summarize count(TimeGenerated) by identity_s, TimeGenerated
```


В Azure Data Lake Storage 1-го поколения есть пример обработки и анализа данных журнала. Вы можете найти [https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample)образец на . 

## <a name="see-also"></a>См. также
* [Общие сведения об Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Защита данных в Data Lake Storage Gen1](data-lake-store-secure-data.md)

