---
title: Мониторинг журналов диагностики служб мультимедиа с помощью Azure Monitor | Документация Майкрософт
description: В этой статье показано, как для маршрутизации и Просмотр журналов диагностики с помощью Azure Monitor.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2019
ms.author: juliako
ms.openlocfilehash: 233b043ffdc295fe94ed2e3ba837d4229848df22
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795841"
---
# <a name="monitor-media-services-diagnostic-logs"></a>Мониторинг журналов диагностики службы мультимедиа

[Azure Monitor](../../azure-monitor/overview.md) включает мониторинг метрик и журналов диагностики, которые помогут вам понять, как выполняемых приложений. Подробное описание этого компонента и почему нужно использовать службы мультимедиа Azure метрики и журналы диагностики, см. в разделе [служб мультимедиа монитор метрики и журналы диагностики](media-services-metrics-diagnostic-logs.md).

В этой статье показано, как для маршрутизации данных в учетную запись хранения и просмотра данных. 

## <a name="prerequisites"></a>предварительные требования

- [Создание учетной записи Служб мультимедиа](create-account-cli-how-to.md).
- Просмотрите [служб мультимедиа монитор метрики и журналы диагностики](media-services-metrics-diagnostic-logs.md).

## <a name="route-data-to-the-storage-account-using-the-portal"></a>Отправка данных в учетную запись хранения с помощью портала

1. Войдите на портал Azure по адресу https://portal.azure.com.
1. Перейдите к учетной записи служб мультимедиа в и щелкните **параметров диагностики** под **монитор**. Здесь можно увидеть список всех ресурсов в подписке, создающих данные мониторинга с помощью Azure Monitor. 

    ![Раздел параметров диагностики](media/media-services-diagnostic-logs/logs01.png)

1. Нажмите кнопку **добавьте параметр диагностики**.

   Параметр диагностики ресурса — это определение того, *какие* данные мониторинга нужно маршрутизировать из определенного ресурса и *куда* их необходимо отправить.

1. В открывшемся разделе назначьте параметру **имя** и установите флажок **Archive to a storage account** (Архивировать в учетную запись хранения).

    Выберите учетную запись хранения, к которому вы хотите отправлять журналы и нажмите клавишу **ОК**.
1. Установите все флажки в разделе **Журнал** и **Метрика**. В зависимости от типа ресурса у вас может быть только один из этих вариантов. Эти флажки определяют, какие категории журналов и данных метрик, доступные для этого типа ресурса, отправляются в выбранное место назначения (в данном случае в учетную запись хранения).

   ![Раздел параметров диагностики](media/media-services-diagnostic-logs/logs02.png)
1. Задайте для ползунка **Хранение (в днях)** значение 30. Этот ползунок задает количество дней хранения данных мониторинга в учетной записи хранения. Azure Monitor автоматически удаляет данные, которые хранятся дольше заданного количества дней. Нулевое значение для периода хранения означает, что данные будут храниться неограниченно долго.
1. Нажмите кнопку **Сохранить**.

Данные мониторинга из ресурса теперь поступают в учетную запись хранения.

## <a name="route-data-to-the-storage-account-using-the-cli"></a>Отправка данных в учетную запись хранения с помощью интерфейса командной строки

Чтобы включить журналов диагностики в учетную запись хранения, необходимо выполнить следующие `az monitor diagnostic-settings` команду интерфейса командной строки: 

```cli
az monitor diagnostic-settings create --name <diagnostic name> \
    --storage-account <name or ID of storage account> \
    --resource <target resource object ID> \
    --resource-group <storage account resource group> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true,
        "retentionPolicy": {
            "days": <# days to retain>,
            "enabled": true
        }
    }]'
```

Пример:

```cli
az monitor diagnostic-settings create --name amsv3diagnostic \
    --storage-account storageaccountforams  \
    --resource "/subscriptions/00000000-0000-0000-0000-0000000000/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount" \
    --resource-group "amsResourceGroup" \
    --logs '[{"category": "KeyDeliveryRequests",  "enabled": true, "retentionPolicy": {"days": 3, "enabled": true }}]'
```

## <a name="view-data-in-the-storage-account-using-the-portal"></a>Просмотр данных в учетную запись хранения с помощью портала

Если выполнены предыдущие шаги, данные будут поступать в учетную запись хранения.

Событие может появиться в учетной записи через пять минут.

1. На портале перейдите к разделу **Учетные записи хранения**, который можно найти в области навигации слева.
1. Идентифицируйте учетную запись хранения, созданную в предыдущем разделе, и щелкните ее.
1. Щелкните **большие двоичные объекты**, а затем контейнер с меткой **insights журналы keydeliveryrequests**. Это контейнер, содержащий журналы в нем. Данные мониторинга разбиты на контейнеры по Идентификатору ресурса, затем по дате и времени.
1. Перейдите к файлу PT1H.json, щелкнув контейнеры, чтобы получить идентификаторы ресурса, дату и время. Щелкните файл PT1H.json, а затем — **Скачать**.

 Теперь можно просмотреть событие JSON, сохраненное в учетной записи хранения.

### <a name="examples-of-pt1hjson"></a>Примеры PT1H.json

#### <a name="clear-key-delivery-log"></a>Незащищенный ключ доставки журналов

```json
{
  "time": "2019-05-21T00:07:33.2820450Z",
  "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/amsResourceGroup/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/AMSACCOUNT",
  "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
  "operationVersion": "1.0",
  "category": "KeyDeliveryRequests",
  "resultType": "Succeeded",
  "resultSignature": "OK",
  "durationMs": 253,
  "identity": {
    "authorization": {
      "issuer": "myIssuer",
      "audience": "myAudience"
    },
    "claims": {
      "urn:microsoft:azure:mediaservices:contentkeyidentifier": "e4276e1d-c012-40b1-80d0-ac15808b9277",
      "nbf": "1558396914",
      "exp": "1558400814",
      "iss": "myIssuer",
      "aud": "myAudience"
    }
  },
  "level": "Informational",
  "location": "westus2",
  "properties": {
    "requestId": "fb5c2b3a-bffa-4434-9c6f-73d689649add",
    "keyType": "Clear",
    "keyId": "e4276e1d-c012-40b1-80d0-ac15808b9277",
    "policyName": "SharedContentKeyPolicyUsedByAllAssets",
    "tokenType": "JWT",
    "statusMessage": "OK"
  }
}
```

#### <a name="widevine-encrypted-key-delivery-log"></a>Журнал доставки зашифрованных ключей Widevine

```json
{
  "time": "2019-05-20T23:15:22.7088747Z",
  "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/amsResourceGroup/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/AMSACCOUNT",
  "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
  "operationVersion": "1.0",
  "category": "KeyDeliveryRequests",
  "resultType": "Succeeded",
  "resultSignature": "OK",
  "durationMs": 69,
  "identity": {
    "authorization": {
      "issuer": "myIssuer",
      "audience": "myAudience"
    },
    "claims": {
      "urn:microsoft:azure:mediaservices:contentkeyidentifier": "0092d23a-0a42-4c5f-838e-6d1bbc6346f8",
      "nbf": "1558392430",
      "exp": "1558396330",
      "iss": "myIssuer",
      "aud": "myAudience"
    }
  },
  "level": "Informational",
  "location": "westus2",
  "properties": {
    "requestId": "49613dd2-16aa-4595-a6e0-4e68beae6d37",
    "keyType": "Widevine",
    "keyId": "0092d23a-0a42-4c5f-838e-6d1bbc6346f8",
    "policyName": "DRMContentKeyPolicy",
    "tokenType": "JWT",
    "statusMessage": "OK"
  }
}
```

## <a name="see-also"></a>См. также

* [Метрики Azure Monitor](../../azure-monitor/platform/data-platform.md)
* [Журналы диагностики Azure Monitor](../../azure-monitor/platform/diagnostic-logs-overview.md)
* [Как собирать и использовать данные журнала из ресурсов Azure](../../azure-monitor/platform/diagnostic-logs-overview.md)

## <a name="next-steps"></a>Следующие шаги

[Мониторинг метрик](media-services-metrics-howto.md)
