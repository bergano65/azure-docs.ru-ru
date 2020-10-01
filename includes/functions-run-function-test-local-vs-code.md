---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/28/2020
ms.author: glenga
ms.openlocfilehash: 43da0ea4ddfc5410425465d436522523739218fe
ms.sourcegitcommit: b48e8a62a63a6ea99812e0a2279b83102e082b61
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/28/2020
ms.locfileid: "91408583"
---
## <a name="run-the-function-locally"></a>Локальное выполнение функции

Visual Studio Code интегрируется с [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md), чтобы перед публикацией в Azure можно было запустить этот проект на локальном компьютере разработки.

1. Чтобы вызвать функцию, нажмите клавишу <kbd>F5</kbd>, чтобы запустить проект приложения-функции. Выходные данные основных инструментов отображаются на панели **Terminal** (Терминал).

1. Если вы еще не установили Azure Functions Core Tools, выберите команду **Установить** в приглашении. При установке Core Tools приложение запускается на панели **Терминал**. Отобразится URL-адрес конечной точки активируемой HTTP-запросом функции, которая выполняется локально.

    ![Выходные данные VS Code локальной функции](./media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)

1. Запустив Core Tools, перейдите по указанному далее URL-адресу, чтобы выполнить запрос GET, который включает строку запроса `?name=Functions`.

    `http://localhost:7071/api/HttpExample?name=Functions`

1. Возвращается ответ, который в браузере выглядит следующим образом:

    ![Браузер: пример выходных данных localhost](./media/functions-run-function-test-local-vs-code/functions-test-local-browser.png)

1. Сведения о запросе отображаются на панели **Терминал**.

    ![Запуск узла задач: выходные данные терминала VS Code](./media/functions-run-function-test-local-vs-code/function-execution-terminal.png)

1. Нажмите клавиши <kbd>CTRL+C</kbd>, чтобы остановить Core Tools и отключить отладчик.
