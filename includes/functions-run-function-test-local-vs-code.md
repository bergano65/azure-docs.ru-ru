---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/12/2020
ms.author: glenga
ms.openlocfilehash: f4075b8d05c179e8115ff46c9f82751817372491
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76842222"
---
## <a name="run-the-function-locally"></a>Локальное выполнение функции

Visual Studio Code интегрируется с [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md), чтобы перед публикацией в Azure можно было запустить этот проект на локальном компьютере разработки.

1. Чтобы вызвать функцию, нажмите клавишу F5, чтобы запустить проект приложения-функции. Выходные данные основных инструментов отображаются на панели **Terminal** (Терминал).

1. Если вы еще не установили Azure Functions Core Tools, выберите команду **Установить** в приглашении. При установке Core Tools приложение запускается на панели **Терминал**.

1. На панели **Terminal** (Терминал) скопируйте URL-адрес конечной точки функции, активируемой HTTP-запросом. 

    ![Локальные выходные данные в Azure](./media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)

1. Вставьте URL-адрес запроса в адресную строку браузера. Добавьте строку запроса `?name=<yourname>` в этот URL-адрес и выполните запрос GET. 

1. Возвращается ответ, который в браузере выглядит следующим образом:

    ![Ответ функции localhost в браузере](./media/functions-run-function-test-local-vs-code/functions-test-local-browser.png)

1. Нажмите сочетание клавиш Shift + F5, чтобы остановить Core Tools и отключить отладчик.
