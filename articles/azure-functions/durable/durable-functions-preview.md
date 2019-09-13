---
title: Функции предварительной версии Устойчивые функции — функции Azure
description: Дополнительные сведения о функциях предварительной версии для Устойчивые функции.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: article
ms.date: 09/04/2019
ms.author: azfuncdf
ms.openlocfilehash: 8f2560141eac4e7d9fed267d347990e65bfe9c26
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/12/2019
ms.locfileid: "70933245"
---
# <a name="durable-functions-20-preview-azure-functions"></a>Предварительная версия Устойчивые функции 2,0 (функции Azure)

*Устойчивые функции* являются расширением [Функций Azure](../functions-overview.md) и [веб-заданий Azure](../../app-service/web-sites-create-web-jobs.md), которое позволяет писать функции с отслеживанием состояния в безсерверной среде. Расширение автоматически управляет состоянием, создает контрольные точки и перезагружается. Если вы еще не знакомы с Устойчивые функции, см. [обзорную документацию](durable-functions-overview.md).

Устойчивые функции 1. x — это общедоступная (публичная) функция функций Azure, но также содержит несколько подкомпонентов, которые в настоящее время доступны в общедоступной предварительной версии. В этой статье описываются недавно выпущенные функции предварительной версии и приводятся подробные сведения о том, как они работают и как их можно приступить к использованию.

> [!NOTE]
> Эти функции предварительной версии являются частью выпуска Устойчивые функции 2,0, который в настоящее время является предварительным выпуском с несколькими критическими изменениями. Сборки пакета устойчивых расширений для функций Azure можно найти в nuget.org с версиями в формате **2.0.0-бетакс**. Эти сборки не предназначены для рабочих нагрузок, а последующие выпуски могут содержать дополнительные критические изменения.

## <a name="breaking-changes"></a>Критические изменения

В Устойчивые функции 2,0 введено несколько критических изменений. Существующие приложения не должны быть совместимы с Устойчивые функции 2,0 без изменений кода. В этом разделе перечислены некоторые из этих изменений.

### <a name="hostjson-schema"></a>Схема Host. JSON

В следующем фрагменте кода показана новая схема для Host. JSON. Основные изменения, о которых следует помнить, — это новые подразделы:

* `"storageProvider"`(и `"azureStorage"` подраздела) для конфигурации, относящейся к хранилищу
* `"tracking"`для конфигурации отслеживания и ведения журнала
* `"notifications"`(и `"eventGrid"` подраздела) для конфигурации уведомлений сетки событий

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "azureStorage": {
          "connectionStringName": <string>,
          "controlQueueBatchSize": <int?>,
          "partitionCount": <int?>,
          "controlQueueVisibilityTimeout": <hh:mm:ss?>,
          "workItemQueueVisibilityTimeout": <hh:mm:ss?>,
          "trackingStoreConnectionStringName": <string?>,
          "trackingStoreNamePrefix": <string?>,
          "maxQueuePollingInterval": <hh:mm:ss?>
        }
      },
      "tracking": {
        "traceInputsAndOutputs": <bool?>,
        "traceReplayEvents": <bool?>,
      },
      "notifications": {
        "eventGrid": {
          "topicEndpoint": <string?>,
          "keySettingName": <string?>,
          "publishRetryCount": <string?>,
          "publishRetryInterval": <hh:mm:ss?>,
          "publishRetryHttpStatus": <int[]?>,
          "publishEventTypes": <string[]?>,
        }
      },
      "maxConcurrentActivityFunctions": <int?>,
      "maxConcurrentOrchestratorFunctions": <int?>,
      "extendedSessionsEnabled": <bool?>,
      "extendedSessionIdleTimeoutInSeconds": <int?>,
      "customLifeCycleNotificationHelperType": <string?>
  }
}
```

Так как устойчивые функции 2,0 остается неустойчивой, в `durableTask` раздел Host. JSON будут внесены дополнительные изменения. Дополнительные сведения об этих изменениях см. в [этой ошибке GitHub](https://github.com/Azure/azure-functions-durable-extension/issues/641).

### <a name="public-interface-changes"></a>Изменения в открытом интерфейсе

Различные объекты "Context", поддерживаемые Устойчивые функции, имели абстрактные базовые классы, предназначенные для использования при модульном тестировании. В рамках Устойчивые функции 2,0 эти абстрактные базовые классы были заменены интерфейсами. Код функции, непосредственно использующий конкретные типы, не затрагивается.

В следующей таблице представлены основные изменения.

| Старый тип | Новый тип |
|----------|----------|
| DurableOrchestrationClientBase | идураблеорчестратионклиент |
| дураблеорчестратионконтекстбасе | идураблеорчестратионконтекст |
| дураблеактивитиконтекстбасе | идураблеактивитиконтекст |
| орчестратионклиентаттрибуте | дураблеклиентаттрибуте |

В случае, когда абстрактный базовый класс содержал виртуальные методы, эти виртуальные методы были заменены методами расширения, определенными в `DurableContextExtensions`.

## <a name="entity-functions"></a>Функции сущностей

Начиная с Устойчивые функции v 2.0.0-Alpha мы предоставили новую концепцию [функций сущностей](durable-functions-entities.md) .

Функции сущностей определяют операции чтения и обновления мелких частей состояния, известных как *устойчивые сущности*. Как и функции Orchestrator, функции сущностей — это функции с особым типом триггера, *триггером сущности*. В отличие от функций Orchestrator, функции сущностей не имеют конкретных ограничений кода. Функции сущностей также управляют состоянием явным образом, а не неявно представляют состояние с помощью потока управления.

В соответствии с исходными отзывами пользователей мы добавили поддержку модели программирования на основе классов для сущностей в Устойчивые функции v 2.0.0-Beta1.

Дополнительные сведения см. в статье о [функциях сущностей](durable-functions-entities.md) .

## <a name="durable-http"></a>Устойчивый протокол HTTP

Начиная с версии Устойчивые функции v 2.0.0-бета мы предоставили новую [устойчивую функцию HTTP](durable-functions-http-features.md) , которая позволяет:

* Вызывайте API HTTP непосредственно из функций оркестрации (с некоторыми задокументированными ограничениями).
* Реализует опрос состояния HTTP 202 на стороне клиента
* Встроенная поддержка [управляемых удостоверений Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

Дополнительные сведения см. в статье о [функциях HTTP](durable-functions-http-features.md#consuming-http-apis) .

## <a name="alternate-storage-providers"></a>Альтернативные поставщики хранилища

В настоящее время платформа устойчивых задач поддерживает несколько поставщиков хранилища, включая службу [хранилища Azure](https://github.com/Azure/durabletask/tree/master/src/DurableTask.AzureStorage), [служебную шину Azure](https://github.com/Azure/durabletask/tree/master/src/DurableTask.ServiceBus), [эмулятор в памяти](https://github.com/Azure/durabletask/tree/master/src/DurableTask.Emulator)и экспериментальный поставщик [Redis](https://github.com/Azure/durabletask/tree/redis/src/DurableTask.Redis) . Однако до настоящего момента расширение устойчивых задач для функций Azure поддерживало только поставщик хранилища Azure. Начиная с Устойчивые функции 2,0, добавляется поддержка альтернативных поставщиков хранилища, начиная с поставщика Redis.

> [!NOTE]
> Устойчивые функции 2,0 поддерживает только .NET Standard 2,0-совместимые поставщики.

### <a name="emulator"></a>эмулятор

Поставщик [DurableTask. Emulator](https://www.nuget.org/packages/Microsoft.Azure.DurableTask.Emulator/) — это локальная память, неустойчивый поставщик хранилища, подходящий для локальных сценариев тестирования. Ее можно настроить с помощью следующей минимальной схемы **Host. JSON** :

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "emulator": { "enabled": true }
      }
    }
  }
}
```

### <a name="redis-experimental"></a>Redis (экспериментальная версия)

Поставщик [DurableTask. Redis](https://www.nuget.org/packages/Microsoft.Azure.DurableTask.Redis/) сохраняет все состояния оркестрации в настроенном кластере Redis.

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "redis": {
          "connectionStringName": <string>,
        }
      }
    }
  }
}
```

Объект `connectionStringName` должен ссылаться на имя параметра приложения или переменной среды. Этот параметр приложения или переменная среды должны содержать значение строки подключения Redis в формате *"сервер: порт*". Например, `localhost:6379` для подключения к локальному кластеру Redis.

> [!NOTE]
> Поставщик Redis в настоящее время экспериментальен и поддерживает только приложения-функции, выполняющиеся на одном узле. Не гарантируется, что поставщик Redis становится общедоступным и может быть удален в будущем выпуске.
