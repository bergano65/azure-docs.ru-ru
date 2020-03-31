---
title: Подготовка к изменению формата в журналах ресурсов Azure Monitor
description: 1 ноября 2018 года журналы ресурсов Azure перешли на использование капли приложений.
author: johnkemnetz
services: monitoring
ms.topic: conceptual
ms.date: 07/06/2018
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: 001dfbc78c0027249143e933684523d47af383d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79096776"
---
# <a name="prepare-for-format-change-to-azure-monitor-platform-logs-archived-to-a-storage-account"></a>Подготовка к изменению формата в журналы платформы Azure Monitor, заархивированные в учетную запись хранения

> [!WARNING]
> Если вы отправляете [журналы ресурсов или метрикресурсов Azure в учетную запись хранения с помощью диагностических настроек](resource-logs-collect-storage.md) или [журналов активности в учетную запись хранения с помощью профилей журналов,](resource-logs-collect-storage.md)формат данных в учетной записи хранилища изменен на JSON Lines 1 ноября 2018 года. Ниже описывается влияние этого изменения, а также приводятся инструкции по обновлению инструментария для включения поддержки нового формата.
>

## <a name="what-changed"></a>Изменения

Azure Monitor предлагает возможность, позволяющую отправлять журналы ресурсов и журналы активности в учетную запись хранения Azure, пространство имен событий концентраторов или в рабочее пространство аналитики журналов в Azure Monitor. Для решения проблемы производительности системы **1 ноября 2018 года в 12:00 по UTC** изменился формат отправки данных журнала на хранение кабо. Если у вас есть инструменты для считывания данных из хранилища BLOB-объектов, их необходимо обновить для распознавания нового формата данных.

* В четверг, 1 ноября 2018 года в 12:00 полночь UTC, формат blob изменился на [JSON Lines](http://jsonlines.org/). Это означает, что каждая запись будет разделяться новой строкой. Массив внешних записей и запятые между записями JSON использоваться не будут.
* Формат blob изменился для всех диагностических настроек сразу по всем подпискам. Первый файл PT1H.json, изданный на 1 ноября, использовал этот новый формат. Имена больших двоичных объектов и контейнеров останутся без изменений.
* Установка диагностической настройки в период до 1 ноября продолжала излучать данные в текущем формате до 1 ноября.
* Это изменение произошло сразу во всех регионах общедоступных облаков. Изменения не произойдут в Microsoft Azure, работающих в 21Vianet, Azure Germany или Azure Government clouds.
* Это изменение затрагивает следующие типы данных:
  * [Журналы ресурсов Azure](archive-diagnostic-logs.md) [(см. список ресурсов здесь)](diagnostic-logs-schema.md)
  * [метрики ресурсов Azure, экспортируемые с помощью параметров диагностики](diagnostic-settings.md);
  * [данные журнала действий Azure, экспортируемые с помощью профилей журналов](activity-log-collect.md).
* Это изменение не затрагивает следующие данные:
  * журналы сетевых потоков;
  * Системы служб Azure еще не доступны через Azure Monitor (например, журналы ресурсов службы приложений Azure, журналы аналитики хранения данных)
  * Направление журналов ресурсов Azure и журналов активности в другие направления (Концентраторы событий, аналитика журналов журналов)

### <a name="how-to-see-if-you-are-impacted"></a>Как узнать о возможном влиянии

Это изменение затронет вас только в приведенных ниже случаях.
1. Отправляет данные журнала в учетную запись хранения Azure с помощью диагностической настройки, и
2. У вас есть инструменты, зависящие от структуры JSON этих журналов в хранилище.
 
Чтобы определить, есть ли у вас диагностические настройки, которые отправляют данные в учетную запись хранения Azure, можно перейти в раздел **Монитор** портала, нажмите на **диагностические настройки**и определите любые ресурсы, которые имеют **диагностический статус,** установленный на **Enabled:**

![Колонка "Параметры диагностики" Azure Monitor](media/diagnostic-logs-append-blobs/portal-diag-settings.png)

Если в колонке "Состояние диагностики" задано значение "Включено", для данного ресурса существует активный параметр диагностики. Щелкните ресурс, чтобы просмотреть, используются ли какие-либо параметры диагностики для отправки данных в учетную запись хранения.

![Учетная запись хранения включена](media/diagnostic-logs-append-blobs/portal-storage-enabled.png)

Если у вас есть ресурсы, которые отправляют данные в учетную запись хранения с помощью этих параметров диагностики ресурса, это изменение затронет формат данных в этой учетной записи. Если у вас нет специальных инструментов, которые работают с этими учетными записями хранения, изменение формата вас не коснется.

### <a name="details-of-the-format-change"></a>Сведения об изменении формата

Текущий формат файла PT1H.json в хранилище BLOB-объектов Azure использует массив записей JSON. Ниже приведен пример файла журнала KeyVault.

```json
{
    "records": [
        {
            "time": "2016-01-05T01:32:01.2691226Z",
            "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
            "operationName": "VaultGet",
            "operationVersion": "2015-06-01",
            "category": "AuditEvent",
            "resultType": "Success",
            "resultSignature": "OK",
            "resultDescription": "",
            "durationMs": "78",
            "callerIpAddress": "104.40.82.76",
            "correlationId": "",
            "identity": {
                "claim": {
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com",
                    "appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"
                }
            },
            "properties": {
                "clientInfo": "azure-resource-manager/2.0",
                "requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01",
                "id": "https://contosokeyvault.vault.azure.net/",
                "httpStatusCode": 200
            }
        },
        {
            "time": "2016-01-05T01:33:56.5264523Z",
            "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
            "operationName": "VaultGet",
            "operationVersion": "2015-06-01",
            "category": "AuditEvent",
            "resultType": "Success",
            "resultSignature": "OK",
            "resultDescription": "",
            "durationMs": "83",
            "callerIpAddress": "104.40.82.76",
            "correlationId": "",
            "identity": {
                "claim": {
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com",
                    "appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"
                }
            },
            "properties": {
                "clientInfo": "azure-resource-manager/2.0",
                "requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01",
                "id": "https://contosokeyvault.vault.azure.net/",
                "httpStatusCode": 200
            }
        }
    ]
}
```

В новом формате используются [строки JSON](http://jsonlines.org/), где каждое событие представляет собой строку, а символ новой строки означает новое событие. После изменения приведенный выше пример в файле PT1H.json будет выглядеть следующим образом:

```json
{"time": "2016-01-05T01:32:01.2691226Z","resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT","operationName": "VaultGet","operationVersion": "2015-06-01","category": "AuditEvent","resultType": "Success","resultSignature": "OK","resultDescription": "","durationMs": "78","callerIpAddress": "104.40.82.76","correlationId": "","identity": {"claim": {"http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com","appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},"properties": {"clientInfo": "azure-resource-manager/2.0","requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id": "https://contosokeyvault.vault.azure.net/","httpStatusCode": 200}}
{"time": "2016-01-05T01:33:56.5264523Z","resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT","operationName": "VaultGet","operationVersion": "2015-06-01","category": "AuditEvent","resultType": "Success","resultSignature": "OK","resultDescription": "","durationMs": "83","callerIpAddress": "104.40.82.76","correlationId": "","identity": {"claim": {"http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com","appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},"properties": {"clientInfo": "azure-resource-manager/2.0","requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id": "https://contosokeyvault.vault.azure.net/","httpStatusCode": 200}}
```

Этот новый формат позволяет Azure Monitor отправлять файлы журналов с помощью [добавочных BLOB-объектов](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs), которые более эффективны для постоянного добавления новых данных событий.

## <a name="how-to-update"></a>Способ обновления

Обновления следует выполнять только при наличии специальных инструментов, которые принимают эти файлы журналов для дальнейшей обработки. Если вы используете внешний инструмент анализа журналов или SIEM, рекомендуется [заменить его на концентраторы событий для приема данных](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/). Интеграция Центров событий оптимально подходит для обработки журналов из многих служб и создания закладок для расположений в определенном журнале.

Пользовательские инструменты необходимо обновить для обработки текущего формата и описанного выше формата JSON Lines. Тем самым гарантируется, что они сохранят работоспособность при появлении данных в новом формате.

## <a name="next-steps"></a>Дальнейшие действия

* Узнайте о [архиве журналов ресурсов ресурсов в учетную запись хранения](./../../azure-monitor/platform/archive-diagnostic-logs.md)
* Узнайте больше об [архивации данных журнала действий в учетной записи хранения](./../../azure-monitor/platform/archive-activity-log.md).

