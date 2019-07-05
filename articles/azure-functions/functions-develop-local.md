---
title: Разработка и выполнение Функций Azure локально | Документация Майкрософт
description: Узнайте, как программировать и тестировать функции Azure на локальном компьютере перед их запуском в Функциях Azure.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: abb807262d976419a0c7700046bd8ad58322fc90
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/03/2019
ms.locfileid: "67537075"
---
# <a name="code-and-test-azure-functions-locally"></a>Как программировать и тестировать Функции Azure в локальной среде

Хотя вы можете разрабатывать и тестировать Функции Azure на [портал Azure], многие разработчики предпочитают локальную среду разработки. Решение "Функции" позволяет с легкостью использовать предпочитаемый редактор кода и средства разработки для создания и тестирования функций на локальном компьютере. Локальные функции можно подключать к действующим службам Azure, а их отладку можно выполнять на локальном компьютере с помощью полной среды выполнения решения "Функции".

## <a name="local-development-environments"></a>Локальные среды разработки

Способ разработки функций на локальном компьютере зависит от настроек [языка](supported-languages.md) и инструментария. В нижеприведенной таблице указаны среды, которые поддерживают локальную разработку:

|Среда                              |Языки         |Описание|
|-----------------------------------------|------------|---|
|[Visual Studio Code](functions-develop-vs-code.md)| [C#(библиотека классов) ](functions-dotnet-class-library.md), [ C# сценария (csx)](functions-reference-csharp.md), [JavaScript](functions-reference-node.md), [PowerShell](functions-create-first-function-powershell.md), [Python](functions-reference-python.md) | [Расширение Функций Azure для VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) добавляет поддержку средства "Функции" в VS Code. Требуются основные инструменты. Поддерживает разработку на платформах Linux, MacOS и Windows при использовании версии 2.x основных инструментов. Для получения дополнительных сведений см. статью [Создание функции в Azure с помощью Visual Studio Code](functions-create-first-function-vs-code.md). |
| [Командная строка или терминал](functions-run-local.md) | [C#(библиотека классов) ](functions-dotnet-class-library.md), [ C# сценария (csx)](functions-reference-csharp.md), [JavaScript](functions-reference-node.md), [PowerShell](functions-reference-powershell.md), [Python](functions-reference-python.md) | [Основные инструменты службы "Функции Azure"] предоставляет среда выполнения и шаблоны для создания функции, которые позволяют локальной разработки. Версия 2.x поддерживает разработку в Linux, MacOS и Windows. Во всех средах применяют основные инструменты для локальной среды выполнения средства "Функции". |
| [Visual Studio 2019](functions-develop-vs.md) | [C# (библиотека классов)](functions-dotnet-class-library.md) | Средства функций Azure включены в **разработки Azure** рабочую нагрузку [Visual Studio 2019](https://www.visualstudio.com/vs/) и более поздних версий. Позволяет компилировать функции в библиотеке классов и публиковать библиотеки DLL в Azure. Включает основные инструменты для локального тестирования. Дополнительные сведения см. в статье [Инструменты Функций Azure для Visual Studio](functions-develop-vs.md). |
| [Maven](functions-create-first-java-maven.md) (различные) | [Java](functions-reference-java.md) | Интегрируется с основными инструментами и обеспечивает возможность разработки функций Java. Версия 2.x поддерживает разработку в Linux, MacOS и Windows. Дополнительные сведения см. в статье [Создание первой функции с помощью Java и Maven](functions-create-first-java-maven.md). Также поддерживает разработку с помощью [Eclipse](functions-create-maven-eclipse.md) и [IntelliJ IDEA](functions-create-maven-intellij.md). |

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

Каждая из этих локальных сред разработки позволяет создавать проекты приложений-функций и использовать предопределенные шаблоны решения "Функции" для создания новых функций. Каждая из них использует основные инструменты, чтобы вы могли тестировать и отлаживать функции в реальной среде выполнения решения "Функции" на своем компьютере как и любое другое приложение. Вы также можете опубликовать проект приложения-функции из любой из этих сред в Azure.  

## <a name="next-steps"></a>Дальнейшие действия

+ Дополнительные сведения о локальной разработки компиляции C# функции с помощью Visual Studio 2019 г., см. в разделе [разрабатывать функции Azure с помощью Visual Studio](functions-develop-vs.md).
+ Дополнительные сведения о локальной разработке функций с помощью VS Code на компьютерах с ОС Mac, Linux или Windows см. в статье [VS Code documentation for Azure Functions](https://code.visualstudio.com/tutorials/functions-extension/getting-started) (Документация VS Code для Функций Azure).
+ Дополнительные сведения о разработке функций из командной строки или терминала см. в статье [Как программировать и тестировать Функции Azure в локальной среде](functions-run-local.md).

<!-- LINKS -->

[Основные инструменты службы "Функции Azure"]: https://www.npmjs.com/package/azure-functions-core-tools
[портал Azure]: https://portal.azure.com 
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
