---
title: Привязки RabbitMQ для Azure для функций Azure
description: Узнайте, как отправить триггеры и привязки Azure RabbitMQ в функциях Azure.
author: cachai2
ms.assetid: ''
ms.topic: reference
ms.date: 12/15/2020
ms.author: cachai
ms.custom: ''
ms.openlocfilehash: 72c31d0eebc19a926805976ed637168a00fa9495
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/17/2020
ms.locfileid: "97617193"
---
# <a name="rabbitmq-bindings-for-azure-functions-overview"></a>Общие сведения о привязках RabbitMQ для функций Azure

> [!NOTE]
> Привязки RabbitMQ полностью поддерживаются только в планах **Windows Premium** . Использование и Linux в настоящее время не поддерживаются.

Функции Azure интегрируются с [RabbitMQ](https://www.rabbitmq.com/) через [триггеры и привязки](./functions-triggers-bindings.md). Расширения привязки RabbitMQ для функций Azure позволяют отправлять и получать сообщения с помощью API RabbitMQ с функциями.

| Действие | Тип |
|---------|---------|
| Выполнение функции, когда сообщение RabbitMQ поступает через очередь | [Триггер](./functions-bindings-rabbitmq-trigger.md) |
| Отправка сообщений RabbitMQ |[Выходная привязка](./functions-bindings-rabbitmq-output.md) |

## <a name="add-to-your-functions-app"></a>Добавление в приложение функций

Чтобы приступить к разработке с помощью этого расширения, убедитесь, что вы [установили конечную точку RabbitMQ](https://github.com/Azure/azure-functions-rabbitmq-extension/wiki/Setting-up-a-RabbitMQ-Endpoint). Дополнительные сведения о RabbitMQ см. на странице « [Начало работы](https://www.rabbitmq.com/getstarted.html)».

### <a name="functions-3x-and-higher"></a>Функции 3. x и более поздних версий

Для работы с триггером и привязками требуется ссылка на соответствующий пакет. Пакет NuGet используется для библиотек классов .NET, в то время как набор расширений используется для всех других типов приложений.

| Язык                                        | Добавить по...                                   | Remarks 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Установка [пакета NuGet], версия 4. x | |
| Скрипт C#, Java, JavaScript, Python, PowerShell | Регистрация [пакета расширений]          | [Расширение "инструменты Azure] " рекомендуется использовать с Visual Studio Code. |
| Скрипт C# (только в сети портал Azure)         | Добавление привязки                            | Чтобы обновить существующие расширения привязки без повторной публикации приложения функции, см. статью [Обновление расширений]. |

[Пакет NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.RabbitMQ
[core tools]: ./functions-run-local.md
[Пакет расширений]: ./functions-bindings-register.md#extension-bundles
[Обновление расширений]: ./functions-bindings-register.md
[Расширение "инструменты Azure"]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x-and-2x"></a>Функции 1. x и 2. x

Расширения привязки RabbitMQ не поддерживаются для функций 1. x и 2. x. Используйте функции 3. x и более поздней версии.

## <a name="next-steps"></a>Дальнейшие действия

- [Выполнение функции при создании сообщения RabbitMQ (триггер)](./functions-bindings-rabbitmq-trigger.md)
- [Отправка сообщений RabbitMQ из функций Azure (Выходная привязка)](./functions-bindings-rabbitmq-output.md)