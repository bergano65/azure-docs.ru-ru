---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: 0bb0c22227946cba6790b536b3cb8db24af3ccbc
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422869"
---
## <a name="run-the-function-locally"></a>Локальное выполнение функции

1. Выполните функцию, запустив локальное хост-приложение среды выполнения Функций Azure из папки *LocalFunctionProj*:

    ```
    func start
    ```

    Ближе к концу выходных данных появятся следующие строки: 
    
    <pre>
    ...
    
    Now listening on: http://0.0.0.0:7071
    Application started. Press Ctrl+C to shut down.
    
    Http Functions:
    
            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ...
    
    </pre>
    
    >[!NOTE]  
    > Если результат HttpExample не похож на пример ниже, скорее всего, вы запустили хост-приложение из папки, отличной от корневой папки проекта. В этом случае остановите хост-приложение клавишами **CTRL**+**C**, перейдите в корневую папку проекта и снова выполните указанную выше команду.

1. Скопируйте URL-адрес функции `HttpExample` из этих выходных данных в браузер и добавьте строку запроса `?name=<YOUR_NAME>`, сформировав полный URL-адрес, например `http://localhost:7071/api/HttpExample?name=Functions`. В браузере должно отобразиться сообщение, например `Hello Functions`:

    ![Получившаяся функция выполняется локально в браузере](./media/functions-run-function-test-local-cli/function-test-local-browser.png)

1. Терминал, в котором вы запустили проект, также выводит данные журнала при выполнении запросов.

1. Когда все будет готово, нажмите клавиши **CTRL**+**C** и выберите `y`, чтобы отключить хост-приложение функции.
