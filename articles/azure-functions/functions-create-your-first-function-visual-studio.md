---
title: Краткое руководство. Создание первой функции в Azure с помощью Visual Studio
description: Из этого краткого руководства вы узнаете, как создать и опубликовать Функцию Azure, активируемую HTTP-запросом, с помощью Visual Studio.
ms.assetid: 82db1177-2295-4e39-bd42-763f6082e796
ms.topic: quickstart
ms.date: 03/06/2020
ms.custom: mvc, devcenter, vs-azure, 23113853-34f2-4f
ms.openlocfilehash: 13a03ca6820229fc90467236e0c23f236664d73f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/26/2020
ms.locfileid: "80056688"
---
# <a name="quickstart-create-your-first-function-in-azure-using-visual-studio"></a>Краткое руководство. Создание первой функции в Azure с помощью Visual Studio

Решение "Функции Azure" позволяет выполнять код в бессерверной среде без необходимости создавать виртуальную машину или публиковать веб-приложение.

Изучив это краткое руководство, вы научитесь с помощью Visual Studio 2019 локально создавать и тестировать приложение-функцию C# hello world, активируемое HTTP-запросом, а затем публиковать его в Azure. 

![Ответ функции localhost в браузере](./media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-local-final.png)

Это краткое руководство предназначено для Visual Studio 2019. 

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством сначала установите [Visual Studio 2019](https://azure.microsoft.com/downloads/). Во время установки обязательно выберите рабочую нагрузку **Разработка для Azure**. Если вы хотите создать проект "Функции Azure" с помощью Visual Studio 2017, необходимо сначала установить [новейшие инструменты Функций Azure](functions-develop-vs.md#check-your-tools-version).

![Установка Visual Studio с рабочей нагрузкой разработки в Azure](media/functions-create-your-first-function-visual-studio/functions-vs-workloads.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-function-app-project"></a>Создание проекта приложения-функции

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

Visual Studio создает проект и класс, содержащий стандартный код для типа функции триггера HTTP. Атрибут `FunctionName` метода задает имя функции. По умолчанию — `Function1`. Атрибут `HttpTrigger` указывает, что функция вызывается HTTP-запросом. Стандартный код отправляет HTTP-ответ, включающий в себя значение из текста или строки запроса.

Расширьте возможности функции с помощью входных и выходных привязок данных, применив для метода соответствующие атрибуты. Дополнительные сведения см. в разделе [Триггеры и привязки](functions-dotnet-class-library.md#triggers-and-bindings) в статье [Справочник разработчика C# по функциям Azure](functions-dotnet-class-library.md).

Созданный проект функции и функцию, активируемую HTTP, можно протестировать на локальном компьютере.

## <a name="run-the-function-locally"></a>Локальное выполнение функции

Visual Studio интегрируется с Azure Functions Core Tools. Поэтому вы можете тестировать функции локально, используя полную среду выполнения Функций Azure.  

[!INCLUDE [functions-run-function-test-local-vs](../../includes/functions-run-function-test-local-vs.md)]

Убедившись, что функция выполняется правильно на локальном компьютере, опубликуйте проект в Azure.

## <a name="publish-the-project-to-azure"></a>Публикация проекта в Azure

Перед публикацией проекта убедитесь, что в вашей подписке Azure есть приложение-функция. Для первой публикации проекта в Visual Studio создается приложение-функция.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="test-your-function-in-azure"></a>Тестирование функции в Azure

1. Скопируйте базовый URL-адрес приложения-функции на странице профиля **Публикация**. Замените часть `localhost:port` URL-адреса, который использовался при локальном тестировании функции, новым базовым URL-адресом. Добавьте строку запроса `?name=<YOUR_NAME>` в этот URL-адрес и выполните запрос.

    URL-адрес для вызова функции, активируемой HTTP-запросом, указывается в следующем формате:

    `http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?name=<YOUR_NAME>`

2. Вставьте этот URL-адрес HTTP-запроса в адресную строку браузера. На рисунке ниже показан ответ в браузере на удаленный запрос GET, возвращаемый функцией:

    ![Ответ функции в браузере](./media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-azure.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Дальнейшие действия

В рамках этого краткого руководства вы с помощью Visual Studio создали и опубликовали в Azure приложение-функцию C# с простой функцией, активируемой HTTP-запросом. 

Перейдите к следующей статье, чтобы узнать, как добавить привязку очереди службы хранилища Azure к этой функции:
> [!div class="nextstepaction"]
> [Connect functions to Azure Storage using Visual Studio Code](functions-add-output-binding-storage-queue-vs.md) (Подключение функций к службе хранилища Azure с помощью Visual Studio Code)

