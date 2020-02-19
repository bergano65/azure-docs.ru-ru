---
title: Привязки службы "Сетка событий Azure" для функций Azure
description: Узнайте, как обрабатывать события службы "Сетка событий" в службе "Функции Azure".
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: fasttrack-edit
ms.openlocfilehash: 21654a3b325e8b8f0a3e49ee64b7624c8540d0d5
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/19/2020
ms.locfileid: "77461085"
---
# <a name="azure-event-grid-bindings-for-azure-functions"></a>Привязки службы "Сетка событий Azure" для функций Azure

В этом справочнике объясняется, как управлять событиями службы " [Сетка событий](../event-grid/overview.md) " в функциях Azure. Дополнительные сведения о том, как управлять сообщениями сетки событий в конечной точке HTTP, см. в разделе [получение событий в конечной точке](../event-grid/receive-events.md)HTTP.

Служба "Сетка событий" — это служба Azure, которая отправляет HTTP-запросы для уведомления о событиях, которые происходят в *издателях*. Издатель — это служба или ресурс, в котором происходит событие. Например, учетная запись хранения больших двоичных объектов Azure является издателем, а [отправка или удаление большого двоичного объекта — это событие](../storage/blobs/storage-blob-event-overview.md). Некоторые [службы Azure имеют встроенную поддержку публикации событий в службу "Сетка событий"](../event-grid/overview.md#event-sources).

*Обработчики* событий получают и обрабатывают события. Служба "Функции Azure" является одной из нескольких [служб Azure, в которых есть встроенная поддержка обработки событий Сетки событий](../event-grid/overview.md#event-handlers). В этом справочнике вы узнаете, как использовать триггер сетки событий для вызова функции при получении события из сетки событий, а также использовать выходную привязку для отправки событий в [пользовательский раздел сетки событий](../event-grid/post-to-custom-topic.md).

При желании можно использовать триггер HTTP для управления событиями сетки событий. см. раздел [получение событий для конечной точки HTTP](../event-grid/receive-events.md). Сейчас, если событие доставляется в [схему CloudEvents](../event-grid/cloudevents-schema.md#azure-functions), триггер службы "Сетка событий" нельзя использовать в приложении "Функции Azure". В этом случае необходимо использовать триггер HTTP.

| Действие | Тип |
|---------|---------|
| Выполнение функции при диспетчеризации события сетки событий | [Триггер](./functions-bindings-event-grid-trigger.md) |
| Отправляет событие сетки событий |[Выходная привязка](./functions-bindings-event-grid-output.md) |

Код в этой ссылке по умолчанию использует синтаксис .NET Core, используемый в функциях версии 2. x и более поздних. Сведения о синтаксисе версии 1.x см. на странице с [соответствующими шаблонами функций](https://github.com/Azure/azure-functions-templates/tree/v1.x/Functions.Templates/Templates).

## <a name="add-to-your-functions-app"></a>Добавление в приложение функций

### <a name="functions-2x-and-higher"></a>Функции 2. x и более поздних версий

Для работы с триггером и привязками требуется ссылка на соответствующий пакет. Пакет NuGet используется для библиотек классов .NET, в то время как набор расширений используется для всех других типов приложений.

| Язык                                        | Добавить по...                                   | Примечания 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Установка [Пакет NuGet], версия 3. x | |
| C#Script, Java, JavaScript, Python, PowerShell | Регистрация [Пакет расширений]          | [Расширение "инструменты Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) " рекомендуется использовать с Visual Studio Code. |
| C#Скрипт (только в интерактивном режиме в портал Azure)         | Добавление привязки                            | Чтобы обновить существующие расширения привязки без повторной публикации приложения функции, см. статью [Обновление расширений]. |

[core tools]: ./functions-run-local.md
[Пакет расширений]: ./functions-bindings-register.md#extension-bundles
[Пакет NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid.
[Обновление расширений]: ./install-update-binding-extensions-manual.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Функции 1.x

Функции 1. x автоматически имеют ссылку на пакет NuGet [Microsoft. Azure. веб-задания](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) , версия 2. x.

## <a name="next-steps"></a>Следующие шаги
* [Выполнение функции при диспетчеризации события сетки событий](./functions-bindings-event-grid-trigger.md)
* [Отправка события сетки событий](./functions-bindings-event-grid-trigger.md)
