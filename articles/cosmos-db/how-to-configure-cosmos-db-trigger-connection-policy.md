---
title: Триггер Функций Azure для политики подключения Cosmos DB
description: Из этой статьи вы узнаете, как настроить политику подключения, которая используется триггером Функций Azure для Cosmos DB.
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: maquaran
ms.openlocfilehash: 6e4c50e5dcc35450463d02bbed040754ea778e70
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70093619"
---
# <a name="how-to-configure-the-connection-policy-used-by-azure-functions-trigger-for-cosmos-db"></a>Настройка политики подключения, используемой триггером Функций Azure для Cosmos DB

В этой статье описывается, как настроить политику подключения при использовании триггера Функций Azure для Cosmos DB с целью подключения к учетной записи Azure Cosmos.

## <a name="why-is-the-connection-policy-important"></a>Почему политика подключения так важна?

Существует два режима подключения: режим прямого подключения и режим шлюза. Дополнительные сведения об этих режимах подключения см. в статье [Советы по улучшению производительности](./performance-tips.md#networking). По умолчанию для установки всех подключений на триггере Функций Azure для Cosmos DB используется **шлюз**. Тем не менее, это может быть не лучшим вариантом для сценариев, ориентированных на производительность.

## <a name="changing-the-connection-mode-and-protocol"></a>Изменение режима и протокола подключения

Для настройки политики подключения для клиента используются два основных параметра конфигурации: **режим** и **протокол** подключения. Вы можете изменить режим подключения по умолчанию и протокол, который используется триггером Функций Azure для Cosmos DB и всеми [привязками Azure Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2.md#output). Чтобы изменить параметры по умолчанию, вам потребуется найти `host.json` в своем проекте или приложении Функций Azure и добавить следующий [дополнительный параметр](../azure-functions/functions-bindings-cosmosdb-v2.md#hostjson-settings).

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

## <a name="next-steps"></a>Следующие шаги

* [Ограничение числа подключений](../azure-functions/manage-connections.md#connection-limit)
* [Советы по повышению производительности для .NET в Azure Cosmos DB](./performance-tips.md)
* [Примеры кода](https://github.com/ealsur/serverless-recipes/tree/master/connectionmode)
