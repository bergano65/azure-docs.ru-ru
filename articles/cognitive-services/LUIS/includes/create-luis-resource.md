---
title: Поддержка контейнеров
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/23/2019
ms.author: dapine
ms.openlocfilehash: ecb172aec37fdab21bed8373ac7484dc52a54829
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72822029"
---
## <a name="create-a-luis-resource"></a>Создание ресурса LUIS

1. Войдите на [портал Azure](https://portal.azure.com).
1. Нажмите кнопку [создать **Language Understanding** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
1. Введите все необходимые параметры:

    |Параметр|Value|
    |--|--|
    |Name|Требуемое имя (2-64 символов)|
    |Subscription|Выберите подходящую подписку|
    |Location|Выберите все расположенные поблизости и доступные расположения|
    |Уровень цен|`F0` — минимальная ценовая категория|
    |Группа ресурсов|Выберите доступную группу ресурсов|

1. Щелкните **создать** и дождитесь создания ресурса. После создания перейдите на страницу ресурсов.
1. Собирайте настроенные `endpoint` и ключ API:

    |Вкладка ресурсов на портале|Параметр|Value|
    |--|--|--|
    |**Обзор**|Конечная точка|Скопируйте конечную точку. Он выглядит так же, как `https://luis.cognitiveservices.azure.com/luis/v2.0`|
    |**Ключи**|Ключ API|Скопируйте 1 из двух ключей. Это строка, состоящая из 32 буквенно-цифровых символов без пробелов или дефисов `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
