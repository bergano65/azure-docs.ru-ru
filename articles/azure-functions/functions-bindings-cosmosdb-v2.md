---
title: Привязки Azure Cosmos DB для службы "Функции" версии 2.х
description: Узнайте, как использовать триггеры и привязки Azure Cosmos DB в службе "Функции Azure".
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2017
ms.author: cshoe
ms.openlocfilehash: f258a7aff52796a53540706bc8413575d63c9e7d
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77605761"
---
# <a name="azure-cosmos-db-trigger-and-bindings-for-azure-functions-2x-overview"></a>Обзор Azure Cosmos DB триггеров и привязок для функций Azure 2. x

> [!div class="op_single_selector" title1="Выберите версию используемой среды выполнения функций Azure: "]
> * [Версия 1](functions-bindings-cosmosdb.md)
> * [Версия 2](functions-bindings-cosmosdb-v2.md)

В этом наборе статей объясняется, как работать с привязками [Azure Cosmos DB](../cosmos-db/serverless-computing-database.md) в функциях Azure 2. x. Служба "Функции Azure" поддерживает привязки триггера, а также входные и выходные привязки для Azure Cosmos DB.

| Действие | Тип |
|---------|---------|
| Выполнение функции при создании или изменении документа Azure Cosmos DB | [Триггер](./functions-bindings-cosmosdb-v2-trigger.md) |
| Чтение Azure Cosmos DB документа | [Входная привязка](./functions-bindings-cosmosdb-v2-input.md) |
| Сохранение изменений в документе Azure Cosmos DB  |[Выходная привязка](./functions-bindings-cosmosdb-v2-output.md) |

> [!NOTE]
> Эта ссылка предназначена для [функций Azure версии 2. x](functions-versions.md).  Для получения дополнительных сведений об использовании Функций версии 1.x см. раздел [Функции 1.х](functions-bindings-cosmosdb.md).
>
> Эта привязка называлась DocumentDB. В Функциях версии 2.x триггер, привязки и пакет вместе называются Cosmos DB.

## <a name="supported-apis"></a>Поддерживаемые API

[!INCLUDE [SQL API support only](../../includes/functions-cosmosdb-sqlapi-note.md)]

## <a name="add-to-your-functions-app"></a>Добавление в приложение функций

### <a name="functions-2x-and-higher"></a>Функции 2. x и более поздних версий

Для работы с триггером и привязками требуется ссылка на соответствующий пакет. Пакет NuGet используется для библиотек классов .NET, в то время как набор расширений используется для всех других типов приложений.

| Язык                                        | Добавить по...                                   | Remarks 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Установка [Пакет NuGet], версия 3. x | |
| C#Script, Java, JavaScript, Python, PowerShell | Регистрация [Пакет расширений]          | [Расширение "инструменты Azure"] " рекомендуется использовать с Visual Studio Code. |
| C#Скрипт (только в интерактивном режиме в портал Azure)         | Добавление привязки                            | Чтобы обновить существующие расширения привязки без повторной публикации приложения функции, см. статью [Обновление расширений]. |

[Пакет NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB.
[core tools]: ./functions-run-local.md
[Пакет расширений]: ./functions-bindings-register.md#extension-bundles
[Обновление расширений]: ./install-update-binding-extensions-manual.md
[Расширение "инструменты Azure"]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Функции 1.x

Функции 1. x автоматически имеют ссылку на пакет NuGet [Microsoft. Azure. веб-задания](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) , версия 2. x.

## <a name="next-steps"></a>Дальнейшие действия

- [Выполнение функции при создании или изменении документа Azure Cosmos DB (триггер)](./functions-bindings-cosmosdb-v2-trigger.md)
- [Чтение Azure Cosmos DB документа (входная привязка)](./functions-bindings-cosmosdb-v2-input.md)
- [Сохранение изменений в документе Azure Cosmos DB (Выходная привязка)](./functions-bindings-cosmosdb-v2-output.md)
