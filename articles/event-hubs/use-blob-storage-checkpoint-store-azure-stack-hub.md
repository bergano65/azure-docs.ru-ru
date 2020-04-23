---
title: Используйте хранилище Blob в качестве хранилища контрольной точки на azure Stack Hub (предварительный просмотр)
description: В этой статье описывается, как использовать Blob Storage в качестве хранилища в концентрах событий в azure Stack Hub (предварительный просмотр).
services: event-hubs
documentationcenter: na
author: spelluru
ms.service: event-hubs
ms.topic: how-to
ms.date: 03/18/2020
ms.author: spelluru
ms.openlocfilehash: 2938099383c32eac493e4b4bb620f03c76ca5c44
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "82023671"
---
# <a name="use-blob-storage-as-checkpoint-store---event-hubs-on-azure-stack-hub-preview"></a>Используйте Хранилище Blob в качестве хранилища контрольной точки - Концентраторы событий на azure Stack Hub (предварительный просмотр)
Если вы используете Хранилище Azure Blob Storage в качестве хранилища в среде, поддерживающей другую версию Storage Blob SDK, чем те, которые обычно доступны в Azure, необходимо использовать код для изменения aPI-версии службы хранения данных на конкретную версию, поддерживаемую этой средой. Например, если вы работаете с [концентраторами событий в версии Azure Stack Hub 2002](https://docs.microsoft.com/azure-stack/user/event-hubs-overview)года, наивысшей доступной версией для службы хранения является версия 2017-11-09. В этом случае необходимо использовать код для таргетинга на версию API службы хранения данных на 2017-11-09 годы. Для примера таргетинга на конкретную версию API хранилища см. 

- [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/Sample10_RunningWithDifferentStorageVersion.cs)
- [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorWithCustomStorageVersion.java). 
- [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/javascript/receiveEventsWithApiSpecificStorage.js) или [TypeScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/typescript/src/receiveEventsWithApiSpecificStorage.ts) 
- Python - [синхронный,](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob/samples/receive_events_using_checkpoint_store_storage_api_version.py) [асинхронный](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/receive_events_using_checkpoint_store_storage_api_version_async.py)

> [!IMPORTANT]
> Концентраторы событий в azure Stack Hub в настоящее время находятся в [предварительном просмотре](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) и бесплатны. 

Если вы запустите приемник Event Hubs, который использует Хранилище Blob в качестве хранилища контрольной точки без таргетинга на версию, поддерживаемую Azure Stack Hub, вы получите следующее сообщение об ошибке:

```
The value for one of the HTTP headers is not in the correct format
```


## <a name="sample-error-message-in-python"></a>Сообщение об ошибке образца в Python
Для Python ошибка `azure.core.exceptions.HttpResponseError` передается обработчику ошибок `on_error(partition_context, error)` `EventHubConsumerClient.receive()`. Но метод `receive()` не вызывает исключения. `print(error)`распечатать следующую информацию об исключении:

```bash
The value for one of the HTTP headers is not in the correct format.

RequestId:f048aee8-a90c-08ba-4ce1-e69dba759297
Time:2020-03-17T22:04:13.3559296Z
ErrorCode:InvalidHeaderValue
Error:None
HeaderName:x-ms-version
HeaderValue:2019-07-07
```

Регистратор будет регистрировать два предупреждения, как следующие из них:

```bash
WARNING:azure.eventhub.extensions.checkpointstoreblobaio._blobstoragecsaio: 
An exception occurred during list_ownership for namespace '<namespace-name>.eventhub.<region>.azurestack.corp.microsoft.com' eventhub 'python-eh-test' consumer group '$Default'. 

Exception is HttpResponseError('The value for one of the HTTP headers is not in the correct format.\nRequestId:f048aee8-a90c-08ba-4ce1-e69dba759297\nTime:2020-03-17T22:04:13.3559296Z\nErrorCode:InvalidHeaderValue\nError:None\nHeaderName:x-ms-version\nHeaderValue:2019-07-07')

WARNING:azure.eventhub.aio._eventprocessor.event_processor:EventProcessor instance '26d84102-45b2-48a9-b7f4-da8916f68214' of eventhub 'python-eh-test' consumer group '$Default'. An error occurred while load-balancing and claiming ownership. 

The exception is HttpResponseError('The value for one of the HTTP headers is not in the correct format.\nRequestId:f048aee8-a90c-08ba-4ce1-e69dba759297\nTime:2020-03-17T22:04:13.3559296Z\nErrorCode:InvalidHeaderValue\nError:None\nHeaderName:x-ms-version\nHeaderValue:2019-07-07'). Retrying after 71.45254944090853 seconds
```



## <a name="next-steps"></a>Дальнейшие действия

Смотрите следующую статью, узнать о разделении и контрольной точки: [Баланс нагрузки раздела в нескольких экземплярах приложения](event-processor-balance-partition-load.md)
