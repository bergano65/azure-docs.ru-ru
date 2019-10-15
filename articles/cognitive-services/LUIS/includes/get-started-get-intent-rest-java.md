---
title: Получение сведений о намерении с помощью вызова REST и Java
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 7199dfaaa476e4be27929010b76a6e0544857bdb
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/03/2019
ms.locfileid: "71838530"
---
## <a name="prerequisites"></a>Предварительные требования

* [JDK SE](https://aka.ms/azure-jdks) (комплект разработчика Java, выпуск "Стандартный");
* [Visual Studio Code](https://code.visualstudio.com/) или привычный вам редактор кода;
* идентификатор общедоступного приложения: df67dcdb-c37d-46af-88e1-8b97951ca1c2.

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>Получение ключа LUIS

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-programmatically"></a>Получение намерения программным способом

С помощью Java можно получить доступ к тем же результатам, которые вы уже видели в окне браузера на предыдущем шаге. Обязательно добавьте в проект библиотеки Apache.

1. Скопируйте следующий код для создания класса в файл с именем `LuisGetRequest.java`:

   [!code-java[Console app code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/java/call-endpoint.java)]

2. Замените значение переменной `YOUR-KEY` ключом LUIS.

3. Замените путь к файлу и скомпилируйте программу Java из командной строки: `javac -cp .;<FILE_PATH>\* LuisGetRequest.java`.

4. Замените путь к файлу и запустите приложение из командной строки: `java -cp .;<FILE_PATH>\* LuisGetRequest.java`. Отобразится тот же результат JSON, который вы видели ранее в окне браузера.

    ![Окно консоли, в котором отображается результат JSON из LUIS](../media/luis-get-started-java-get-intent/console-turn-on.png)
    


## <a name="luis-keys"></a>Ключи LUIS

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Очистка ресурсов

По окончании работы с этим кратким руководством закройте проект Visual Studio и удалите каталог проекта из файловой системы. 

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Добавление высказываний и обучение на Java](../luis-get-started-java-add-utterance.md)