---
title: Получение намерения с помощью Java — LUIS
titleSuffix: Azure Cognitive Services
description: Из этого краткого руководства по Java вы узнаете, как использовать общедоступное приложение LUIS для определения намерений пользователя в разговоре.
author: diberry
manager: nitinme
services: cognitive-services
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 07/16/2019
ms.author: diberry
ms.openlocfilehash: 7a80aefd7a88727cf3a2261115c076853bd45b3f
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563820"
---
# <a name="quickstart-get-intent-using-java"></a>Краткое руководство. Получение намерения с использованием Java

Из этой статьи вы узнаете, как передать фразы в конечную точку LUIS и получить намерение и сущности.

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

<a name="create-luis-subscription-key"></a>

## <a name="prerequisites"></a>Предварительные требования

* [JDK SE](https://aka.ms/azure-jdks) (комплект разработчика Java, выпуск "Стандартный");
* [Visual Studio Code](https://code.visualstudio.com/) или привычный вам редактор кода;
* идентификатор общедоступного приложения: df67dcdb-c37d-46af-88e1-8b97951ca1c2.

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>Получение ключа LUIS

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-with-browser"></a>Получение намерения с помощью браузера

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="get-intent-programmatically"></a>Получение намерения программным способом

С помощью Java можно получить доступ к тем же результатам, которые вы уже видели в окне браузера на предыдущем шаге. Обязательно добавьте в проект библиотеки Apache.

1. Скопируйте следующий код для создания класса в файл с именем `LuisGetRequest.java`:

   [!code-java[Console app code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/java/call-endpoint.java)]

2. Замените значение переменной `YOUR-KEY` ключом LUIS.

3. Замените путь к файлу и скомпилируйте программу Java из командной строки: `javac -cp .;<FILE_PATH>\* LuisGetRequest.java`.

4. Замените путь к файлу и запустите приложение из командной строки: `java -cp .;<FILE_PATH>\* LuisGetRequest.java`. Отобразится тот же результат JSON, который вы видели ранее в окне браузера.

    ![Окно консоли, в котором отображается результат JSON из LUIS](./media/luis-get-started-java-get-intent/console-turn-on.png)
    
## <a name="luis-keys"></a>Ключи LUIS

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Очистка ресурсов

Удалите файл Java или папку проекта.

## <a name="next-steps"></a>Дополнительная информация
> [!div class="nextstepaction"]
> [Добавление высказываний](luis-get-started-java-add-utterance.md)
