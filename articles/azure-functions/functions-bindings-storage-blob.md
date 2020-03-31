---
title: Триггер и привязки хранилища Azure Blob для функций Azure
description: Научитесь использовать триггерик и привязки хранилища Azure Blob в функциях Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.openlocfilehash: 6950f1dd81ef2c70f2c45fb4c547ed7676067790
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277236"
---
# <a name="azure-blob-storage-bindings-for-azure-functions-overview"></a>Привязки хранения Azure Blob для обзора функций Azure

Функции Azure интегрируются с [Хранилищем Azure](https://docs.microsoft.com/azure/storage/) с [помощью триггеров и привязок.](./functions-triggers-bindings.md) Интеграция с хранилищем Blob позволяет создавать функции, которые реагируют на изменения в данных blob, а также читать и писать значения.

| Действие | Тип |
|---------|---------|
| Выполнить функцию как изменения данных хранения каблов | [Триггер](./functions-bindings-storage-blob-trigger.md) |
| Читать данные о хранении капли в функции | [Связывание входных вхоза](./functions-bindings-storage-blob-input.md) |
| Разрешить функции для записи данных хранения капли |[Выходная привязка](./functions-bindings-storage-blob-output.md) |

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

- [Выполнить функцию при изменении данных хранения каблики](./functions-bindings-storage-blob-trigger.md)
- [Чтение данных о хранении капли при запуске функции](./functions-bindings-storage-blob-input.md)
- [Запись данных о хранении каблов из функции](./functions-bindings-storage-blob-output.md)
