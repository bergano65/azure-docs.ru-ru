---
title: Разработка и запуск функций Azure в локальной среде
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

Хотя вы можете разрабатывать и тестировать Функции Azure на [портал Azure], многие разработчики предпочитают локальную среду разработки. Решение "Функции" позволяет с легкостью использовать предпочитаемый редактор кода и средства разработки для создания и тестирования функций на локальном компьютере. Локальные функции можно подключать к действующим службам Azure, а их отладку можно выполнять на локальном компьютере с помощью полной среды выполнения решения "Функции".

## <a name="local-development-environments"></a>Локальные среды разработки

Способ разработки функций на локальном компьютере зависит от настроек [языка](supported-languages.md) и инструментария. В нижеприведенной таблице указаны среды, которые поддерживают локальную разработку:

|Среда                              |Languages         |ОПИСАНИЕ|
|-----------------------------------------|------------|---|
|[Visual Studio Code](functions-develop-vs-code.md)| [](functions-create-first-function-powershell.md) [](functions-reference-python.md) [ C# ](functions-reference-csharp.md) [(библиотека классов), скрипт (. CSX), JavaScript C# ](functions-dotnet-class-library.md), PowerShell, Python [](functions-reference-node.md) | [Расширение Функций Azure для VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) добавляет поддержку средства "Функции" в VS Code. Требуются основные инструменты. Поддерживает разработку на платформах Linux, MacOS и Windows при использовании версии 2.x основных инструментов. Для получения дополнительных сведений см. статью [Создание функции в Azure с помощью Visual Studio Code](functions-create-first-function-vs-code.md). |
| [Командная строка или терминал](functions-run-local.md) | [](functions-reference-powershell.md) [](functions-reference-python.md) [ C# ](functions-reference-csharp.md) [(библиотека классов), скрипт (. CSX), JavaScript C# ](functions-dotnet-class-library.md), PowerShell, Python [](functions-reference-node.md) | [Основные инструменты службы "Функции Azure"] предоставляет основную среду выполнения и шаблоны для создания функций, которые обеспечивают локальную разработку. Версия 2.x поддерживает разработку в Linux, MacOS и Windows. Во всех средах применяют основные инструменты для локальной среды выполнения средства "Функции". |
| [Visual Studio 2019](functions-develop-vs.md) | [C# (библиотека классов)](functions-dotnet-class-library.md) | Средства функций Azure включены в рабочую нагрузку **разработки Azure** [Visual Studio 2019](https://www.visualstudio.com/vs/) и более поздних версий. Позволяет компилировать функции в библиотеке классов и публиковать библиотеки DLL в Azure. Включает основные инструменты для локального тестирования. Дополнительные сведения см. в статье [Инструменты Функций Azure для Visual Studio](functions-develop-vs.md). |
| [Maven](functions-create-first-java-maven.md) (различные) | [Java](functions-reference-java.md) | Интегрируется с основными инструментами и обеспечивает возможность разработки функций Java. Версия 2.x поддерживает разработку в Linux, MacOS и Windows. Дополнительные сведения см. в статье [Создание первой функции с помощью Java и Maven](functions-create-first-java-maven.md). Также поддерживает разработку с помощью [Eclipse](functions-create-maven-eclipse.md) и [IntelliJ IDEA](functions-create-maven-intellij.md). |

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

Каждая из этих локальных сред разработки позволяет создавать проекты приложений-функций и использовать предопределенные шаблоны решения "Функции" для создания новых функций. Каждая из них использует основные инструменты, чтобы вы могли тестировать и отлаживать функции в реальной среде выполнения решения "Функции" на своем компьютере как и любое другое приложение. Вы также можете опубликовать проект приложения функции из любой из этих сред в Azure.  

## <a name="next-steps"></a>Дополнительная информация

+ Дополнительные сведения о локальной разработке скомпилированных C# функций с помощью visual Studio 2019 см. в статье [разработка функций Azure с помощью Visual Studio](functions-develop-vs.md).
+ Дополнительные сведения о локальной разработке функций с помощью VS Code на компьютере Mac, Linux или Windows см. в статье [развертывание функций Azure из VS Code](/azure/javascript/tutorial-vscode-serverless-node-01).
+ Дополнительные сведения о разработке функций из командной строки или терминала см. в статье [Как программировать и тестировать Функции Azure в локальной среде](functions-run-local.md).

<!-- LINKS -->

[Основные инструменты службы "Функции Azure"]: https://www.npmjs.com/package/azure-functions-core-tools
[портал Azure]: https://portal.azure.com 
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
