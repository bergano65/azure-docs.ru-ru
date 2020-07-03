---
title: Создание первой функции на портале Azure
description: Узнайте, как создать первую функцию Azure, выполняемую без сервера, с помощью портала Azure.
ms.assetid: 96cf87b9-8db6-41a8-863a-abb828e3d06d
ms.topic: how-to
ms.date: 03/26/2020
ms.custom: mvc, devcenter, cc996988-fb4f-47
ms.openlocfilehash: 030af8a289daaf03d17f8402e8d603e893657853
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83123632"
---
# <a name="create-your-first-function-in-the-azure-portal"></a>Создание первой функции на портале Azure

Решение "Функции Azure" позволяет выполнять код в бессерверной среде без необходимости сначала создавать виртуальную машину или публиковать веб-приложение. Из этой статьи вы узнаете, как использовать функции Azure для создания функции триггера HTTP "Hello World" в портал Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Разработчики C# могут [создать функцию в Visual Studio 2019](functions-create-your-first-function-visual-studio.md), а не на портале. 

## <a name="sign-in-to-azure"></a>Вход в Azure

Войдите на [портал Azure](https://portal.azure.com) с помощью своей учетной записи Azure.

## <a name="create-a-function-app"></a>Создание приложения-функции

Для выполнения функций вам понадобится приложение-функция, позволяющее группировать функции в логические единицы и упростить развертывание, масштабирование и совместное использование ресурсов, а также управление ими.

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Затем создайте функцию в новом приложении-функции.

## <a name="create-an-http-trigger-function"></a><a name="create-function"></a>Создание функции триггера HTTP

1. В меню слева окна **функции** выберите **функции**, а затем в верхнем меню выберите **добавить** . 
 
1. В окне **Новая функция** выберите **триггер HTTP**.

    ![Выбор функции триггера HTTP](./media/functions-create-first-azure-function/function-app-select-http-trigger.png)

1. В окне **Новая функция** примите имя по умолчанию для **новой функции**или введите новое имя. 

1. Выберите **Анонимный** в раскрывающемся списке **уровень авторизации** , а затем выберите **создать функцию**.

    Azure создает функцию триггера HTTP. Теперь вы можете запустить новую функцию, отправив HTTP-запрос.

## <a name="test-the-function"></a>Проверка функции

1. В новой функции триггера HTTP выберите **код + тест** в меню слева, а затем в верхнем меню выберите **получить URL-адрес функции** .

    ![Выбор получения URL-адреса функции](./media/functions-create-first-azure-function/function-app-select-get-function-url.png)

1. В диалоговом окне **Получение URL-адреса функции** в раскрывающемся списке выберите **значение по умолчанию** , а затем щелкните значок **Копировать в буфер обмена** . 

    ![Копирование URL-адреса функции с портала Azure](./media/functions-create-first-azure-function/function-app-develop-tab-testing.png)

1. Вставьте URL-адрес функции в адресную строку браузера. Добавьте значение строки запроса `?name=<your_name>` в конец этого URL-адреса и нажмите клавишу ВВОД, чтобы выполнить запрос. 

    Следующий пример демонстрирует ответ в браузере:

    ![Ответ функции в браузере.](./media/functions-create-first-azure-function/function-app-browser-testing.png)

    URL-адрес запроса включает ключ, который по умолчанию необходим для доступа к функции по протоколу HTTP.

1. При выполнении функции сведения о трассировке записываются в журналы. Чтобы просмотреть выходные данные трассировки, вернитесь на страницу **код + тест** на портале и разверните стрелку **журналы** в нижней части страницы.

   ![Средство просмотра журналов Функций на портале Azure.](./media/functions-create-first-azure-function/function-view-logs.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Дальнейшие действия

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

