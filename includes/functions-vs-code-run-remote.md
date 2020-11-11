---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/01/2020
ms.author: glenga
ms.openlocfilehash: e9465e014c1b8770f8968cbee89a5b455ad53470
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424824"
---
## <a name="run-the-function-in-azure"></a>Запуск функции в Azure

1. Вернитесь в область **Azure: Functions** (Azure: Функции) на панели слева и откройте новое приложение-функцию в своей подписке. Разверните **Функции** , щелкните правой кнопкой мыши (Windows) или щелкните, нажав клавишу <kbd>CTRL</kbd> (macOS), элемент **HttpExample** , а затем выберите команду **Copy function URL** (Копировать URL-адрес функции).

    ![Скопируйте URL-адрес функции для создания нового HTTP-триггера](./media/functions-vs-code-run-remote/function-copy-endpoint-url.png)

1. Вставьте этот URL-адрес для HTTP-запроса в адресную строку браузера, добавьте строку запроса `name` в качестве `?name=Functions` в конец этого URL-адреса, а затем выполните запрос. URL-адрес для вызова функции, активируемой HTTP-запросом, должен быть указан в таком формате:

    ```http
    http://<FUNCTION_APP_NAME>.azurewebsites.net/api/httpexample?name=Functions
    ```

    В примере ниже показан ответ в браузере на удаленный запрос GET, возвращаемый функцией:

    ![Ответ функции в браузере](./media/functions-vs-code-run-remote/functions-test-remote-browser.png)
