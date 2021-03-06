---
title: Регистрация расширений привязки функций Azure
description: Узнайте, как зарегистрировать расширение привязки функций Azure на основе вашей среды.
author: craigshoemaker
ms.topic: reference
ms.date: 07/08/2019
ms.author: cshoe
ms.openlocfilehash: 599becae0225bea623c383ead49cd9abcea6fff2
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231114"
---
# <a name="register-azure-functions-binding-extensions"></a>Регистрация расширений привязки функций Azure

В функциях Azure версии 2. x [привязки](./functions-triggers-bindings.md) доступны в виде отдельных пакетов из среды выполнения функций. Несмотря на то, что функции .NET обращаются к привязкам через пакеты NuGet, пакеты расширений позволяют другим функциям обращаться ко всем привязкам через параметр конфигурации.

Рассмотрим следующие элементы, связанные с расширениями привязки:

- Расширения привязки явно не регистрируются в функциях 1. x, за исключением случаев [создания библиотеки C# классов с помощью Visual Studio](#local-csharp).

- Триггеры HTTP и Timer поддерживаются по умолчанию и не нуждаются в расширении.

В следующей таблице показано, когда и как регистрируются привязки.

| Среда разработки |Регистрация<br/> в службе "Функции" версии 1.x  |Регистрация<br/> в службе "Функции" версии 2.x  |
|-------------------------|------------------------------------|------------------------------------|
|портале Azure|Автоматический|Автоматический|
|Разработка Non-.NET языков или локальных средств Azure Core Tools|Автоматический|[Использование Azure Functions Core Tools и пакетов расширений](#extension-bundles)|
|C#Библиотека классов с помощью Visual Studio|[С помощью средств NuGet](#vs)|[С помощью средств NuGet](#vs)|
|Библиотека классов C# с использованием Visual Studio Code|Недоступно|[С помощью .NET Core CLI](#vs-code)|

## <a name="extension-bundles"></a>Пакеты расширений для локальной разработки

Пакеты расширений — это технология развертывания, позволяющая добавить совместимый набор функций расширений привязки к приложению-функции. При создании приложения добавляется предопределенный набор расширений. Пакеты расширений, определенные в пакете, совместимы друг с другом, что позволяет избежать конфликтов между пакетами. Пакеты расширений можно включить в файле host. JSON приложения.  

Вы можете использовать пакеты расширений с версией 2. x и более поздними версиями среды выполнения функций. При разработке локально убедитесь, что вы используете последнюю версию [Azure functions Core Tools](functions-run-local.md#v2).

Пакеты расширений можно использовать для локальной разработки с помощью Azure Functions Core Tools, Visual Studio Code и при удаленной сборке.

Если вы не используете пакеты расширений, необходимо установить пакет SDK для .NET Core 2. x на локальном компьютере перед установкой каких бы то ни было расширений привязки. Пакеты расширений удаляют это требование для локальной разработки. 

Чтобы использовать пакеты расширений, обновите файл *Host. JSON* , включив в него следующую запись для `extensionBundle`:
 
[!INCLUDE [functions-extension-bundles-json](../../includes/functions-extension-bundles-json.md)]

<a name="local-csharp"></a>

## <a name="vs"></a>Библиотека классов C\# в Visual Studio

В **Visual Studio**пакеты можно установить из консоли диспетчера пакетов с помощью команды [Install-Package](https://docs.microsoft.com/nuget/tools/ps-ref-install-package) , как показано в следующем примере:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <TARGET_VERSION>
```

Имя пакета, используемого для данной привязки, приведено в справочной статье для этой привязки. Например, вы можете ознакомиться с [разделом о пакетах в справочной статье о привязках Служебной шины](functions-bindings-service-bus.md#packages---functions-1x).

Замените `<TARGET_VERSION>` в этом примере определенной версией пакета, например `3.0.0-beta5`. Допустимые версии перечислены на отдельных страницах пакета по адресу [NuGet.org](https://nuget.org). Основные версии, соответствующие функциям среды выполнения 1. x или 2. x, указаны в справочной статье для привязки.

Если для ссылки на привязку используется `Install-Package`, не нужно использовать [пакеты расширений](#extension-bundles). Этот подход характерен для библиотек классов, созданных в Visual Studio.

## <a name="vs-code"></a>C# библиотека классов с Visual Studio Code

> [!NOTE]
> Мы рекомендуем использовать [пакеты расширений](#extension-bundles) , чтобы функции автоматически установили совместимый набор пакетов расширений привязки. 

В **Visual Studio Code**установите пакеты для проекта библиотеки C# классов из командной строки с помощью команды [DotNet Add Package](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package) в .NET Core CLI. В следующем примере показано, как добавить привязку:

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.<BINDING_TYPE_NAME> --version <TARGET_VERSION>
```

Интерфейс .NET Core CLI можно использовать только при разработке в среде "Функции Azure" версии 2.х.

Замените `<BINDING_TYPE_NAME>` именем пакета, который содержит необходимую привязку. Нужную ссылочную статью привязки можно найти в [списке поддерживаемых привязок](./functions-triggers-bindings.md#supported-bindings).

Замените `<TARGET_VERSION>` в этом примере определенной версией пакета, например `3.0.0-beta5`. Допустимые версии перечислены на отдельных страницах пакета по адресу [NuGet.org](https://nuget.org). Основные версии, соответствующие функциям среды выполнения 1. x или 2. x, указаны в справочной статье для привязки.

## <a name="next-steps"></a>Дополнительная информация
> [!div class="nextstepaction"]
> [Пример триггера и привязки функции Azure](./functions-bindings-example.md)
