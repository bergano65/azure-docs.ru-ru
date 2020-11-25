---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: b0f8055dcd8bdfb2ecb3ea99448838514eeb34f3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2020
ms.locfileid: "96026204"
---
## <a name="add-to-your-functions-app"></a>Добавление в приложение функций

### <a name="functions-2x-and-higher"></a>Функции 2.x и более поздних версий

Для работы с триггером и привязками требуется ссылка на соответствующий пакет. Пакет NuGet используется для библиотек классов .NET, в то время как набор расширений используется для всех других типов приложений.

| Язык                                        | Добавить по...                                   | Комментарии 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Установка [пакета NuGet], версия 3. x | |
| Скрипт C#, Java, JavaScript, Python, PowerShell | Регистрация [пакета расширений]          | [Расширение "инструменты Azure] " рекомендуется использовать с Visual Studio Code. |
| Скрипт C# (только в сети портал Azure)         | Добавление привязки                            | Чтобы обновить существующие расширения привязки без повторной публикации приложения функции, см. статью [Обновление расширений]. |

[Пакет NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs
[core tools]: ../articles/azure-functions/functions-run-local.md
[Пакет расширений]: ../articles/azure-functions/functions-bindings-register.md#extension-bundles
[Обновление расширений]: ../articles/azure-functions/functions-bindings-register.md
[Расширение "инструменты Azure"]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Функции 1.x

Функции 1. x автоматически имеют ссылку на пакет NuGet [Microsoft. Azure. веб-задания](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) , версия 2. x.