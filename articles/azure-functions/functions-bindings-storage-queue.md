---
title: Обзор триггеров и привязок хранилища очередей Azure для функций Azure
description: Узнайте, как использовать триггер службы хранилища очередей Azure и выходную привязку в службе "Функции Azure".
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 16c325f2d6ea78ba980406f56839dd4d0b64bee1
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/20/2020
ms.locfileid: "77482264"
---
# <a name="azure-queue-storage-trigger-and-bindings-for-azure-functions-overview"></a>Обзор триггеров и привязок хранилища очередей Azure для функций Azure

Функции Azure могут выполняться по мере создания новых сообщений хранилища очередей Azure и могут записывать сообщения очереди в функцию.

| Действие | Тип |
|---------|---------|
| Выполнение функции как изменений данных хранилища очередей | [Триггер](./functions-bindings-storage-queue-trigger.md) |
| Сообщения хранилища очереди записи |[Выходная привязка](./functions-bindings-storage-blob-output.md) |

## <a name="add-to-your-functions-app"></a>Добавление в приложение функций

### <a name="functions-2x-and-higher"></a>Функции 2. x и более поздних версий

Для работы с триггером и привязками требуется ссылка на соответствующий пакет. Пакет NuGet используется для библиотек классов .NET, в то время как набор расширений используется для всех других типов приложений.

| Язык                                        | Добавить по...                                   | Remarks 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Установка [Пакет NuGet], версия 3. x | |
| C#Script, Java, JavaScript, Python, PowerShell | Регистрация [Пакет расширений]          | [Расширение "инструменты Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) " рекомендуется использовать с Visual Studio Code. |
| C#Скрипт (только в интерактивном режиме в портал Azure)         | Добавление привязки                            | Чтобы обновить существующие расширения привязки без повторной публикации приложения функции, см. статью [Обновление расширений]. |

[core tools]: ./functions-run-local.md
[Пакет расширений]: ./functions-bindings-register.md#extension-bundles
[Пакет NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage.
[Обновление расширений]: ./install-update-binding-extensions-manual.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Функции 1.x

Функции 1. x автоматически имеют ссылку на пакет NuGet [Microsoft. Azure. веб-задания](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) , версия 2. x.

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="next-steps"></a>Дальнейшие действия

- [Выполнение функции как изменения данных хранилища очередей (триггер)](./functions-bindings-storage-queue-trigger.md)
- [Сообщения хранилища очереди записи (Выходная привязка)](./functions-bindings-storage-blob-output.md)