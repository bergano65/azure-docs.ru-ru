---
title: Привязки службы SignalR для службы "Функции Azure"
description: Узнайте, как использовать привязки службы SignalR с помощью службы "Функции Azure".
author: craigshoemaker
ms.topic: reference
ms.date: 02/28/2019
ms.author: cshoe
ms.openlocfilehash: 863620ce6f0af33b05ef290ae95ccdc99a53a54d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77523042"
---
# <a name="signalr-service-bindings-for-azure-functions"></a>Привязки службы SignalR для службы "Функции Azure"

В этом наборе статей объясняется, как аутентифицировать и отправлять сообщения в режиме реального времени клиентам, подключенным к [службе Azure SignalR,](https://azure.microsoft.com/services/signalr-service/) с помощью привязок службы SignalR в Azure Functions. Служба "Функции Azure" поддерживает входные и выходные привязки для службы SignalR.

| Действие | Тип |
|---------|---------|
| Верните URL-адрес конечных точек службы и токен доступа | [Связывание входных вхоза](./functions-bindings-signalr-service-input.md) |
| Отправка сообщений службы SignalR |[Выходная привязка](./functions-bindings-signalr-service-output.md) |

## <a name="add-to-your-functions-app"></a>Добавление в приложение Функции

### <a name="functions-2x-and-higher"></a>Функции 2.x и выше

Работа с триггером и привязками требует, чтобы вы ссылали соответствующий пакет. Пакет NuGet используется для библиотек класса .NET, в то время как пакет расширения используется для всех других типов приложений.

| Язык                                        | Добавить по...                                   | Remarks 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Установка [пакета NuGet,]версия 3.x | |
| Си-скрипт, Java, JavaScript, Python, PowerShell | Регистрация [пакета расширения]          | [Расширение Azure Tools] рекомендуется использовать с помощью Visual Studio Code. |
| Си-скрипт (онлайн-портал Azure)         | Добавление связывания                            | Чтобы обновить существующие обязательные расширения без необходимости переиздания приложения функции, [см.] |

[Пакет NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService
[core tools]: ./functions-run-local.md
[расширение расслоение]: ./functions-bindings-register.md#extension-bundles
[Обновление расширений]: ./install-update-binding-extensions-manual.md
[Расширение Azure Tools]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

Для получения подробной информации о том, как настроить и использовать службу SignalR и функции Azure вместе, обратитесь к [разработке и конфигурации функций Azure с помощью Службы СигналR Azure.](../azure-signalr/signalr-concept-serverless-development-config.md)

### <a name="annotations-library-java-only"></a>Библиотека аннотаций (только Java)

Чтобы использовать аннотации службы SignalR в функциях Java, необходимо добавить зависимость к артефакту *azure-functions-java-library-signalr* (версия 1.0 или выше) в файл *pom.xml.*

```xml
<dependency>
    <groupId>com.microsoft.azure.functions</groupId>
    <artifactId>azure-functions-java-library-signalr</artifactId>
    <version>1.0.0</version>
</dependency>
```

## <a name="next-steps"></a>Дальнейшие действия

- [Верните URL-адрес и токен доступа (связывание входных данных)](./functions-bindings-signalr-service-input.md)
- [Отправка сообщений службы SignalR (связывание выходов)](./functions-bindings-signalr-service-output.md) 
