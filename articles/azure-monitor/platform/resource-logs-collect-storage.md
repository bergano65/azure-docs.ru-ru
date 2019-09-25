---
title: Архивация журналов ресурсов Azure в учетную запись хранения | Документация Майкрософт
description: Узнайте, как архивировать журналы ресурсов Azure для долгосрочного хранения в учетной записи хранения.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 8a1802f0f24ba5ccad3ec1c45f3baa29dfe6909f
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262561"
---
# <a name="archive-azure-resource-logs-to-storage-account"></a>Архивация журналов ресурсов Azure в учетную запись хранения
[Журналы ресурсов](resource-logs-overview.md) в Azure предоставляют широкие и часто встречающиеся данные о внутренней работе ресурса Azure. В этой статье описывается сбор журналов ресурсов в учетную запись хранения Azure для хранения данных для архивирования.

## <a name="prerequisites"></a>Предварительные требования
Если у вас еще нет [учетной записи хранения Azure](../../storage/common/storage-quickstart-create-account.md) , ее необходимо создать. Учетная запись хранения не обязательно должна находиться в той же подписке, что и журнал, отправляющий журналы, если пользователь, настроив параметр, имеет соответствующий доступ RBAC к обеим подпискам.

Не следует использовать существующую учетную запись хранения, в которой хранятся другие данные, не относящиеся к мониторингу, чтобы лучше управлять доступом к данным мониторинга. Если вы также заархивирование [журнала действий](activity-logs-overview.md) в учетную запись хранения, вы можете использовать эту же учетную запись хранения для централизованного хранения всех данных мониторинга.

## <a name="create-a-diagnostic-setting"></a>Создание параметра диагностики
Журналы ресурсов по умолчанию не собираются. Собирайте их в учетной записи хранения Azure и других целевых объектах, создав параметр диагностики для ресурса Azure. Дополнительные сведения см. в статье [Создание параметров диагностики для сбора журналов и метрик в Azure](diagnostic-settings.md) .


## <a name="data-retention"></a>Хранение данных
Политика хранения определяет количество дней хранения данных из каждой категории журналов и данных метрик, хранящихся в учетной записи хранения. Политика хранения может иметь любое число дней от 0 до 365. Политика хранения, равная нулю, указывает, что события для этой категории журнала хранятся неограниченное время.

Политики хранения применяются по дням, поэтому в конце дня (по времени в формате UTC) журналы, срок которых теперь превышает период хранения, будут удалены. Например, если настроена политика хранения в течение одного дня, то в начале текущего дня журналы за вчерашний день будет удалены. Удаление начинается в полночь по времени UTC. Обратите внимание, что удаление журналов из учетной записи хранения может занять до 24 часов. 


## <a name="schema-of-resource-logs-in-storage-account"></a>Схема журналов ресурсов в учетной записи хранения

После создания параметра диагностики в учетной записи хранения создается контейнер хранилища, как только в одной из включенных категорий журнала будет происходить событие. Большие двоичные объекты в контейнере используют следующее соглашение об именовании:

```
insights-logs-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/RESOURCEGROUPS/{resource group name}/PROVIDERS/{resource provider name}/{resource type}/{resource name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Например, большой двоичный объект для группы безопасности сети может иметь имя, аналогичное следующему:

```
insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUP/TESTNSG/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Каждый большой двоичный объект PT1H.json содержит большой двоичный объект JSON с событиями, произошедшими в течение часа, указанного в URL-адресе этого объекта (например, h=12). В течение заданного часа события добавляются в файл PT1H.json по мере возникновения. Значение минуты (m = 00) всегда равно 00, так как события журнала ресурсов разбиваются на отдельные большие двоичные объекты в час.

В файле PT1H. JSON каждое событие хранится в следующем формате. При этом будет использоваться общая схема верхнего уровня, но она будет уникальной для каждой службы Azure, как описано в статье о [схемах журналов ресурсов](resource-logs-overview.md#resource-logs-schema).

``` JSON
{"time": "2016-07-01T00:00:37.2040000Z","systemId": "46cdbb41-cb9c-4f3d-a5b4-1d458d827ff1","category": "NetworkSecurityGroupRuleCounter","resourceId": "/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/TESTNSG","operationName": "NetworkSecurityGroupCounters","properties": {"vnetResourceGuid": "{12345678-9012-3456-7890-123456789012}","subnetPrefix": "10.3.0.0/24","macAddress": "000123456789","ruleName": "/subscriptions/ s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg/securityRules/default-allow-rdp","direction": "In","type": "allow","matchedConnections": 1988}}
```

> [!NOTE]
> Журналы платформы записываются в хранилище BLOB-объектов с помощью [строк JSON](http://jsonlines.org/), где каждое событие является линией, а символ новой строки обозначает новое событие. Этот формат был реализован в ноябре 2018. До этой даты журналы были записаны в хранилище BLOB-объектов в виде массива записей JSON, как описано в разделе [Подготовка к изменению формата для Azure Monitor журналов платформы, архивов которых является учетная запись хранения](resource-logs-blob-format.md).

## <a name="next-steps"></a>Следующие шаги

* [Скачайте большие двоичные объекты для анализа](../../storage/blobs/storage-quickstart-blobs-dotnet.md).
* [Архивируйте журналы Azure Active Directory с Azure Monitor](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md).
* [Дополнительные сведения см. в статье о журналах ресурсов](../../azure-monitor/platform/resource-logs-overview.md).

