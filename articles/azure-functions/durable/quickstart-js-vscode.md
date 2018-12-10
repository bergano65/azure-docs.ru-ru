---
title: Создание устойчивой функции в Azure с помощью JavaScript
description: Создание и публикация устойчивой функции Azure с помощью Visual Studio Code.
services: functions
documentationcenter: na
author: ColbyTresness
manager: jeconnoc
keywords: функции azure, функции, обработка событий, вычисления, независимая архитектура
ms.service: azure-functions
ms.devlang: multiple
ms.topic: quickstart
ms.date: 11/07/2018
ms.author: azfuncdf, cotresne, glenga
ms.openlocfilehash: 7dceed4d81f1e1767cbf91804573043d1204beee
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52838911"
---
# <a name="create-your-first-durable-function-in-javascript"></a>Создание устойчивой функции с помощью JavaScript

*Устойчивые функции* — это расширение [Функций Azure](../functions-overview.md), которое позволяет писать функции с отслеживанием состояния в беcсерверной среде. Расширение автоматически управляет состоянием, создает контрольные точки и перезагружается.

В этой статье описано, как использовать расширение Функций Azure в Visual Studio Code, чтобы локально создать и тестировать устойчивую функцию hello world.  Эта функция будет организовывать и объединять в цепочку вызовы других функций. Затем вы опубликуете код функции в Azure.

![Выполнение устойчивых функций в Azure](./media/quickstart-js-vscode/functions-vs-code-complete.png)

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством:

* Установите [Visual Studio Code](https://code.visualstudio.com/download).

* Убедитесь, что используются [новейшие средства Функций Azure](../functions-develop-vs.md#check-your-tools-version).

* Убедитесь, что на компьютере с ОС Windows установлен и запущен [эмулятор службы хранилища Azure](../../storage/common/storage-use-emulator.md). На компьютере с ОС Mac или Linux необходимо использовать саму учетную запись хранения Azure.

* Убедитесь, что установлена версия [Node.js](https://nodejs.org/) 8.0 или более поздняя.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-install-vs-code-extension](../../../includes/functions-install-vs-code-extension.md)]

[!INCLUDE [functions-create-function-app-vs-code](../../../includes/functions-create-function-app-vs-code.md)]

## <a name="create-a-starter-function"></a>Создание функции запуска

Сначала создайте функцию, активируемую протоколом HTTP, которая запускает оркестрацию устойчивых функций.

1. В расширении "**Функции Azure**" щелкните значок Create Function (Создать функцию).

    ![Создание функции](./media/quickstart-js-vscode/create-function.png)

1. Укажите папку со своим проектом приложения-функции и выберите шаблон функции **HTTP trigger** (Триггер HTTP).

    ![Выбор шаблона функции, активируемой HTTP-запросом](./media/quickstart-js-vscode/create-function-choose-template.png)

1. Введите `HttpStart`, чтобы задать имя функции и нажмите клавишу ВВОД, после чего укажите способ проверки подлинности **Anonymous** (Анонимно).

    ![Анонимная аутентификация](./media/quickstart-js-vscode/create-function-anonymous-auth.png)

    С помощью шаблона функции, активируемой HTTP-запросом, будет создана функция на выбранном вами языке.

1. Замените файл index.js поданным ниже кодом JavaScript.

    ```javascript
    const df = require("durable-functions");
    
    module.exports = async function (context, req) {
        const client = df.getClient(context);
        const instanceId = await client.startNew(req.params.functionName, undefined, req.body);
    
        context.log(`Started orchestration with ID = '${instanceId}'.`);
    
        return client.createCheckStatusResponse(context.bindingData.req, instanceId);
    };
    ```

1. Замените файл function.json поданным ниже кодом JSON.

    ```JSON
    {
      "bindings": [
        {
          "authLevel": "anonymous",
          "name": "req",
          "type": "httpTrigger",
          "direction": "in",
          "route": "orchestrators/{functionName}",
          "methods": ["post"]
        },
        {
          "name": "$return",
          "type": "http",
          "direction": "out"
        },
        {
          "name": "starter",
          "type": "orchestrationClient",
          "direction": "in"
        }
      ],
      "disabled": false
    }
    ```

Мы создали точку входа в нашу устойчивую функцию. Теперь добавим оркестратор.

## <a name="create-an-orchestrator-function"></a>Создание функции оркестратора

Теперь нужно создать еще одну функцию, которая будет работать в качестве оркестратора. Для удобства мы используем шаблон функции для триггеров HTTP. Код самой функции заменяется кодом оркестратора.

1. Повторите шаги из предыдущего раздела, чтобы создать вторую функцию с помощью шаблона триггера HTTP. Дайте этой функции имя `OrchestratorFunction`.

1. Откройте файл index.js новой функции и замените его содержимое следующим кодом.

    [!code-json[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/index.js)]

1. Откройте файл function.json и замените его следующим кодом JSON.

    [!code-json[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/function.json)]

Мы добавили оркестратор для координации функций действий. Теперь добавим функцию действия, на которое делается ссылка.

## <a name="create-an-activity-function"></a>Создание функции действия

1. Повторите шаги из предыдущих разделов, чтобы создать третью функцию с помощью шаблона триггера HTTP. Дайте этой функции имя `SayHello`.

1. Откройте файл index.js новой функции и замените его содержимое следующим кодом.

    [!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/index.js)]

1. Замените файл function.json поданным ниже кодом JSON.

    [!code-json[Main](~/samples-durable-functions/samples/csx/E1_SayHello/function.json)]

Теперь мы добавили все компоненты, необходимые для начала оркестрации и объединения в цепочку функций действий.

## <a name="test-the-function-locally"></a>Локальное тестирование функции

Основные инструменты службы "Функции Azure" позволяют запускать проекты функций Azure на локальном компьютере разработчика. Вам будет предложено установить эти инструменты при первом запуске функции из Visual Studio Code.  

1. Установите пакет npm устойчивых функций, выполнив `npm install durable-functions` в корневом каталоге приложения-функции.

1. На компьютере с ОС Windows запустите эмулятор службы хранилища Azure и убедитесь, что свойству **AzureWebJobsStorage** в local.settings.json присваивается значение `UseDevelopmentStorage=true`. На компьютере с ОС Mac или Linux свойству **AzureWebJobsStorage** необходимо задать значение строки подключения существующей учетной записи хранения Azure. Далее в этой статье описывается создание учетной записи хранения.

1. Чтобы протестировать созданную функцию, установите точку останова в коде функции и нажмите клавишу F5 для запуска проекта приложения-функции. Выходные данные основных инструментов отображаются на панели **Terminal** (Терминал). Если вы впервые используете устойчивые функции, расширение устойчивых функций установлено, и сборка может занять несколько секунд.

1. На панели **Terminal** (Терминал) скопируйте URL-адрес конечной точки функции, активируемой HTTP-запросом.

    ![Локальные выходные данные в Azure](../media/functions-create-first-function-vs-code/functions-vscode-f5.png)

1. Вставьте URL-адрес HTTP-запроса в адресную строку браузера и просмотрите состояние оркестрации.

1. Нажмите клавиши SHIFT+F1, чтобы остановить отладку.

Убедившись, что функция выполняется правильно на локальном компьютере, опубликуйте проект в Azure.

[!INCLUDE [functions-create-function-app-vs-code](../../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../../includes/functions-publish-project-vscode.md)]

## <a name="test-your-function-in-azure"></a>Тестирование функции в Azure

1. Скопируйте URL-адрес HTTP-триггера на панели **Output** (Выходные данные). URL-адрес для вызова функции, активируемой HTTP-запросом, должен быть указан в таком формате:

        http://<functionappname>.azurewebsites.net/api/<functionname>

1. Вставьте этот URL-адрес HTTP-запроса в адресную строку браузера. При использовании опубликованного приложения ответ состояния должен быть таким же, как и ранее.

## <a name="next-steps"></a>Дополнительная информация

Вы создали и опубликовали приложение устойчивой функции JavaScript с помощью Visual Studio Code.

> [!div class="nextstepaction"]
> [Дополнительные сведения о распространенных шаблонах устойчивых функций](durable-functions-overview.md)