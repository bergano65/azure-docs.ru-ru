---
title: Создание первой функции в Azure с помощью Visual Studio
description: Создание и публикация активируемой HTTP функции Azure с помощью Visual Studio.
author: ggailey777
manager: gwallace
keywords: функции azure, функции, обработка событий, вычисления, независимая архитектура
ms.assetid: 82db1177-2295-4e39-bd42-763f6082e796
ms.service: azure-functions
ms.topic: quickstart
ms.date: 07/19/2019
ms.author: glenga
ms.custom: mvc, devcenter, vs-azure, 23113853-34f2-4f
ms.openlocfilehash: 1d798be3a0cde39de208105c4b3d4458fa5b124c
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329491"
---
# <a name="create-your-first-function-using-visual-studio"></a>Создание первой функции с помощью Visual Studio

Решение "Функции Azure" позволяет выполнять код в [бессерверной](https://azure.microsoft.com/solutions/serverless/) среде без необходимости создавать виртуальную машину или публиковать веб-приложение.

Изучив эту статью, вы научитесь с помощью Visual Studio 2019 локально создавать и тестировать функцию hello world, а затем публиковать ее в Azure. Это краткое руководство предназначено для Visual Studio 2019. При создании проекта "Функции Azure" с помощью Visual Studio 2017 необходимо сначала установить [новейшие инструменты Функций Azure](functions-develop-vs.md#check-your-tools-version).

![Ответ функции localhost в браузере](./media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-local-final.png)

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим учебником необходимо сначала установить [Visual Studio 2019](https://azure.microsoft.com/downloads/). Убедитесь, что рабочая нагрузка **Разработка для Azure** также установлена.

![Установка Visual Studio с рабочей нагрузкой разработки в Azure](media/functions-create-your-first-function-visual-studio/functions-vs-workloads.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-function-app-project"></a>Создание проекта приложения-функции

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

Visual Studio создает проект и класс, содержащий стандартный код для типа функции триггера HTTP. Атрибут `FunctionName` метода задает имя функции. По умолчанию — `HttpTrigger`. Атрибут `HttpTrigger` указывает, что функция вызывается HTTP-запросом. Стандартный код отправляет HTTP-ответ, включающий в себя значение из текста или строки запроса.

Вы можете расширить возможности функции, используя входные и выходные привязки данных, применив для метода соответствующие атрибуты. Дополнительные сведения см. в разделе [Триггеры и привязки](functions-dotnet-class-library.md#triggers-and-bindings) в статье [Справочник разработчика C# по функциям Azure](functions-dotnet-class-library.md).

Созданный проект функции и функцию, активируемую HTTP, можно протестировать на локальном компьютере.

## <a name="run-the-function-locally"></a>Локальное выполнение функции

Visual Studio интегрируется с Azure Functions Core Tools, поэтому вы можете тестировать функции локально, используя полную среду выполнения Функций.  

[!INCLUDE [functions-run-function-test-local-vs](../../includes/functions-run-function-test-local-vs.md)]

Убедитесь, что функция правильно работает на локальном компьютере. Затем опубликуйте проект в Azure.

## <a name="publish-the-project-to-azure"></a>Публикация проекта в Azure

Перед публикацией проекта убедитесь, что в вашей подписке Azure есть приложения-функция. Для первой публикации проекта в Visual Studio создается приложение-функция.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="test-your-function-in-azure"></a>Тестирование функции в Azure

1. Скопируйте базовый URL-адрес приложения-функции на странице профиля публикации. Замените часть `localhost:port` URL-адреса, который использовался при локальной проверке функции новым базовым URL-адресом. Как и ранее, добавьте строку запроса `?name=<YOUR_NAME>` в этот URL-адрес и выполните запрос.

    URL-адрес, вызывающий функцию, активирующую HTTP, должен быть в следующем формате:

        http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?name=<YOUR_NAME> 

2. Вставьте этот URL-адрес HTTP-запроса в адресную строку браузера. Ниже показан ответ в браузере на удаленный запрос GET, возвращаемый функцией:

    ![Ответ функции в браузере](./media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-azure.png)

## <a name="next-steps"></a>Дополнительная информация

С помощью Visual Studio вы создали и опубликовали в Azure приложение-функцию C# с простой функцией, активируемой HTTP. Дополнительные сведения о разработке функций, таких как библиотеки класса .NET, см. в статье [Справочник разработчика C# по функциям Azure](functions-dotnet-class-library.md).

> [!div class="nextstepaction"]
> [Connect functions to Azure Storage using Visual Studio Code](functions-add-output-binding-storage-queue-vs.md) (Подключение функций к службе хранилища Azure с помощью Visual Studio Code)
