---
title: Получение сведений о намерении с помощью вызова REST и C#
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: e6ae9590cee3a2ddc3b8e121161fcf84815da28a
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/03/2019
ms.locfileid: "71838565"
---
## <a name="prerequisites"></a>Предварительные требования

* [выпуск Visual Studio Community 2017](https://visualstudio.microsoft.com/vs/community/);
* язык программирования C# (входит в состав выпуска Visual Studio Community 2017);
* идентификатор общедоступного приложения: df67dcdb-c37d-46af-88e1-8b97951ca1c2.


[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>Получение ключа LUIS

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-programmatically"></a>Получение намерения программным способом

С помощью C# выполните запрос к конечной точке прогнозирования [API](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78) GET, чтобы получить такие же результаты, как при использовании браузера в предыдущем разделе. 

1. Создайте в Visual Studio консольное приложение. 

    ![Создайте в Visual Studio консольное приложение.](../media/luis-get-started-cs-get-intent/visual-studio-console-app.png)

2. В обозревателе решений проекта Visual Studio выберите **Добавить ссылку**, затем на вкладке "Сборки" выберите **System.Web**.

    ![Щелкните "Добавить ссылку", а затем на вкладке "Сборки" выберите System.Web.](../media/luis-get-started-cs-get-intent/add-system-dot-web-to-project.png)

3. Перезапишите содержимое файла Program.cs следующим кодом:
    
   [!code-csharp[Console app code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/csharp/Program.cs)]

4. Замените значение `YOUR_KEY` ключом LUIS.

5. Скомпилируйте и запустите консольное приложение. Отобразится тот же результат JSON, который вы видели ранее в окне браузера.

    ![Окно консоли, в котором отображается результат JSON из LUIS](../media/luis-get-started-cs-get-intent/console-turn-on.png)



## <a name="luis-keys"></a>Ключи LUIS

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Очистка ресурсов

По окончании работы с этим кратким руководством закройте проект Visual Studio и удалите каталог проекта из файловой системы. 

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Добавление высказываний и обучение на C#](../luis-get-started-cs-add-utterance.md)