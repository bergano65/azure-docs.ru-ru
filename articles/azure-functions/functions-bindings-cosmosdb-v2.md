---
title: Привязки Azure Cosmos DB для службы "Функции" версии 2.х
description: Узнайте, как использовать триггеры и привязки Azure Cosmos DB в службе "Функции Azure".
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2017
ms.author: cshoe
ms.openlocfilehash: f258a7aff52796a53540706bc8413575d63c9e7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77605761"
---
# <a name="azure-cosmos-db-trigger-and-bindings-for-azure-functions-2x-overview"></a>Триггер и привязки Azure Cosmos DB для обзора Azure Functions 2.x

> [!div class="op_single_selector" title1="Выберите версию времени выполнения функций Azure, которые вы используете: "]
> * [Версия 1](functions-bindings-cosmosdb.md)
> * [Версия 2](functions-bindings-cosmosdb-v2.md)

В этом наборе статей объясняется, как работать с привязками [Azure Cosmos DB](../cosmos-db/serverless-computing-database.md) в Azure Functions 2.x. Служба "Функции Azure" поддерживает привязки триггера, а также входные и выходные привязки для Azure Cosmos DB.

| Действие | Тип |
|---------|---------|
| Выполнить функцию при создании или изменении документа Azure Cosmos DB | [Триггер](./functions-bindings-cosmosdb-v2-trigger.md) |
| Прочитайте документ DB Azure Cosmos | [Связывание входных вхоза](./functions-bindings-cosmosdb-v2-input.md) |
| Сохранение изменений в dB-документе Azure Cosmos  |[Выходная привязка](./functions-bindings-cosmosdb-v2-output.md) |

> [!NOTE]
> Эта ссылка предназначена для [версии Azure Functions 2.x](functions-versions.md).  Для получения дополнительных сведений об использовании Функций версии 1.x см. раздел [Функции 1.х](functions-bindings-cosmosdb.md).
>
> Эта привязка называлась DocumentDB. В Функциях версии 2.x триггер, привязки и пакет вместе называются Cosmos DB.

## <a name="supported-apis"></a>Поддерживаемые API

[!INCLUDE [SQL API support only](../../includes/functions-cosmosdb-sqlapi-note.md)]

## <a name="add-to-your-functions-app"></a>Добавление в приложение Функции

### <a name="functions-2x-and-higher"></a>Функции 2.x и выше

Работа с триггером и привязками требует, чтобы вы ссылали соответствующий пакет. Пакет NuGet используется для библиотек класса .NET, в то время как пакет расширения используется для всех других типов приложений.

| Язык                                        | Добавить по...                                   | Remarks 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Установка [пакета NuGet,]версия 3.x | |
| Си-скрипт, Java, JavaScript, Python, PowerShell | Регистрация [пакета расширения]          | [Расширение Azure Tools] рекомендуется использовать с помощью Visual Studio Code. |
| Си-скрипт (онлайн-портал Azure)         | Добавление связывания                            | Чтобы обновить существующие обязательные расширения без необходимости переиздания приложения функции, [см.] |

[Пакет NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB
[core tools]: ./functions-run-local.md
[расширение расслоение]: ./functions-bindings-register.md#extension-bundles
[Обновление расширений]: ./install-update-binding-extensions-manual.md
[Расширение Azure Tools]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Функции 1.x

Функции 1.x приложения автоматически имеют ссылку на пакет [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet, версия 2.x.

## <a name="next-steps"></a>Дальнейшие действия

- [Выполнить функцию при создании или изменении документа Azure Cosmos DB (Trigger)](./functions-bindings-cosmosdb-v2-trigger.md)
- [Прочитайте документ Azure Cosmos DB (связывание ввода)](./functions-bindings-cosmosdb-v2-input.md)
- [Сохранение изменений в документе Azure Cosmos DB (связка выхода)](./functions-bindings-cosmosdb-v2-output.md)
