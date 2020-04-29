---
title: Триггер и привязки хранилища BLOB-объектов Azure для функций Azure
description: Узнайте, как использовать триггер и привязки хранилища BLOB-объектов Azure в функциях Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.openlocfilehash: 6950f1dd81ef2c70f2c45fb4c547ed7676067790
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79277236"
---
# <a name="azure-blob-storage-bindings-for-azure-functions-overview"></a>Общие сведения о привязках хранилища BLOB-объектов Azure для функций Azure

Функции Azure интегрируются с [хранилищем Azure](https://docs.microsoft.com/azure/storage/) с помощью [триггеров и привязок](./functions-triggers-bindings.md). Интеграция с хранилищем BLOB-объектов позволяет создавать функции, реагирующие на изменения в данных больших двоичных объектов, а также значения чтения и записи.

| Действие | Type |
|---------|---------|
| Выполнение функции в качестве изменений данных хранилища BLOB-объектов | [Триггер](./functions-bindings-storage-blob-trigger.md) |
| Чтение данных хранилища BLOB-объектов в функции | [Входная привязка](./functions-bindings-storage-blob-input.md) |
| Разрешить функции записывать данные хранилища BLOB-объектов |[Выходная привязка](./functions-bindings-storage-blob-output.md) |

## <a name="add-to-your-functions-app"></a>Добавление в приложение функций

### <a name="functions-2x-and-higher"></a>Функции 2. x и более поздних версий

Для работы с триггером и привязками требуется ссылка на соответствующий пакет. Пакет NuGet используется для библиотек классов .NET, в то время как набор расширений используется для всех других типов приложений.

| Язык                                        | Добавить по...                                   | Remarks 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Установка [пакета NuGet], версия 3. x | |
| Скрипт C#, Java, JavaScript, Python, PowerShell | Регистрация [пакета расширений]          | [Расширение "инструменты Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) " рекомендуется использовать с Visual Studio Code. |
| Скрипт C# (только в сети портал Azure)         | Добавление привязки                            | Чтобы обновить существующие расширения привязки без повторной публикации приложения функции, см. статью [Обновление расширений]. |

[core tools]: ./functions-run-local.md
[Пакет расширений]: ./functions-bindings-register.md#extension-bundles
[Пакет NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage
[Обновление расширений]: ./install-update-binding-extensions-manual.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Функции 1.x

Функции 1. x автоматически имеют ссылку на пакет NuGet [Microsoft. Azure. веб-задания](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) , версия 2. x.

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="next-steps"></a>Дальнейшие шаги

- [Выполнение функции при изменении данных хранилища BLOB-объектов](./functions-bindings-storage-blob-trigger.md)
- [Чтение данных хранилища BLOB-объектов при выполнении функции](./functions-bindings-storage-blob-input.md)
- [Запись данных хранилища BLOB-объектов из функции](./functions-bindings-storage-blob-output.md)
