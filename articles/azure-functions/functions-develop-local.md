---
title: Develop and run Azure functions locally
description: Узнайте, как программировать и тестировать функции Azure на локальном компьютере перед их запуском в Функциях Azure.
ms.topic: conceptual
ms.date: 09/04/2018
ms.openlocfilehash: 835edcb94b294d93cab41ea51b88ac38db71d95e
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230648"
---
# <a name="code-and-test-azure-functions-locally"></a>Как программировать и тестировать Функции Azure в локальной среде

Хотя вы можете разрабатывать и тестировать Функции Azure на [Портал Azure], многие разработчики предпочитают локальную среду разработки. Решение "Функции" позволяет с легкостью использовать предпочитаемый редактор кода и средства разработки для создания и тестирования функций на локальном компьютере. Локальные функции можно подключать к действующим службам Azure, а их отладку можно выполнять на локальном компьютере с помощью полной среды выполнения решения "Функции".

## <a name="local-development-environments"></a>Локальные среды разработки

Способ разработки функций на локальном компьютере зависит от настроек [языка](supported-languages.md) и инструментария. В нижеприведенной таблице указаны среды, которые поддерживают локальную разработку:

|Среда                              |Языки         |Описание|
|-----------------------------------------|------------|---|
|[Код Visual Studio](functions-develop-vs-code.md)| [C# (class library)](functions-dotnet-class-library.md), [C# script (.csx)](functions-reference-csharp.md), [JavaScript](functions-reference-node.md), [PowerShell](functions-create-first-function-powershell.md), [Python](functions-reference-python.md) | [Расширение Функций Azure для VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) добавляет поддержку средства "Функции" в VS Code. Требуются основные инструменты. Поддерживает разработку на платформах Linux, MacOS и Windows при использовании версии 2.x основных инструментов. Для получения дополнительных сведений см. статью [Создание функции в Azure с помощью Visual Studio Code](functions-create-first-function-vs-code.md). |
| [Командная строка или терминал](functions-run-local.md) | [C# (class library)](functions-dotnet-class-library.md), [C# script (.csx)](functions-reference-csharp.md), [JavaScript](functions-reference-node.md), [PowerShell](functions-reference-powershell.md), [Python](functions-reference-python.md) | [Основные инструменты службы "Функции Azure"] provides the core runtime and templates for creating functions, which enable local development. Версия 2.x поддерживает разработку в Linux, MacOS и Windows. Во всех средах применяют основные инструменты для локальной среды выполнения средства "Функции". |
| [Visual Studio 2019](functions-develop-vs.md) | [C# (библиотека классов)](functions-dotnet-class-library.md) | The Azure Functions tools are included in the **Azure development** workload of [Visual Studio 2019](https://www.visualstudio.com/vs/) and later versions. Позволяет компилировать функции в библиотеке классов и публиковать библиотеки DLL в Azure. Включает основные инструменты для локального тестирования. Дополнительные сведения см. в статье [Инструменты Функций Azure для Visual Studio](functions-develop-vs.md). |
| [Maven](functions-create-first-java-maven.md) (различные) | [Java](functions-reference-java.md) | Интегрируется с основными инструментами и обеспечивает возможность разработки функций Java. Версия 2.x поддерживает разработку в Linux, MacOS и Windows. Дополнительные сведения см. в статье [Создание первой функции с помощью Java и Maven](functions-create-first-java-maven.md). Также поддерживает разработку с помощью [Eclipse](functions-create-maven-eclipse.md) и [IntelliJ IDEA](functions-create-maven-intellij.md). |

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

Каждая из этих локальных сред разработки позволяет создавать проекты приложений-функций и использовать предопределенные шаблоны решения "Функции" для создания новых функций. Каждая из них использует основные инструменты, чтобы вы могли тестировать и отлаживать функции в реальной среде выполнения решения "Функции" на своем компьютере как и любое другое приложение. You can also publish your function app project from any of these environments to Azure.  

## <a name="next-steps"></a>Дальнейшие действия

+ To learn more about local development of compiled C# functions using Visual Studio 2019, see [Develop Azure Functions using Visual Studio](functions-develop-vs.md).
+ To learn more about local development of functions using VS Code on a Mac, Linux, or Windows computer, see [Deploy Azure Functions from VS Code](/azure/javascript/tutorial-vscode-serverless-node-01).
+ Дополнительные сведения о разработке функций из командной строки или терминала см. в статье [Как программировать и тестировать Функции Azure в локальной среде](functions-run-local.md).

<!-- LINKS -->

[Основные инструменты службы "Функции Azure"]: https://www.npmjs.com/package/azure-functions-core-tools
[Портал Azure]: https://portal.azure.com 
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
