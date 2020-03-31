---
title: Триггер и привязки к хранилищу Azure Queue для обзора функций Azure
description: Узнайте, как использовать триггер службы хранилища очередей Azure и выходную привязку в службе "Функции Azure".
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 71321fa631bafde5b1dbaac2d165cb68fd8116d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277314"
---
# <a name="azure-queue-storage-trigger-and-bindings-for-azure-functions-overview"></a>Триггер и привязки к хранилищу Azure Queue для обзора функций Azure

Функции Azure могут выполняться по мере создания новых сообщений хранения очереди Azure и записывания сообщений очереди в функции.

| Действие | Тип |
|---------|---------|
| Запуск функции при изменении данных хранения очередей | [Триггер](./functions-bindings-storage-queue-trigger.md) |
| Запись сообщений хранения очередей |[Выходная привязка](./functions-bindings-storage-queue-output.md) |

## <a name="add-to-your-functions-app"></a>Добавление в приложение Функции

### <a name="functions-2x-and-higher"></a>Функции 2.x и выше

Работа с триггером и привязками требует, чтобы вы ссылали соответствующий пакет. Пакет NuGet используется для библиотек класса .NET, в то время как пакет расширения используется для всех других типов приложений.

| Язык                                        | Добавить по...                                   | Remarks 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Установка [пакета NuGet,]версия 3.x | |
| Си-скрипт, Java, JavaScript, Python, PowerShell | Регистрация [пакета расширения]          | [Расширение Azure Tools](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) рекомендуется использовать с помощью Visual Studio Code. |
| Си-скрипт (онлайн-портал Azure)         | Добавление связывания                            | Чтобы обновить существующие обязательные расширения без необходимости переиздания приложения функции, [см.] |

[core tools]: ./functions-run-local.md
[расширение расслоение]: ./functions-bindings-register.md#extension-bundles
[Пакет NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage
[Обновление расширений]: ./install-update-binding-extensions-manual.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Функции 1.x

Функции 1.x приложения автоматически имеют ссылку на пакет [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet, версия 2.x.

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="next-steps"></a>Дальнейшие действия

- [Выполнить функцию при изменении данных хранения очереди (Trigger)](./functions-bindings-storage-queue-trigger.md)
- [Запись сообщений хранения очереди (связка выхода)](./functions-bindings-storage-queue-output.md)
