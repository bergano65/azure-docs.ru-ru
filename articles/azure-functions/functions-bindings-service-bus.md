---
title: Привязки служебной шины Azure для службы "Функции Azure"
description: Научитесь отправлять триггеры и привязки службы Azure в функции Azure.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.openlocfilehash: 44e4114b328701d5de363e91f5562f1daad351c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277418"
---
# <a name="azure-service-bus-bindings-for-azure-functions"></a>Привязки служебной шины Azure для службы "Функции Azure"

Функции Azure интегрируются с [Azure Service Bus](https://azure.microsoft.com/services/service-bus) с [помощью триггеров и привязок.](./functions-triggers-bindings.md) Интеграция с Service Bus позволяет создавать функции, которые реагируют и отправляют сообщения очереди или темы.

| Действие | Тип |
|---------|---------|
| Запуск функции при создании очереди в автобусе службы или сообщении темы | [Триггер](./functions-bindings-service-bus-trigger.md) |
| Отправка сообщений службы Azure Bus |[Выходная привязка](./functions-bindings-service-bus-output.md) |

## <a name="add-to-your-functions-app"></a>Добавление в приложение Функции

### <a name="functions-2x-and-higher"></a>Функции 2.x и выше

Работа с триггером и привязками требует, чтобы вы ссылали соответствующий пакет. Пакет NuGet используется для библиотек класса .NET, в то время как пакет расширения используется для всех других типов приложений.

| Язык                                        | Добавить по...                                   | Remarks 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Установка [пакета NuGet,]версия 4.x | |
| Си-скрипт, Java, JavaScript, Python, PowerShell | Регистрация [пакета расширения]          | [Расширение Azure Tools] рекомендуется использовать с помощью Visual Studio Code. |
| Си-скрипт (онлайн-портал Azure)         | Добавление связывания                            | Чтобы обновить существующие обязательные расширения без необходимости переиздания приложения функции, [см.] |

[Пакет NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus/
[core tools]: ./functions-run-local.md
[расширение расслоение]: ./functions-bindings-register.md#extension-bundles
[Обновление расширений]: ./install-update-binding-extensions-manual.md
[Расширение Azure Tools]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Функции 1.x

Функции 1.x приложения автоматически имеют ссылку на пакет [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet, версия 2.x.

## <a name="next-steps"></a>Дальнейшие действия

- [Выполнить функцию при создании очереди в автобусе службы или сообщении темы (Trigger)](./functions-bindings-service-bus-trigger.md)
- [Отправка сообщений шины Azure от функций Azure (связывание вывода)](./functions-bindings-service-bus-output.md)
