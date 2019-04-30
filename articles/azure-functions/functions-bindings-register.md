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
origin.date: 02/18/2019
ms.date: 04/26/2019
ms.author: v-junlch
ms.openlocfilehash: 5534086d5754691f650370e465fa2c63210e0dc7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "61437860"
---
# <a name="register-azure-functions-binding-extensions"></a>Регистрация расширений привязки функций Azure

Функции Azure поддерживают HTTP и таймера без дополнительной настройки. Для работы с другими службами, необходимо установить или *зарегистрировать* [привязки](./functions-triggers-bindings.md) расширения. Расширения привязки предоставляются через пакет основных инструментов Azure или NuGet. 

Следующая таблица показывает, время и способ регистрации привязки.

| Среда разработки |Регистрация<br/> в службе "Функции" версии 1.x  |Регистрация<br/> в службе "Функции" версии 2.x  |
|-------------------------|------------------------------------|------------------------------------|
|Портал Azure|Автоматически|[Автоматически, с соответствующим запросом](#azure-portal-development)|
|Языки, отличные от .NET или локальная разработка основных инструментов Azure|Автоматически|[С помощью команд CLI из основных инструментов](#local-development-azure-functions-core-tools)|
|Библиотека классов C# с использованием Visual Studio 2017|[С помощью средств NuGet](#c-class-library-with-visual-studio-2017)|[С помощью средств NuGet](#c-class-library-with-visual-studio-2017)|
|Библиотека классов C# с использованием Visual Studio Code|Н/Д|[С помощью .NET Core CLI](#c-class-library-with-visual-studio-code)|

Следующие типы привязки считаются исключениями и не требуют явной регистрации, так как они автоматически регистрируются во всех версиях и средах: HTTP и таймер.

> [!IMPORTANT]
> Это содержимое для оставшейся части этой статьи применяется только к функций версии 2.x. Расширения привязки не зарегистрирована явно в функциях версии 1.x, за исключением случаев [создание C# библиотеки классов с помощью Visual Studio 2017](#local-csharp).

## <a name="azure-portal-development"></a>Разработка на портале Azure

При создании функции или добавлении привязки вы увидите запрос, если нужно зарегистрировать расширение для триггера или привязки. Щелкните в этом запросе действие **Установить**, чтобы зарегистрировать расширение. В рамках плана потребления установка может занять до 10 минут. 

Достаточно установить каждое расширение по одному разу для конкретного приложения-функции. В поддерживаемых привязках, которые недоступны на портале или при обновлении установленных расширений, также можно использовать сведения из статьи [Manually install or update Azure Functions binding extensions from the portal](install-update-binding-extensions-manual.md) (Установка или обновление расширения привязки Функции Azure вручную на портале).  

## <a name="local-development-azure-functions-core-tools"></a>Локальная разработка основных инструментов решения "Функции Azure"

[!INCLUDE [functions-core-tools-install-extension](../../includes/functions-core-tools-install-extension.md)]

<a name="local-csharp"></a>
## <a name="c-class-library-with-visual-studio-2017"></a>Библиотека классов C# с Visual Studio 2017

В **Visual Studio 2017** вы можете установить пакеты, выполнив в консоли диспетчера пакетов команду [Install-Package](https://docs.microsoft.com/nuget/tools/ps-ref-install-package), как показано в следующем примере:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <target_version>
```

Имя пакета, которое нужно указать для конкретной привязки, предоставляется в справочной статье по этой привязке. Например, вы можете ознакомиться с [разделом о пакетах в справочной статье о привязках Служебной шины](functions-bindings-service-bus.md#packages---functions-1x).

Замените `<target_version>` в этом примере определенной версией пакета, например `3.0.0-beta5`. Допустимые версии перечислены на страницах отдельных пакетов на сайте [NuGet.org](https://nuget.org). Основные версии, которые соответствуют среде выполнения службы "Функции" версии 1.х или 2.х, указаны в справочной статье по конкретной привязке.

## <a name="c-class-library-with-visual-studio-code"></a>Библиотека классов C# с Visual Studio Code

В **Visual Studio Code** пакеты можно установить из командной строки .NET CLI, используя команду [dotnet add package](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package), как показано в следующем примере.

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.ServiceBus --version <target_version>
```

Интерфейс .NET Core CLI можно использовать только при разработке в среде "Функции Azure" версии 2.х.

Имя пакета, которое нужно указать для конкретной привязки, предоставляется в справочной статье по этой привязке. Например, вы можете ознакомиться с [разделом о пакетах в справочной статье о привязках Служебной шины](functions-bindings-service-bus.md#packages---functions-1x).

Замените `<target_version>` в этом примере определенной версией пакета, например `3.0.0-beta5`. Допустимые версии перечислены на страницах отдельных пакетов на сайте [NuGet.org](https://nuget.org). Основные версии, которые соответствуют среде выполнения службы "Функции" версии 1.х или 2.х, указаны в справочной статье по конкретной привязке.

## <a name="next-steps"></a>Дальнейшие действия
> [!div class="nextstepaction"]
> [Пример триггера и привязки для Azure функции](./functions-bindings-example.md)


