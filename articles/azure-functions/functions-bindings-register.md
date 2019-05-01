---
title: Регистрация расширений привязки функций Azure
description: Узнайте, как зарегистрировать расширение привязки функций Azure об используемой среде.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 02/25/2019
ms.author: cshoe
ms.openlocfilehash: 802e177b6f3844abe4d24c26b7ea2d0d4fb1688c
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64697014"
---
# <a name="register-azure-functions-binding-extensions"></a>Регистрация расширений привязки функций Azure

Начиная с функций Azure версии 2.x, [привязки](./functions-triggers-bindings.md) доступны в виде отдельных пакетов из среды выполнения функций. Одновременно функций .NET доступ привязки через пакеты NuGet, пакеты расширения открытие других функций для всех привязок через параметр конфигурации.

Примите во внимание следующие элементы, относящиеся к расширений привязки.

- Расширения привязки не зарегистрирована явно в функциях версии 1.x, за исключением случаев [создание C# библиотеки классов с помощью Visual Studio 2017](#local-csharp).

- Триггеры HTTP и таймера поддерживаются по умолчанию и не требуют расширения.

Следующая таблица показывает, время и способ регистрации привязки.

| Среда разработки |Регистрация<br/> в службе "Функции" версии 1.x  |Регистрация<br/> в службе "Функции" версии 2.x  |
|-------------------------|------------------------------------|------------------------------------|
|Портал Azure|Автоматический|Автоматический|
|Языки, отличные от .NET или локальная разработка основных инструментов Azure|Автоматический|[Использование основных инструментов функций Azure и пакетов расширения](#local-development-with-azure-functions-core-tools-and-extension-bundles)|
|Библиотека классов C# с использованием Visual Studio 2017|[С помощью средств NuGet](#c-class-library-with-visual-studio-2017)|[С помощью средств NuGet](#c-class-library-with-visual-studio-2017)|
|Библиотека классов C# с использованием Visual Studio Code|Н/Д|[С помощью .NET Core CLI](#c-class-library-with-visual-studio-code)|

## <a name="local-development-with-azure-functions-core-tools-and-extension-bundles"></a>Локальной разработки с помощью основных инструментов функций Azure и пакетов расширения

[!INCLUDE [functions-core-tools-install-extension](../../includes/functions-core-tools-install-extension.md)]

<a name="local-csharp"></a>
## <a name="c-class-library-with-visual-studio-2017"></a>Библиотека классов C# с Visual Studio 2017

В **Visual Studio 2017** вы можете установить пакеты, выполнив в консоли диспетчера пакетов команду [Install-Package](https://docs.microsoft.com/nuget/tools/ps-ref-install-package), как показано в следующем примере:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <TARGET_VERSION>
```

Имя пакета, который используется для данной привязки приводятся в статье ссылки для этой привязки. Например, вы можете ознакомиться с [разделом о пакетах в справочной статье о привязках Служебной шины](functions-bindings-service-bus.md#packages---functions-1x).

Замените `<TARGET_VERSION>` в этом примере определенной версией пакета, например `3.0.0-beta5`. Допустимые версии перечислены на страницах отдельных пакетов на сайте [NuGet.org](https://nuget.org). Основные версии, которые соответствуют среде выполнения службы "Функции" версии 1.х или 2.х, указаны в справочной статье по конкретной привязке.

## <a name="c-class-library-with-visual-studio-code"></a>Библиотека классов C# с Visual Studio Code

В **Visual Studio Code** пакеты можно установить из командной строки .NET CLI, используя команду [dotnet add package](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package), как показано в следующем примере.

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.ServiceBus --version <TARGET_VERSION>
```

Интерфейс .NET Core CLI можно использовать только при разработке в среде "Функции Azure" версии 2.х.

Имя пакета, которое нужно указать для конкретной привязки, предоставляется в справочной статье по этой привязке. Например, вы можете ознакомиться с [разделом о пакетах в справочной статье о привязках Служебной шины](functions-bindings-service-bus.md#packages---functions-1x).

Замените `<TARGET_VERSION>` в этом примере определенной версией пакета, например `3.0.0-beta5`. Допустимые версии перечислены на страницах отдельных пакетов на сайте [NuGet.org](https://nuget.org). Основные версии, которые соответствуют среде выполнения службы "Функции" версии 1.х или 2.х, указаны в справочной статье по конкретной привязке.

## <a name="next-steps"></a>Дальнейшие действия
> [!div class="nextstepaction"]
> [Пример триггера и привязки для Azure функции](./functions-bindings-example.md)

