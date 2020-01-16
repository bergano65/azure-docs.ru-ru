---
title: Архивация журналов ресурсов Azure в учетную запись хранения | Документация Майкрософт
description: Узнайте, как архивировать журналы ресурсов Azure для долгосрочного хранения в учетной записи хранения.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 12/15/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: e99a0e8e42bcfb5c7967a9cb1c91631bacbea53a
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75980079"
---
# <a name="archive-azure-resource-logs-to-storage-account"></a>Архивация журналов ресурсов Azure в учетную запись хранения
[Журналы платформы](platform-logs-overview.md) в Azure, в том числе журнал действий Azure и журналы ресурсов, предоставляют подробные сведения о диагностике и аудите для ресурсов Azure и платформы Azure, от которых они зависят.  В этой статье описывается сбор журналов платформы в учетную запись хранения Azure для хранения данных для архивирования.

## <a name="prerequisites"></a>Технические условия
Если у вас еще нет [учетной записи хранения Azure](../../storage/common/storage-account-create.md) , ее необходимо создать. Учетная запись хранения не обязательно должна находиться в той же подписке, что и журнал, отправляющий журналы, если пользователь, настроив параметр, имеет соответствующий доступ RBAC к обеим подпискам.


> [!IMPORTANT]
> Учетные записи Azure Data Lake Storage 2-го поколения в настоящее время не поддерживаются в качестве места назначения для параметров диагностики, хотя они могут быть указаны в портал Azure в качестве допустимого параметра.


Не следует использовать существующую учетную запись хранения, в которой хранятся другие данные, не относящиеся к мониторингу, чтобы лучше управлять доступом к данным. Если вы выполняете архивацию журналов действий и журналов ресурсов, вы можете использовать одну и ту же учетную запись хранения для централизованного хранения всех данных мониторинга.

## <a name="create-a-diagnostic-setting"></a>Создание параметра диагностики
Отправьте журналы платформы в хранилище и другие места назначения, создав параметр диагностики для ресурса Azure. Дополнительные сведения см. в статье [Создание параметров диагностики для сбора журналов и метрик в Azure](diagnostic-settings.md) .


## <a name="collect-data-from-compute-resources"></a>Получение данных из ресурсов вычислений
Параметры диагностики будут выполнять сбор журналов ресурсов для ресурсов Azure, таких как любые другие ресурсы, но не их гостевой операционной системы или рабочих нагрузок. Чтобы получить эти данные, установите [Агент Windows система диагностики Azure](diagnostics-extension-overview.md). Дополнительные сведения см. [в статье хранение и Просмотр диагностических данных в службе хранилища Azure](diagnostics-extension-to-storage.md) .


## <a name="schema-of-platform-logs-in-storage-account"></a>Схема журналов платформы в учетной записи хранения

После создания параметра диагностики в учетной записи хранения создается контейнер хранилища, как только в одной из включенных категорий журнала будет происходить событие. Большие двоичные объекты в контейнере используют следующее соглашение об именовании:

```
insights-logs-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/RESOURCEGROUPS/{resource group name}/PROVIDERS/{resource provider name}/{resource type}/{resource name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Например, большой двоичный объект для группы безопасности сети может иметь имя, аналогичное следующему:

```
insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUP/TESTNSG/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Каждый большой двоичный объект PT1H.json содержит большой двоичный объект JSON с событиями, произошедшими в течение часа, указанного в URL-адресе этого объекта (например, h=12). В течение заданного часа события добавляются в файл PT1H.json по мере возникновения. Значение минуты (m = 00) всегда равно 00, так как события журнала ресурсов разбиваются на отдельные большие двоичные объекты в час.

В файле PT1H. JSON каждое событие хранится в следующем формате. При этом будет использоваться общая схема верхнего уровня, но она будет уникальной для каждой службы Azure, как описано в статье [журналы ресурсов](diagnostic-logs-schema.md) и [схема журнала действий](activity-log-schema.md).

``` JSON
{"time": "2016-07-01T00:00:37.2040000Z","systemId": "46cdbb41-cb9c-4f3d-a5b4-1d458d827ff1","category": "NetworkSecurityGroupRuleCounter","resourceId": "/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/TESTNSG","operationName": "NetworkSecurityGroupCounters","properties": {"vnetResourceGuid": "{12345678-9012-3456-7890-123456789012}","subnetPrefix": "10.3.0.0/24","macAddress": "000123456789","ruleName": "/subscriptions/ s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg/securityRules/default-allow-rdp","direction": "In","type": "allow","matchedConnections": 1988}}
```

> [!NOTE]
> Журналы платформы записываются в хранилище BLOB-объектов с помощью [строк JSON](http://jsonlines.org/), где каждое событие является линией, а символ новой строки обозначает новое событие. Этот формат был реализован в ноябре 2018. До этой даты журналы были записаны в хранилище BLOB-объектов в виде массива записей JSON, как описано в разделе [Подготовка к изменению формата для Azure Monitor журналов платформы, архивов которых является учетная запись хранения](resource-logs-blob-format.md).

## <a name="next-steps"></a>Дальнейшие действия

* [Дополнительные сведения см. в статье о журналах ресурсов](platform-logs-overview.md).
* [Создайте параметр диагностики для сбора журналов и метрик в Azure](diagnostic-settings.md).
* [Скачайте большие двоичные объекты для анализа](../../storage/blobs/storage-quickstart-blobs-dotnet.md).
* [Архивируйте журналы Azure Active Directory с Azure Monitor](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md).
