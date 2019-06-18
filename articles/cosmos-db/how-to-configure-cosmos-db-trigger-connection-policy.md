---
title: Политика подключения триггера Azure Cosmos DB
description: Сведения о настройке политики подключения, которая используется триггером Azure Cosmos DB
author: ealsur
ms.service: cosmos-db
ms.topic: sample
ms.date: 06/05/2019
ms.author: maquaran
ms.openlocfilehash: 584d59884b70d2ee8243216e6f907fc9ec2d8ad4
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/07/2019
ms.locfileid: "66755462"
---
# <a name="how-to-configure-the-connection-policy-used-by-azure-cosmos-db-trigger"></a>Настройка политики подключения, которая используется триггером Azure Cosmos DB

В этой статье описывается настройка политики подключения при использовании триггера Azure Cosmos DB для подключения к учетной записи Azure Cosmos.

## <a name="why-is-the-connection-policy-important"></a>Почему политика подключения так важна?

Существует два режима подключения: режим прямого подключения и режим шлюза. Дополнительные сведения об этих режимах подключения см. в статье [Советы по улучшению производительности](./performance-tips.md#networking). По умолчанию **шлюз** используется для установки подключений на триггер Azure Cosmos DB. Тем не менее, это может быть не лучшим вариантом для сценариев, ориентированных на производительность.

## <a name="changing-the-connection-mode-and-protocol"></a>Изменение режима и протокола подключения

Для настройки политики подключения для клиента используются два основных параметра конфигурации: **режим** и **протокол** подключения. Вы можете изменить режим подключения по умолчанию и протокол, который используется триггером Azure Cosmos DB и всеми [привязками Azure Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2.md#output). Чтобы изменить параметры по умолчанию, вам потребуется найти `host.json` в своем проекте или приложении Функций Azure и добавить следующий [дополнительный параметр](../azure-functions/functions-bindings-cosmosdb-v2.md#hostjson-settings).

```js
{
  "cosmosDB": {
    "connectionMode": "Direct",
    "protocol": "Tcp"
  }
}
```

В `connectionMode` должен быть указан необходимый режим подключения (прямое подключение или шлюз), а в `protocol` — желаемый протокол подключения (Tcp или Https). 

Если проект Функций Azure работает со средой выполнения Функций Azure версии 1, в имени конфигурации есть небольшое различие. Вместо `cosmosDB` следует использовать `documentDB`.

```js
{
  "documentDB": {
    "connectionMode": "Direct",
    "protocol": "Tcp"
  }
}
```

> [!NOTE]
> При работе с планом размещения и потребления в службе "Функции Azure" каждый экземпляр будет иметь ограничение объема подключения через сокет, который он может поддерживать. При работе с режимом прямого подключения или протоколом TCP создается больше подключений, которые могут достигать предела [плана потребления](../azure-functions/manage-connections.md#connection-limit), и в этом случае вы можете использовать режим шлюза или запустить службу "Функции Azure" в [режиме Службы приложений](../azure-functions/functions-scale.md#app-service-plan).

## <a name="next-steps"></a>Дополнительная информация

* [Ограничение числа подключений](../azure-functions/manage-connections.md#connection-limit)
* [Советы по повышению производительности для .NET в Azure Cosmos DB](./performance-tips.md)
* [Примеры кода](https://github.com/ealsur/serverless-recipes/tree/master/connectionmode)
