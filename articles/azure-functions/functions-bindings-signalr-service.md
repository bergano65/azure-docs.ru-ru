---
title: Привязки службы SignalR для службы "Функции Azure"
description: Узнайте, как использовать привязки службы SignalR с помощью службы "Функции Azure".
author: craigshoemaker
ms.topic: reference
ms.date: 02/28/2019
ms.author: cshoe
ms.openlocfilehash: 863620ce6f0af33b05ef290ae95ccdc99a53a54d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77523042"
---
# <a name="signalr-service-bindings-for-azure-functions"></a>Привязки службы SignalR для службы "Функции Azure"

В этом наборе статей объясняется, как выполнять проверку подлинности и отправлять сообщения в режиме реального времени клиентам, подключенным к [службе Azure SignalR](https://azure.microsoft.com/services/signalr-service/) , с помощью привязок службы SignalR в функциях Azure. Служба "Функции Azure" поддерживает входные и выходные привязки для службы SignalR.

| Действие | Type |
|---------|---------|
| Возврат URL-адреса конечной точки службы и маркера доступа | [Входная привязка](./functions-bindings-signalr-service-input.md) |
| Отправка сообщений службы SignalR |[Выходная привязка](./functions-bindings-signalr-service-output.md) |

## <a name="add-to-your-functions-app"></a>Добавление в приложение функций

### <a name="functions-2x-and-higher"></a>Функции 2. x и более поздних версий

Для работы с триггером и привязками требуется ссылка на соответствующий пакет. Пакет NuGet используется для библиотек классов .NET, в то время как набор расширений используется для всех других типов приложений.

| Язык                                        | Добавить по...                                   | Remarks 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Установка [пакета NuGet], версия 3. x | |
| Скрипт C#, Java, JavaScript, Python, PowerShell | Регистрация [пакета расширений]          | [Расширение "инструменты Azure] " рекомендуется использовать с Visual Studio Code. |
| Скрипт C# (только в сети портал Azure)         | Добавление привязки                            | Чтобы обновить существующие расширения привязки без повторной публикации приложения функции, см. статью [Обновление расширений]. |

[Пакет NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService
[core tools]: ./functions-run-local.md
[Пакет расширений]: ./functions-bindings-register.md#extension-bundles
[Обновление расширений]: ./install-update-binding-extensions-manual.md
[Расширение "инструменты Azure"]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

Дополнительные сведения о настройке и использовании службы SignalR и функций Azure см. в статье [Разработка и Настройка функций Azure с помощью службы Azure SignalR](../azure-signalr/signalr-concept-serverless-development-config.md).

### <a name="annotations-library-java-only"></a>Библиотека заметок (только для Java)

Чтобы использовать заметки службы SignalR в функциях Java, необходимо добавить зависимость к артефакту *Azure-functions-Java-Library-SignalR* (версии 1,0 или более поздней) в файл *POM. XML* .

```xml
<dependency>
    <groupId>com.microsoft.azure.functions</groupId>
    <artifactId>azure-functions-java-library-signalr</artifactId>
    <version>1.0.0</version>
</dependency>
```

## <a name="next-steps"></a>Дальнейшие шаги

- [Возврат URL-адреса конечной точки службы и маркера доступа (входная привязка)](./functions-bindings-signalr-service-input.md)
- [Отправка сообщений службы SignalR (Выходная привязка)](./functions-bindings-signalr-service-output.md) 
