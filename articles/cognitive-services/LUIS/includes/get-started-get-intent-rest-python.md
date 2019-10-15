---
title: Получение сведений о намерении с помощью вызова REST и Python
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: e86d1e16e7c61f851a75ad97d2744b0daa009617
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/03/2019
ms.locfileid: "71838504"
---
## <a name="prerequisites"></a>Предварительные требования

* [Python 3.6](https://www.python.org/downloads/) или более поздней версии.
* [Visual Studio Code](https://code.visualstudio.com/)

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>Получение ключа LUIS

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent--programmatically"></a>Получение намерения программным способом

С помощью Python можно получить доступ к тем же результатам, которые вы уже видели в окне браузера на предыдущем шаге.

1. Скопируйте приведенный ниже фрагмент кода в файл с именем `quickstart-call-endpoint.py`.

    #### <a name="python-27tabp2"></a>[Python 2.7](#tab/P2).

    [!code-python[Console app code that calls a LUIS endpoint for Python 2.7](~/samples-luis/documentation-samples/quickstarts/analyze-text/python/2.x/quickstart-call-endpoint-2-7.py)]    

    #### <a name="python-36tabp3"></a>[Python 3.6](#tab/P3);

    [!code-python[Console app code that calls a LUIS endpoint for Python 3.6](~/samples-luis/documentation-samples/quickstarts/analyze-text/python/3.x/quickstart-call-endpoint-3-6.py)]

    * * *

1. Замените значение поля `Ocp-Apim-Subscription-Key` своим ключом к конечной точке LUIS.

1. Установите зависимости с помощью команды `pip install requests`.

1. Запустите скрипт с помощью команды `python ./quickstart-call-endpoint.py`. Отобразится тот же результат JSON, который вы видели ранее в окне браузера.

## <a name="luis-keys"></a>Ключи LUIS

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Очистка ресурсов

По окончании работы с этим кратким руководством закройте проект Visual Studio и удалите каталог проекта из файловой системы. 

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Добавление высказываний и обучение на Python](../luis-get-started-python-add-utterance.md)