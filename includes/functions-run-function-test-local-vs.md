---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/06/2020
ms.author: glenga
ms.openlocfilehash: b4b2409928b6a4196738c7cc6c7040e781d34686
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/05/2020
ms.locfileid: "80056623"
---
1. Чтобы запустить функцию, нажмите клавишу F5 в Visual Studio. Возможно, вам потребуется включить исключение брандмауэра, чтобы инструменты могли обрабатывать HTTP-запросы. Уровни авторизации никогда не применяются при запуске функции в локальной среде.

2. Скопируйте URL-адрес функции из выходных данных среды выполнения функций Azure.

    ![Локальная среда выполнения Azure](./media/functions-run-function-test-local-vs/functions-debug-local-vs.png)

3. Вставьте URL-адрес запроса в адресную строку браузера. Добавьте строку запроса `?name=<YOUR_NAME>` в этот URL-адрес и выполните запрос. На следующем изображении показан ответ в браузере на локальный запрос GET, возвращаемый функцией: 

    ![Ответ функции localhost в браузере](./media/functions-run-function-test-local-vs/functions-run-browser-local-vs.png)

4. Чтобы остановить отладку, нажмите клавиши SHIFT+F5 в Visual Studio.