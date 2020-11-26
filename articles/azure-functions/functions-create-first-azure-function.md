---
title: Создание первой функции на портале Azure
description: Узнайте, как создать первую функцию Azure, выполняемую без сервера, с помощью портала Azure.
ms.assetid: 96cf87b9-8db6-41a8-863a-abb828e3d06d
ms.topic: how-to
ms.date: 03/26/2020
ms.custom: devx-track-csharp, mvc, devcenter, cc996988-fb4f-47
ms.openlocfilehash: 8a6f154dc3c7a2984c687d55af2cf51a60169d6b
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/26/2020
ms.locfileid: "96182569"
---
# <a name="create-your-first-function-in-the-azure-portal"></a>Создание первой функции на портале Azure

Решение "Функции Azure" позволяет выполнять код в бессерверной среде без необходимости сначала создавать виртуальную машину или публиковать веб-приложение. Из этой статьи вы узнаете, как использовать функции Azure для создания функции триггера HTTP "Hello World" в портал Azure.

Рекомендуется [разрабатывать функции локально](functions-develop-local.md) и публиковать их в приложении-функции в Azure.  
Используйте одну из следующих ссылок, чтобы приступить к работе с выбранной локальной средой разработки и языком:

| Visual Studio Code | Терминал/Командная строка | Visual Studio |
| --- | --- | --- |
|  &bull;&nbsp;[Начало работы с C #](./create-first-function-vs-code-csharp.md?pivots=programming-language-csharp)<br/>&bull;&nbsp;[Начало работы с Java](./create-first-function-vs-code-csharp.md?pivots=programming-language-java)<br/>&bull;&nbsp;[Приступая к работе с JavaScript](./create-first-function-vs-code-csharp.md?pivots=programming-language-javascript)<br/>&bull;&nbsp;[Начало работы с PowerShell](./create-first-function-vs-code-csharp.md?pivots=programming-language-powershell)<br/>&bull;&nbsp;[Начало работы с Python](./create-first-function-vs-code-csharp.md?pivots=programming-language-python) |&bull;&nbsp;[Начало работы с C #](./create-first-function-cli-csharp.md?pivots=programming-language-csharp)<br/>&bull;&nbsp;[Начало работы с Java](./create-first-function-cli-csharp.md?pivots=programming-language-java)<br/>&bull;&nbsp;[Приступая к работе с JavaScript](./create-first-function-cli-csharp.md?pivots=programming-language-javascript)<br/>&bull;&nbsp;[Начало работы с PowerShell](./create-first-function-cli-csharp.md?pivots=programming-language-powershell)<br/>&bull;&nbsp;[Начало работы с Python](./create-first-function-cli-csharp.md?pivots=programming-language-python) | [Приступая к работе с C#](functions-create-your-first-function-visual-studio.md) |

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="sign-in-to-azure"></a>Вход в Azure

Войдите на [портал Azure](https://portal.azure.com) с помощью своей учетной записи Azure.

## <a name="create-a-function-app"></a>Создание приложения-функции

Для выполнения функций вам понадобится приложение-функция, позволяющее группировать функции в логические единицы и упростить развертывание, масштабирование и совместное использование ресурсов, а также управление ими.

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Затем создайте функцию в новом приложении-функции.

## <a name="create-an-http-trigger-function"></a><a name="create-function"></a>Создание функции, активируемой HTTP

1. В меню слева в окне **Функции** выберите **Функции**, а затем в верхнем меню выберите **Добавить**. 
 
1. В окне **Новая функция** выберите **Триггер HTTP**.

    ![Выбор функции, активируемой HTTP](./media/functions-create-first-azure-function/function-app-select-http-trigger.png)

1. В окне **Новая функция** для пункта **Новая функция** оставьте имя по умолчанию или введите новое имя. 

1. Из раскрывающегося списка **Уровень авторизации** выберите **Анонимный**, а затем выберите **Создать функцию**.

    Azure создает функцию, активируемую HTTP. Теперь вы можете запустить новую функцию, отправив HTTP-запрос.

## <a name="test-the-function"></a>Проверка функции

1. В новой функции, активируемой HTTP, выберите **Code + Test** (Код + Тест) в меню слева, а затем в верхнем меню выберите **Получить URL-адрес функции**.

    ![Выбор "Получить URL-адрес функции"](./media/functions-create-first-azure-function/function-app-select-get-function-url.png)

1. В диалоговом окне **Получить URL-адрес функции** в раскрывающемся списке выберите вариант **по умолчанию**, а затем выберите значок **Копировать в буфер обмена**. 

    ![Копирование URL-адреса функции с портала Azure](./media/functions-create-first-azure-function/function-app-develop-tab-testing.png)

1. Вставьте URL-адрес функции в адресную строку браузера. Добавьте значение строки запроса `?name=<your_name>` в конец этого URL-адреса и нажмите клавишу Enter, чтобы выполнить этот запрос. 

    Следующий пример демонстрирует ответ в браузере:

    ![Ответ функции в браузере.](./media/functions-create-first-azure-function/function-app-browser-testing.png)

    URL-адрес запроса включает ключ, который по умолчанию необходим для доступа к функции по протоколу HTTP.

1. При выполнении функции сведения о трассировке записываются в журналы. Чтобы просмотреть выходные данные трассировки, вернитесь на страницу **Code + Test** (Код + Тест) на портале и разверните список **Журналы** в нижней части страницы.

   ![Средство просмотра журналов Функций на портале Azure.](./media/functions-create-first-azure-function/function-view-logs.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Дальнейшие действия

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]