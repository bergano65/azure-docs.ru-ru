---
title: Создание функции в Azure с помощью Visual Studio Code
description: Создание и публикация в Azure простой функции, активируемой по HTTP, с помощью расширения "Функции Azure" для Visual Studio Code.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: функции azure, функции, обработка событий, вычисления, независимая архитектура
ms.service: azure-functions
ms.devlang: multiple
ms.topic: quickstart
ms.date: 09/07/2018
ms.author: glenga
ms.custom: mvc, devcenter
ms.openlocfilehash: c5889d89ec8c913d9300fa85318a16b4eb452d3b
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/30/2018
ms.locfileid: "52633764"
---
# <a name="create-your-first-function-using-visual-studio-code"></a>Создание функции в Azure с помощью Visual Studio Code

Решение "Функции Azure" позволяет выполнять код в [бессерверной](https://azure.microsoft.com/solutions/serverless/) среде без необходимости создавать виртуальную машину или публиковать веб-приложение.

В этой статье описано, как с помощью [Расширение "Функции Azure" для Visual Studio Code] создать и проверить функцию Hello World на локальном компьютере с помощью Microsoft Visual Studio Code. Затем вы опубликуете код функции в Azure.

![Код функций Azure в проекте Visual Studio](./media/functions-create-first-function-vs-code/functions-vscode-intro.png)

Сейчас это расширение поддерживает функции C#, JavaScript и Java. Описанные в этой статье шаги могут различаться в зависимости от выбранного языка для проекта Функции Azure. Расширение сейчас доступно в режиме предварительной версии. Дополнительные сведения см. в разделе [Расширение "Функции Azure" для Visual Studio Code].

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим кратким руководством сделайте следующее:

* Установите [Visual Studio Code](https://code.visualstudio.com/) на одной из [поддерживаемых платформ](https://code.visualstudio.com/docs/supporting/requirements#_platforms). В нашем в статье примере функция создавалась и тестировалась на устройстве под управлением macOS (High Sierra).

* Установите [основные инструменты расширения "Функции Azure"](functions-run-local.md#v2) версии 2.x, имеющей статус предварительной.

* Установите необходимые дополнительные средства для выбранного языка:

    | Язык | Добавочный номер |
    | -------- | --------- |
    | **C#** | [C# для Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp);<br/>[Средства .NET Core CLI](https://docs.microsoft.com/dotnet/core/tools/?tabs=netcore2x)*   |
    | **Java** | [Отладчик для Java](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug)<br/>[Java 8](https://aka.ms/azure-jdks);<br/>[Maven 3+](https://maven.apache.org/) |
    | **JavaScript** | [Node 8.0+](https://nodejs.org/)  |

    \* Также требуется для основных инструментов.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)]

[!INCLUDE [functions-create-function-app-vs-code](../../includes/functions-create-function-app-vs-code.md)]

## <a name="create-an-http-triggered-function"></a>Создание функции, активируемой HTTP

1. В расширении "**Функции Azure**" щелкните значок Create Function (Создать функцию).

    ![Создание функции](./media/functions-create-first-function-vs-code/create-function.png)

1. Укажите папку со своим проектом приложения-функции и выберите шаблон функции **HTTP trigger** (Триггер HTTP).

    ![Выбор шаблона функции, активируемой HTTP-запросом](./media/functions-create-first-function-vs-code/create-function-choose-template.png)

1. Введите `HTTPTrigger`, чтобы задать имя функции и нажмите клавишу ВВОД, после чего укажите способ проверки подлинности **Anonymous** (Анонимно).

    ![Анонимная аутентификация](./media/functions-create-first-function-vs-code/create-function-anonymous-auth.png)

    С помощью шаблона функции, активируемой HTTP-запросом, будет создана функция на выбранном вами языке.

    ![Шаблон функции, активируемой HTTP-запросом, в Visual Studio Code](./media/functions-create-first-function-vs-code/new-function-full.png)

К функции можно добавить входные и выходные привязки, изменив файл function.json. См. дополнительные сведения о [триггерах и привязках в Функциях Azure](functions-triggers-bindings.md).

Созданный проект функции и функцию, активируемую HTTP, можно протестировать на локальном компьютере.

## <a name="test-the-function-locally"></a>Локальное тестирование функции

Основные инструменты службы "Функции Azure" позволяют запускать проекты функций Azure на локальном компьютере разработчика. Вам будет предложено установить эти инструменты при первом запуске функции из Visual Studio Code.  

1. Чтобы протестировать созданную функцию, установите точку останова в коде функции и нажмите клавишу F5 для запуска проекта приложения-функции. Выходные данные основных инструментов отображаются на панели **Terminal** (Терминал).

1. На панели **Terminal** (Терминал) скопируйте URL-адрес конечной точки функции, активируемой HTTP-запросом.

    ![Локальные выходные данные в Azure](./media/functions-create-first-function-vs-code/functions-vscode-f5.png)

1. Вставьте URL-адрес запроса в адресную строку браузера. Добавьте строку запроса `?name=<yourname>` в этот URL-адрес и выполните запрос. Выполнение функции приостановится при достижении точки останова.

    ![Выполнение функции до точки останова в Visual Studio Code](./media/functions-create-first-function-vs-code/function-debug-vscode-js.png)

1. При продолжении выполнения функции в браузере отобразится следующий ответ на запрос GET:

    ![Ответ функции localhost в браузере](./media/functions-create-first-function-vs-code/functions-test-local-browser.png)

1. Нажмите клавиши SHIFT+F1, чтобы остановить отладку.

Убедившись, что функция выполняется правильно на локальном компьютере, опубликуйте проект в Azure.

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

## <a name="test-your-function-in-azure"></a>Тестирование функции в Azure

1. Скопируйте URL-адрес HTTP-триггера на панели **Output** (Выходные данные). Как и в предыдущем случае, добавьте строку запроса `?name=<yourname>` в конец этого URL-адреса и выполните запрос.

    URL-адрес для вызова функции, активируемой HTTP-запросом, должен быть указан в таком формате:

        http://<functionappname>.azurewebsites.net/api/<functionname>?name=<yourname> 

1. Вставьте этот URL-адрес HTTP-запроса в адресную строку браузера. Ниже показан ответ в браузере на удаленный запрос GET, возвращаемый функцией: 

    ![Ответ функции в браузере](./media/functions-create-first-function-vs-code/functions-test-remote-browser.png)

## <a name="next-steps"></a>Дополнительная информация

С помощью Visual Studio Code вы создали приложение-функцию с простой функцией, активируемой HTTP-запросом. Дополнительные сведения о разработке функций на определенном языке см. в справочниках по языкам [JavaScript](functions-reference-node.md), [.NET](functions-dotnet-class-library.md) или [Java](functions-reference-java.md).

Далее можно узнать о локальном тестировании и отладке из терминала или командной строки основных инструментов "Функций Azure".

> [!div class="nextstepaction"]
> [Создание и тестирование кода на локальном компьютере](functions-run-local.md)

[Azure Functions Core Tools]: functions-run-local.md
[Расширение "Функции Azure" для Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
