---
title: Создание функции в Azure с помощью Visual Studio Code
description: Создание и публикация в Azure простой функции, активируемой по HTTP, с помощью расширения "Функции Azure" для Visual Studio Code.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: функции azure, функции, обработка событий, вычисления, независимая архитектура
ms.service: azure-functions
ms.topic: quickstart
ms.date: 06/25/2019
ms.author: glenga
ms.custom: mvc, devcenter
ms.openlocfilehash: 52f682f7c7f06056be122b33d27592a55a01be94
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70744070"
---
# <a name="create-your-first-function-using-visual-studio-code"></a>Создание функции в Azure с помощью Visual Studio Code

Решение "Функции Azure" позволяет выполнять код в [бессерверной](https://azure.microsoft.com/solutions/serverless/) среде без необходимости создавать виртуальную машину или публиковать веб-приложение.

В этой статье описано, как с помощью [Расширение "Функции Azure" для Visual Studio Code] создать и проверить функцию Hello World на локальном компьютере с помощью Microsoft Visual Studio Code. Затем вы опубликуете код функции в Azure.

![Код функций Azure в проекте Visual Studio](./media/functions-create-first-function-vs-code/functions-vscode-intro.png)

Сейчас это расширение поддерживает функции C#, JavaScript, Java и Python. Шаги, описанные этой и следующей статьях, предназначены лишь для функций JavaScript и C#. Сведения о создании и публикации функций Python с помощью Visual Studio Code см. в статье [Deploy Python to Azure Functions](https://code.visualstudio.com/docs/python/tutorial-azure-functions) (Развертывание Python в Функции Azure). Сведения о создании и публикации функций PowerShell с помощью Visual Studio Code см. в статье [Создание первой функции PowerShell в Azure (предварительная версия)](functions-create-first-function-powershell.md). 

Расширение сейчас доступно в режиме предварительной версии. Дополнительные сведения см. в разделе [Расширение "Функции Azure" для Visual Studio Code].

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим кратким руководством сделайте следующее:

* Установите [Visual Studio Code](https://code.visualstudio.com/) на одной из [поддерживаемых платформ](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

* Установите [Основные инструменты Функции Azure](functions-run-local.md#v2) версии 2.x.

* Установите необходимые дополнительные средства для выбранного языка:

    | Язык | Требование |
    | -------- | --------- |
    | **C#** | [расширение C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp);  |
    | **JavaScript** | [Node.js](https://nodejs.org/)<sup>*</sup> | 
 
    <sup>*</sup>Активная версия LTS и версия Maintenance LTS (рекомендуются версии 8.11.1 и 10.14.1).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)]

[!INCLUDE [functions-create-function-app-vs-code](../../includes/functions-create-function-app-vs-code.md)]

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

Убедившись, что функция выполняется правильно на локальном компьютере, опубликуйте проект в Azure.

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

## <a name="run-the-function-in-azure"></a>Запуск функции в Azure

1. Скопируйте URL-адрес HTTP-триггера на панели **Output** (Выходные данные). Этот URL-адрес содержит ключ функции, который передается в параметр запроса `code`. Как и в предыдущем случае, добавьте строку запроса `?name=<yourname>` в конец этого URL-адреса и выполните запрос.

    URL-адрес для вызова функции, активируемой HTTP-запросом, должен быть указан в таком формате:

        http://<functionappname>.azurewebsites.net/api/<functionname>?code=<function_key>&name=<yourname> 

1. Вставьте этот URL-адрес HTTP-запроса в адресную строку браузера. Ниже показан ответ в браузере на удаленный запрос GET, возвращаемый функцией: 

    ![Ответ функции в браузере](./media/functions-create-first-function-vs-code/functions-test-remote-browser.png)

## <a name="next-steps"></a>Дополнительная информация

С помощью Visual Studio Code вы создали приложение-функцию с простой функцией, активируемой HTTP-запросом. В следующей статье вы расширите эту функцию, добавив выходную привязку. Эта привязка записывает строку из HTTP-запроса в сообщение очереди Хранилища очередей Azure. Кроме того, в следующей статье описана очистка новых ресурсов Azure с помощью удаления созданной группы ресурсов.

> [!div class="nextstepaction"]
> [Connect functions to Azure Storage using Visual Studio Code](functions-add-output-binding-storage-queue-vs-code.md) (Подключение функций к службе хранилища Azure с помощью Visual Studio Code)

[Azure Functions Core Tools]: functions-run-local.md
[Расширение "Функции Azure" для Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
