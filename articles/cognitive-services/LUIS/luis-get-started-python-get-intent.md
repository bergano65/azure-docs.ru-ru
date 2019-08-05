---
title: Получение намерения с помощью Python — LUIS
titleSuffix: Azure Cognitive Services
description: Из этой статьи вы узнаете, как передать фразы в конечную точку LUIS и получить намерение и сущности.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 07/16/2019
ms.author: diberry
ms.openlocfilehash: defcb6713516952e56e6cca2d63a99ee61f5b868
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563788"
---
# <a name="quickstart-get-intent-using-python"></a>Краткое руководство. Получение намерения с использованием Python
Из этой статьи вы узнаете, как передать фразы в конечную точку LUIS и получить намерение и сущности.

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

## <a name="prerequisites"></a>Предварительные требования

* [Python 3.6](https://www.python.org/downloads/) или более поздней версии.
* [Visual Studio Code](https://code.visualstudio.com/)

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>Получение ключа LUIS

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-with-browser"></a>Получение намерения с помощью браузера

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="get-intent--programmatically"></a>Получение намерения программным способом

С помощью Python можно получить доступ к тем же результатам, которые вы уже видели в окне браузера на предыдущем шаге.

1. Скопируйте приведенный ниже фрагмент кода в файл с именем `quickstart-call-endpoint.py`.

   [!code-python[Console app code that calls a LUIS endpoint for Python 2.7](~/samples-luis/documentation-samples/quickstarts/analyze-text/python/2.x/quickstart-call-endpoint-2-7.py)]

   [!code-python[Console app code that calls a LUIS endpoint for Python 3.6](~/samples-luis/documentation-samples/quickstarts/analyze-text/python/3.x/quickstart-call-endpoint-3-6.py)]

2. Замените значение поля `Ocp-Apim-Subscription-Key` своим ключом к конечной точке LUIS.

3. Установите зависимости с помощью команды `pip install requests`.

4. Запустите скрипт с помощью команды `python ./quickstart-call-endpoint.py`. Отобразится тот же результат JSON, который вы видели ранее в окне браузера.

## <a name="luis-keys"></a>Ключи LUIS

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Очистка ресурсов
Удалите файл Python. 

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Добавление высказываний](luis-get-started-python-add-utterance.md)
