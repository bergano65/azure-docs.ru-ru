---
title: Привязки Azure Event Grid для функций Azure
description: Узнайте, как обрабатывать события службы "Сетка событий" в службе "Функции Azure".
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: fasttrack-edit
ms.openlocfilehash: 21654a3b325e8b8f0a3e49ee64b7624c8540d0d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461085"
---
# <a name="azure-event-grid-bindings-for-azure-functions"></a>Привязки Azure Event Grid для функций Azure

В этой ссылке объясняется, как обрабатывать события [Event Grid](../event-grid/overview.md) в Azure Functions. Подробную информацию о том, как обрабатывать сообщения Event Grid в конце http, можно узнать о том, как обрабатывать сообщения Event Grid в конечном [пункте HTTP.](../event-grid/receive-events.md)

Служба "Сетка событий" — это служба Azure, которая отправляет HTTP-запросы для уведомления о событиях, которые происходят в *издателях*. Издатель — это служба или ресурс, в котором происходит событие. Например, учетная запись хранения больших двоичных объектов Azure является издателем, а [отправка или удаление большого двоичного объекта — это событие](../storage/blobs/storage-blob-event-overview.md). Некоторые [службы Azure имеют встроенную поддержку публикации событий в службу "Сетка событий"](../event-grid/overview.md#event-sources).

*Обработчики* событий получают и обрабатывают события. Служба "Функции Azure" является одной из нескольких [служб Azure, в которых есть встроенная поддержка обработки событий Сетки событий](../event-grid/overview.md#event-handlers). В этой ссылке вы научитесь использовать триггер Event Grid для вызова функции при получении события из Event Grid и использования привязки вывода для отправки событий в [пользовательскую тему Event Grid.](../event-grid/post-to-custom-topic.md)

Если вы предпочитаете, вы можете использовать триггер HTTP для обработки событий Сетки событий; [видеть события в конечную точку HTTP.](../event-grid/receive-events.md) В настоящее время вы не можете использовать триггер Event Grid для приложения Azure Functions, когда событие выполняется в [схеме CloudEvents.](../event-grid/cloudevents-schema.md#azure-functions) В этом случае необходимо использовать триггер HTTP.

| Действие | Тип |
|---------|---------|
| Выполнить функцию при отправке события Event Grid | [Триггер](./functions-bindings-event-grid-trigger.md) |
| Отправка события Сетки Событий |[Выходная привязка](./functions-bindings-event-grid-output.md) |

Код в этой ссылке по умолчанию является синтаксисом .NET Core, используемым в версии Функции 2.x и выше. Сведения о синтаксисе версии 1.x см. на странице с [соответствующими шаблонами функций](https://github.com/Azure/azure-functions-templates/tree/v1.x/Functions.Templates/Templates).

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
[Пакет NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid
[Обновление расширений]: ./install-update-binding-extensions-manual.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Функции 1.x

Функции 1.x приложения автоматически имеют ссылку на пакет [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet, версия 2.x.

## <a name="next-steps"></a>Дальнейшие действия
* [Выполнить функцию при отправке события Event Grid](./functions-bindings-event-grid-trigger.md)
* [Отправка события сетки событий](./functions-bindings-event-grid-trigger.md)
