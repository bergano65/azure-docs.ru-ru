---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/28/2020
ms.author: glenga
ms.openlocfilehash: a3cb0986637d5ce238930fb87aef71fed684097a
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2020
ms.locfileid: "96842283"
---
## <a name="run-the-function-locally"></a>Локальное выполнение функции

Visual Studio Code интегрируется с [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md), чтобы перед публикацией в Azure можно было запустить этот проект на локальном компьютере разработки.

1. Чтобы вызвать функцию, нажмите клавишу <kbd>F5</kbd>, чтобы запустить проект приложения-функции. Выходные данные основных инструментов отображаются на панели **Terminal** (Терминал). При возникновении проблем с запуском в Windows убедитесь, что в качестве терминала по умолчанию для Visual Studio Code не используется **оболочка WSL**.

1. Если вы еще не установили Azure Functions Core Tools, выберите команду **Установить** в приглашении. При установке Core Tools приложение запускается на панели **Терминал**. Отобразится URL-адрес конечной точки активируемой HTTP-запросом функции, которая выполняется локально.

    ![Выходные данные VS Code локальной функции](./media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)

1. Запустив Core Tools, перейдите по указанному далее URL-адресу, чтобы выполнить запрос GET, который включает строку запроса `?name=Functions`.

    `http://localhost:7071/api/HttpExample?name=Functions`

1. Возвращается ответ, который в браузере выглядит следующим образом:

    ![Браузер: пример выходных данных localhost](./media/functions-run-function-test-local-vs-code/functions-test-local-browser.png)

1. Сведения о запросе отображаются на панели **Терминал**.

    ![Запуск узла задач: выходные данные терминала VS Code](./media/functions-run-function-test-local-vs-code/function-execution-terminal.png)

1. Нажмите клавиши <kbd>CTRL+C</kbd>, чтобы остановить Core Tools и отключить отладчик.
