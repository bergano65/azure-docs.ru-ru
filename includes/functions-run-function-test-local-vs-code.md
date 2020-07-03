---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/12/2020
ms.author: glenga
ms.openlocfilehash: f4af3c202d4f00c4ac3041921175c92226f0db7c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/26/2020
ms.locfileid: "76964155"
---
## <a name="run-the-function-locally"></a>Локальное выполнение функции

Visual Studio Code интегрируется с [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md), чтобы перед публикацией в Azure можно было запустить этот проект на локальном компьютере разработки.

1. Чтобы вызвать функцию, нажмите клавишу F5, чтобы запустить проект приложения-функции. Выходные данные основных инструментов отображаются на панели **Terminal** (Терминал).

1. Если вы еще не установили Azure Functions Core Tools, выберите команду **Установить** в приглашении. При установке Core Tools приложение запускается на панели **Терминал**. Отобразится URL-адрес конечной точки активируемой HTTP-запросом функции, которая выполняется локально. 

    ![Локальные выходные данные в Azure](./media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)

1. Запустив Core Tools, перейдите по указанному далее URL-адресу, чтобы выполнить запрос GET, который включает строку запроса `?name=Functions`.

    <http://localhost:7071/api/HttpExample?name=Functions>

1. Возвращается ответ, который в браузере выглядит следующим образом:

    ![Ответ функции localhost в браузере](./media/functions-run-function-test-local-vs-code/functions-test-local-browser.png)

1. Сведения о запросе отображаются на панели **Терминал**.

    ![Выполнение функции на панели "Терминал"](./media/functions-run-function-test-local-vs-code/function-execution-terminal.png)

1. Нажмите сочетание клавиш CTRL+C, чтобы остановить Core Tools и отключить отладчик.
