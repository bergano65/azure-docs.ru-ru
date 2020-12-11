---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 02/19/2020
ms.author: glenga
ms.openlocfilehash: a5c113849296275432acf1f5603377a1909a2c04
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2020
ms.locfileid: "96842343"
---
## <a name="run-the-function-locally"></a>Локальное выполнение функции

Основные инструменты Функций Azure интегрируются с Visual Studio Code, что дает возможность запускать и отлаживать проект Функций Azure локально. Инструкции по отладке с помощью Visual Studio Code см. в [этой статье](../articles/azure-functions/functions-debug-powershell-local.md). 
1. Чтобы вызвать функцию, нажмите клавишу <kbd>F5</kbd>, чтобы запустить проект приложения-функции. Выходные данные основных инструментов отображаются на панели **Terminal** (Терминал). При возникновении проблем с запуском в Windows убедитесь, что в качестве терминала по умолчанию для Visual Studio Code не используется **оболочка WSL**.

1. На панели **Terminal** (Терминал) скопируйте URL-адрес конечной точки функции, активируемой HTTP-запросом.

    ![Локальные выходные данные в Azure](./media/functions-run-function-test-local-vs-code-ps/functions-vscode-f5.png)

1. Добавьте строку запроса `?name=<yourname>` к этому URL-адресу, а затем используйте `Invoke-RestMethod` во второй командной строке PowerShell, чтобы выполнить запрос, пример показано ниже.

    ```powershell
    PS > Invoke-RestMethod -Method Get -Uri http://localhost:7071/api/HttpTrigger?name=PowerShell
    Hello PowerShell
    ```

    Вы также можете выполнить запрос GET в браузере по следующему URL-адресу:

    `http://localhost:7071/api/HttpExample?name=PowerShell`

    Когда вы вызываете конечную точку HttpTrigger, не передавая `name` в качестве параметра запроса или в тексте, функция возвращает ошибку `BadRequest`. Когда вы просматриваете код в run.ps1, вы поймете, что эта ошибка возникла намеренно.

1. Сведения о запросе отображаются на панели **Терминал**.

    ![Выполнение функции на панели "Терминал"](./media/functions-run-function-test-local-vs-code-ps/function-execution-terminal.png)

1. Чтобы остановить работу Core Tools, нажмите сочетание клавиш **CTRL+C**.

Убедившись, что функция выполняется правильно на локальном компьютере, опубликуйте проект в Azure.
