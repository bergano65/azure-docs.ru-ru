---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/16/2020
ms.author: glenga
ms.openlocfilehash: c54145cf48912d3911a39e681d85cb6907be8e52
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76842322"
---
## <a name="run-the-function-locally"></a>Локальное выполнение функции

Основные инструменты Функций Azure интегрируются с Visual Studio Code, что дает возможность запускать и отлаживать проект Функций Azure локально.  

1. Чтобы отладить свою функцию, вставьте вызов командлета [`Wait-Debugger`](/powershell/module/microsoft.powershell.utility/wait-debugger?view=powershell-6) в код функции, прежде чем подключить отладчик, затем нажмите клавишу F5, чтобы запустить проект приложения-функции и подключить отладчик. Выходные данные основных инструментов отображаются на панели **Terminal** (Терминал).

1. На панели **Terminal** (Терминал) скопируйте URL-адрес конечной точки функции, активируемой HTTP-запросом.

    ![Локальные выходные данные в Azure](./media/functions-run-function-test-local-vs-code-ps/functions-vscode-f5.png)

1. Добавьте строку запроса `?name=<yourname>` к этому URL-адресу, а затем используйте `Invoke-RestMethod` во второй командной строке PowerShell, чтобы выполнить запрос, пример показано ниже.

    ```powershell
    PS > Invoke-RestMethod -Method Get -Uri http://localhost:7071/api/HttpTrigger?name=PowerShell
    Hello PowerShell
    ```

    Вы также можете выполнить запрос GET в браузере.

    Когда вы вызываете конечную точку HttpTrigger, не передавая `name` в качестве параметра запроса или в теле, функция возвращает ошибку [HttpStatusCode]::BadRequest. Когда вы просматриваете код в run.ps1, вы поймете, что эта ошибка возникла намеренно.

1. Нажмите клавиши SHIFT+F5, чтобы остановить отладку.

Убедившись, что функция выполняется правильно на локальном компьютере, опубликуйте проект в Azure.

> [!NOTE]
> Не забудьте удалить все вызовы `Wait-Debugger` перед публикацией своих функций в Azure. 