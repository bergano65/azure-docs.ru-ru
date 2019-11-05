---
title: Поддержка контейнеров
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: 3886777b283af35e84683480a59097584b537fea
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73523759"
---
## <a name="create-an-face-resource"></a>Создание ресурса лицом

1. Войдите на [портал Azure](https://portal.azure.com).
1. Щелкните [создать ресурс **лица** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFace) .
1. Введите все необходимые параметры:

    |Настройка|Значение|
    |--|--|
    |Имя|Требуемое имя (2-64 символов)|
    |Подписка|Выберите подходящую подписку|
    |Расположение|Выберите все расположенные поблизости и доступные расположения|
    |Уровень ценообразования|`F0` — минимальная ценовая категория|
    |Группа ресурсов|Выберите доступную группу ресурсов|

1. Щелкните **создать** и дождитесь создания ресурса. После создания перейдите на страницу ресурсов.
1. Собирайте настроенные `endpoint` и ключ API:

    |Вкладка ресурсов на портале|Настройка|Значение|
    |--|--|--|
    |**Обзор**|Конечная точка|Скопируйте конечную точку. Он выглядит так же, как `https://face.cognitiveservices.azure.com/face/v1.0`|
    |**Ключи**|Ключ API|Скопируйте 1 из двух ключей. Это строка, состоящая из 32 буквенно-цифровых символов без пробелов или дефисов `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
