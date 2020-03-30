---
title: Архив журналов ресурсов Azure для учетной записи хранения (ru) Документы Майкрософт
description: Узнайте, как архивировать журналы ресурсов Azure для долгосрочного хранения в учетной записи хранения.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/15/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 843c179826b2064a1be24d3cee84b398987b4aed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274220"
---
# <a name="archive-azure-resource-logs-to-storage-account"></a>Архивация журналов ресурсов Azure в учетной записи хранения
[Платформы журналов](platform-logs-overview.md) в Azure, включая журналы деятельности Azure и журналы ресурсов, предоставляют подробную информацию о диагностике и аудите для ресурсов Azure и платформы Azure, от которой они зависят.  В этой статье описывается сбор журналов платформ в учетную запись хранения Azure для сохранения данных для архивирования.

## <a name="prerequisites"></a>Предварительные требования
Если у вас ее еще нет, необходимо [создать учетную запись хранения Azure.](../../storage/common/storage-account-create.md) Учетная запись хранилища не должна находиться в той же подписке, что и ресурс, отправляющий журналы, пока пользователь, настраивающий настройку, имеет соответствующий доступ RBAC к обеим подпискам.


> [!IMPORTANT]
> Учетные записи Azure Data Lake Storage Gen2 в настоящее время не поддерживаются в качестве пункта назначения для диагностических настроек, даже если они могут быть указаны в качестве допустимого варианта на портале Azure.


Не следует использовать существующую учетную запись хранения, в ней хранятся другие данные, не контролирующие их, чтобы можно было лучше контролировать доступ к данным. Если вы архивируете журналы активности и журналы ресурсов вместе, вы можете использовать одну и ту же учетную запись хранения, чтобы хранить все данные мониторинга в центральном месте.

## <a name="create-a-diagnostic-setting"></a>Создание параметра диагностики
Отправка журналов платформ в хранилище и другие направления, создав диагностическую настройку для ресурса Azure. Смотрите [Создать диагностическую настройку для сбора журналов и метрик в Azure](diagnostic-settings.md) для получения подробной информации.


## <a name="collect-data-from-compute-resources"></a>Сбор данных из вычислительных ресурсов
Диагностические настройки будут собирать журналы ресурсов для ресурсов Azure, как и любой другой ресурс, но не их гостевой операционной системы или рабочих нагрузок. Чтобы собрать эти данные, установите [агент Windows Azure Diagnostics.](diagnostics-extension-overview.md) 


## <a name="schema-of-platform-logs-in-storage-account"></a>Схема журналов платформы в учетной записи хранилища

После создания диагностической настройки контейнер для хранения создается в учетной записи хранилища, как только событие происходит в одной из категорий включенного журнала. Капли в контейнере используют следующую конвенцию именования:

```
insights-logs-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/RESOURCEGROUPS/{resource group name}/PROVIDERS/{resource provider name}/{resource type}/{resource name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Например, капля для группы сетевой безопасности может иметь имя, похожее на следующее:

```
insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUP/TESTNSG/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Каждый большой двоичный объект PT1H.json содержит большой двоичный объект JSON с событиями, произошедшими в течение часа, указанного в URL-адресе этого объекта (например, h=12). В течение заданного часа события добавляются в файл PT1H.json по мере возникновения. Значение минуты (m'00) всегда 00, так как события журнала ресурсов разбиваются на отдельные капли в час.

В файле PT1H.json каждое событие хранится в следующем формате. При этом будет использоваться общая схема верхнего уровня, но она будет уникальной для каждой службы Azure, описанной в [схеме журналов ресурсов](diagnostic-logs-schema.md) и [схеме журнала активности.](activity-log-schema.md)

``` JSON
{"time": "2016-07-01T00:00:37.2040000Z","systemId": "46cdbb41-cb9c-4f3d-a5b4-1d458d827ff1","category": "NetworkSecurityGroupRuleCounter","resourceId": "/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/TESTNSG","operationName": "NetworkSecurityGroupCounters","properties": {"vnetResourceGuid": "{12345678-9012-3456-7890-123456789012}","subnetPrefix": "10.3.0.0/24","macAddress": "000123456789","ruleName": "/subscriptions/ s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg/securityRules/default-allow-rdp","direction": "In","type": "allow","matchedConnections": 1988}}
```

> [!NOTE]
> Платформные журналы записываются на хранение капли с помощью [линий JSON,](http://jsonlines.org/)где каждое событие является строкой, а новый символ указывает на новое событие. Этот формат был реализован в ноябре 2018 года. До этой даты журналы были записаны на хранение кабов в качестве массива записей json, описанных в [журнале «Подготовка к изменению формата» в журналы платформы Azure Monitor, архивированные на учетную запись хранения.](resource-logs-blob-format.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Подробнее о журналах ресурсов.](platform-logs-overview.md)
* [Создайте диагностическую настройку для сбора журналов и метрик в Azure.](diagnostic-settings.md)
* [Скачать капли для анализа](../../storage/blobs/storage-quickstart-blobs-dotnet.md).
* [Архив журналов Active Directory Azure с помощью Azure Monitor](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md).
