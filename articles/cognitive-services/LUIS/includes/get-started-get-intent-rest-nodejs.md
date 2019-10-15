---
title: Получение сведений о намерении с помощью вызова REST и Node.js
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: a6dfe21cd92c5bf5580d7b121f33f68fb4e135fa
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/03/2019
ms.locfileid: "71838547"
---
## <a name="prerequisites"></a>Предварительные требования

* Язык программирования [Node.js](https://nodejs.org/). 
* [Visual Studio Code](https://code.visualstudio.com/)
* идентификатор общедоступного приложения: df67dcdb-c37d-46af-88e1-8b97951ca1c2.


> [!NOTE] 
> Полное решение Node.js доступно в разделе [**Azure-Samples** репозитория GitHub](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/analyze-text/node).

## <a name="get-luis-key"></a>Получение ключа LUIS

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-programmatically"></a>Получение намерения программным способом

С помощью Node.js можно получить доступ к тем же результатам, которые вы уже видели в окне браузера на предыдущем шаге.

1. Скопируйте следующий фрагмент кода.

   [!code-nodejs[Console app code that calls a LUIS endpoint for Node.js](~/samples-luis/documentation-samples/quickstarts/analyze-text/node/call-endpoint.js)]

2. Создайте файл `.env` со следующим текстом или задайте эти переменные в системной среде:

    ```CMD
    LUIS_APP_ID=df67dcdb-c37d-46af-88e1-8b97951ca1c2
    LUIS_ENDPOINT_KEY=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
    ```

3. Укажите свой ключ для переменной среды `LUIS_ENDPOINT_KEY`.

4. Установите зависимости, выполнив следующую команду в командной строке: `npm install`.

5. Выполните код с помощью команды `npm start`. Отобразятся те же значения, которые вы видели ранее в окне браузера.


## <a name="luis-keys"></a>Ключи LUIS

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Очистка ресурсов

По окончании работы с этим кратким руководством закройте проект Visual Studio и удалите каталог проекта из файловой системы. 

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Добавление высказываний и обучение на Node.js](../luis-get-started-node-add-utterance.md)