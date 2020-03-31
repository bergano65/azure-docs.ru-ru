---
title: Триггеры и привязки HTTP в службе "Функции Azure"
description: Научитесь использовать триггеры и привязки HTTP в функциях Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.openlocfilehash: 29b5e9c7673b4a730a41bf7cf2b1c4a2a86209ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77462111"
---
# <a name="azure-functions-http-triggers-and-bindings-overview"></a>Обзор триггеров и привязок Azure Functions HTTP

Функции Azure могут вызываться через запросы HTTP для создания безсерверных AIS и реагирования на [веб-крючки.](https://en.wikipedia.org/wiki/Webhook)

| Действие | Тип |
|---------|---------|
| Выполнить функцию из запроса HTTP | [Триггер](./functions-bindings-http-webhook-trigger.md) |
| Возврат ответа HTTP от функции |[Выходная привязка](./functions-bindings-http-webhook-output.md) |

Код в этой статье по умолчанию является синтаксисом .NET Core, используемым в версии Функции 2.x и выше. Сведения о синтаксисе версии 1.x см. на странице с [соответствующими шаблонами функций](https://github.com/Azure/azure-functions-templates/tree/v1.x/Functions.Templates/Templates).

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
[Пакет NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http
[Обновление расширений]: ./install-update-binding-extensions-manual.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Функции 1.x

Функции 1.x приложения автоматически имеют ссылку на пакет [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet, версия 2.x.

## <a name="next-steps"></a>Дальнейшие действия

- [Выполнить функцию из запроса HTTP](./functions-bindings-http-webhook-trigger.md)
- [Возврат ответа HTTP от функции](./functions-bindings-http-webhook-output.md)
