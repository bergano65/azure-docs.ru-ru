---
title: Создание функции с помощью службы "Начальный набор Microsoft Azure для учащихся"
description: Узнайте, как создать функцию Azure в подписке на службу "Начальный набор Microsoft Azure для учащихся"
Customer intent: As a student, I want to be able to create an HTTP triggered Function App within the Student Starter plan so that I can easily add APIs to any project.
author: alexkarcher-msft
ms.topic: how-to
ms.date: 04/29/2020
ms.author: alkarche
ms.openlocfilehash: ffb6378d3dc4cc3fb23ea62157aad393d8ae6642
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83122908"
---
# <a name="create-a-function-using-azure-for-students-starter"></a>Создание функции с помощью службы "Начальный набор Microsoft Azure для учащихся"

В этом руководстве мы создадим функцию HTTP "Hello World" в начальной подписке Azure для учащихся. Мы также рассмотрим доступные возможности в Функциях Azure для этого типа подписки.

*Начальный набор Microsoft Azure для учащихся* предоставляет бесплатный доступ к ресурсам для начала работы с продуктами Azure, которые необходимо разработать в облаке. [Узнайте больше об этом предложении здесь.](https://azure.microsoft.com/offers/ms-azr-0144p/)

Решение "Функции Azure" позволяет выполнять код в [бессерверной](https://azure.microsoft.com/solutions/serverless/) среде без необходимости создавать виртуальную машину или публиковать веб-приложение. [Дополнительные сведения о Функциях Azure см. здесь.](./functions-overview.md)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-function"></a>Создание функции

 Из этой статьи вы узнаете, как использовать функции Azure для создания функции триггера HTTP "Hello World" в портал Azure.

![Создание приложения-функции на портале Azure](./media/functions-create-student-starter/function-app-in-portal-editor.png)

## <a name="sign-in-to-azure"></a>Вход в Azure

Войдите на [портал Azure](https://portal.azure.com) с помощью своей учетной записи Azure.

## <a name="create-a-function-app"></a>Создание приложения-функции

Для выполнения функций вам понадобится приложение-функция, позволяющее группировать функции в логические единицы и упростить развертывание, масштабирование и совместное использование ресурсов, а также управление ими.

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Затем создайте функцию в новом приложении-функции.

## <a name="create-an-http-trigger-function"></a><a name="create-function"></a>Создание функции триггера HTTP

1. В меню слева окна **функции** выберите **функции**, а затем в верхнем меню выберите **добавить** . 
 
1. В окне **Новая функция** выберите **триггер HTTP**.

    ![Выбор функции триггера HTTP](./media/functions-create-student-starter/function-app-select-http-trigger.png)

1. В окне **Новая функция** примите имя по умолчанию для **новой функции**или введите новое имя. 

1. Выберите **Анонимный** в раскрывающемся списке **уровень авторизации** , а затем выберите **создать функцию**.

    Azure создает функцию триггера HTTP. Теперь вы можете запустить новую функцию, отправив HTTP-запрос.

## <a name="test-the-function"></a>Проверка функции

1. В новой функции триггера HTTP выберите **код + тест** в меню слева, а затем в верхнем меню выберите **получить URL-адрес функции** .

    ![Выбор получения URL-адреса функции](./media/functions-create-student-starter/function-app-select-get-function-url.png)

1. В диалоговом окне **Получение URL-адреса функции** в раскрывающемся списке выберите **значение по умолчанию** , а затем щелкните значок **Копировать в буфер обмена** . 

    ![Копирование URL-адреса функции с портала Azure](./media/functions-create-student-starter/function-app-develop-tab-testing.png)

1. Вставьте URL-адрес функции в адресную строку браузера. Добавьте значение строки запроса `?name=<your_name>` в конец этого URL-адреса и нажмите клавишу ВВОД, чтобы выполнить запрос. 

    Следующий пример демонстрирует ответ в браузере:

    ![Ответ функции в браузере.](./media/functions-create-student-starter/function-app-browser-testing.png)

    URL-адрес запроса включает ключ, который по умолчанию необходим для доступа к функции по протоколу HTTP.

1. При выполнении функции сведения о трассировке записываются в журналы. Чтобы просмотреть выходные данные трассировки, вернитесь на страницу **код + тест** на портале и разверните стрелку **журналы** в нижней части страницы.

   ![Средство просмотра журналов Функций на портале Azure.](./media/functions-create-student-starter/function-view-logs.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="supported-features-in-azure-for-students-starter"></a>Поддерживаемые функции в службе "Начальный набор Microsoft Azure для учащихся"

В Azure для учащихся Starter вы можете получить доступ к большинству функций среды выполнения функций Azure, используя несколько ключевых ограничений, перечисленных ниже.

* Триггер HTTP является единственным поддерживаемым типом триггера.
    * Поддерживаются все входные и выходные привязки! [Полный список см. здесь.](functions-triggers-bindings.md)
* Поддерживаемые языки: 
    * C# (.NET Core 2);
    * JavaScript (Node.js 8 и 10)
    * F# (.NET Core 2).
    * [Поддерживаемые языки в планах высшего уровня см. здесь](supported-languages.md)
* Windows — единственная поддерживаемая операционная система.
* Масштаб будет ограничен [до одного бесплатного экземпляра уровня](https://azure.microsoft.com/pricing/details/app-service/windows/), выполняющегося ежедневно на протяжении не больше 60 минут. Вы серверлессли масштабирование от 0 до 1 экземпляра автоматически по мере получения HTTP-трафика, но не дальше.
* Поддерживается среда выполнения функций только [версии 2.x и выше](functions-versions.md).
* Для редактирования и публикации функций поддерживаются все средства разработки. В их состав входят: VS Code, Visual Studio, Azure CLI и портал Azure. Если вы хотите использовать любые компоненты, отличные от портала, необходимо сначала создать приложение на портале, а затем выбрать это приложение в качестве целевого объекта развертывания в предпочтительном средстве.

## <a name="next-steps"></a>Дальнейшие действия

Теперь вы завершили создание приложения-функции с помощью простой функции триггера HTTP. Далее вы можете исследовать локальные средства, другие языки, мониторинг и интеграции.

 * [Создание первой функции с помощью Visual Studio](./functions-create-your-first-function-visual-studio.md)
 * [Создание функции в Azure с помощью Visual Studio Code](./functions-create-first-function-vs-code.md)
 * [Руководство разработчика JavaScript для Функций Azure](./functions-reference-node.md)
 * [Подключение к базе данных SQL Azure с помощью Функций Azure](./functions-scenario-database-table-cleanup.md)
 * [Дополнительные сведения о привязках HTTP в Функциях Azure см. здесь](./functions-bindings-http-webhook.md).
 * [Мониторинг Функций Azure](./functions-monitoring.md)
