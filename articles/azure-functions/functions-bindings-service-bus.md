---
title: Привязки служебной шины Azure для службы "Функции Azure"
description: Узнайте, как отправить триггеры и привязки служебной шины Azure в службе "функции Azure".
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.openlocfilehash: b505a90057a213d7f64d4d168a6a70da0ff03c2f
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/20/2020
ms.locfileid: "77485307"
---
# <a name="azure-service-bus-bindings-for-azure-functions"></a>Привязки служебной шины Azure для службы "Функции Azure"

Функции Azure интегрируются с [служебной шиной Azure](https://azure.microsoft.com/services/service-bus) через [триггеры и привязки](./functions-triggers-bindings.md). Интеграция с служебной шиной позволяет создавать функции, реагирующие на и отправляющие сообщения очереди или раздела.

| Действие | Тип |
|---------|---------|
| Выполнение функции при создании сообщения очереди или раздела служебной шины | [Триггер](./functions-bindings-service-bus-trigger.md) |
| Отправка сообщений служебной шины Azure |[Выходная привязка](./functions-bindings-service-bus-output.md) |

## <a name="add-to-your-functions-app"></a>Добавление в приложение функций

### <a name="functions-2x-and-higher"></a>Функции 2. x и более поздних версий

Для работы с триггером и привязками требуется ссылка на соответствующий пакет. Пакет NuGet используется для библиотек классов .NET, в то время как набор расширений используется для всех других типов приложений.

| Язык                                        | Добавить по...                                   | Remarks 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Установка [Пакет NuGet], версия 3. x | |
| C#Script, Java, JavaScript, Python, PowerShell | Регистрация [Пакет расширений]          | [Расширение "инструменты Azure"] " рекомендуется использовать с Visual Studio Code. |
| C#Скрипт (только в интерактивном режиме в портал Azure)         | Добавление привязки                            | Чтобы обновить существующие расширения привязки без повторной публикации приложения функции, см. статью [Обновление расширений]. |

[Пакет NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus.
[core tools]: ./functions-run-local.md
[Пакет расширений]: ./functions-bindings-register.md#extension-bundles
[Обновление расширений]: ./install-update-binding-extensions-manual.md
[Расширение "инструменты Azure"]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Функции 1.x

Функции 1. x автоматически имеют ссылку на пакет NuGet [Microsoft. Azure. веб-задания](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) , версия 2. x.

## <a name="next-steps"></a>Дальнейшие действия

- [Выполнение функции при создании сообщения очереди или раздела служебной шины (триггер)](./functions-bindings-service-bus-trigger.md)
- [Отправка сообщений служебной шины Azure из функций Azure (Выходная привязка)](./functions-bindings-service-bus-output.md)
