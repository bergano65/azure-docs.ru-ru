---
title: Триггер и привязки хранилища BLOB-объектов Azure для функций Azure
description: Узнайте, как использовать триггер и привязки хранилища BLOB-объектов Azure в функциях Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.openlocfilehash: e56d1add36d4296526348d12d7c0b6eb03108f27
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/16/2020
ms.locfileid: "92104365"
---
# <a name="azure-blob-storage-bindings-for-azure-functions-overview"></a>Общие сведения о привязках хранилища BLOB-объектов Azure для функций Azure

Функции Azure интегрируются с [хранилищем Azure](../storage/index.yml) с помощью [триггеров и привязок](./functions-triggers-bindings.md). Интеграция с хранилищем BLOB-объектов позволяет создавать функции, реагирующие на изменения в данных больших двоичных объектов, а также значения чтения и записи.

| Действие | Тип |
|---------|---------|
| Выполнение функции в качестве изменений данных хранилища BLOB-объектов | [Триггер](./functions-bindings-storage-blob-trigger.md) |
| Чтение данных хранилища BLOB-объектов в функции | [Входная привязка](./functions-bindings-storage-blob-input.md) |
| Разрешить функции записывать данные хранилища BLOB-объектов |[Выходная привязка](./functions-bindings-storage-blob-output.md) |

## <a name="add-to-your-functions-app"></a>Добавление в приложение функций

### <a name="functions-2x-and-higher"></a>Функции 2.x и более поздних версий

Для работы с триггером и привязками требуется ссылка на соответствующий пакет. Пакет NuGet используется для библиотек классов .NET, в то время как набор расширений используется для всех других типов приложений.

| Язык                                        | Добавить по...                                   | Комментарии 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Установка [пакета NuGet], версия 3. x | |
| Скрипт C#, Java, JavaScript, Python, PowerShell | Регистрация [пакета расширений]          | [Расширение "инструменты Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) " рекомендуется использовать с Visual Studio Code. |
| Скрипт C# (только в сети портал Azure)         | Добавление привязки                            | Чтобы обновить существующие расширения привязки без повторной публикации приложения функции, см. статью [Обновление расширений]. |

[core tools]: ./functions-run-local.md
[Пакет расширений]: ./functions-bindings-register.md#extension-bundles
[Пакет NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage
[Обновление расширений]: ./functions-bindings-register.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Функции 1.x

Функции 1. x автоматически имеют ссылку на пакет NuGet [Microsoft. Azure. веб-задания](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) , версия 2. x.

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="next-steps"></a>Дальнейшие действия

- [Выполнение функции при изменении данных хранилища BLOB-объектов](./functions-bindings-storage-blob-trigger.md)
- [Чтение данных хранилища BLOB-объектов при выполнении функции](./functions-bindings-storage-blob-input.md)
- [Запись данных хранилища BLOB-объектов из функции](./functions-bindings-storage-blob-output.md)