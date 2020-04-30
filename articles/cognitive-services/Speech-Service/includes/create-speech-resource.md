---
title: Поддержка контейнеров
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/5/2019
ms.author: trbye
ms.openlocfilehash: 8abd520a7bb80f1f9a2d8ebcbe46b90fe59a96ec
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "81422325"
---
## <a name="create-a-speech-resource"></a>Создание речевого ресурса

1. Войдите на [портал Azure](https://portal.azure.com).
1. Щелкните [создать **речевой** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices) ресурс
1. Введите все необходимые параметры:

    |Параметр|Значение|
    |--|--|
    |Имя|Требуемое имя (от 2 до 64 символов)|
    |Подписка|Выберите соответствующую подписку|
    |Расположение|Выберите доступное поблизости расположение|
    |Ценовая категория|`F0` — минимальная ценовая категория|
    |Группа ресурсов|Выберите доступную группу ресурсов|

1. Щелкните **Создать** и дождитесь создания ресурса. После создания перейдите на страницу ресурсов.
1. Собираются `endpoint` настройки и ключ API:

    |Вкладка ресурсов на портале|Параметр|Применение|
    |--|--|--|
    |**Обзор**|Конечная точка|Скопируйте конечную точку. Он выглядит примерно так.`https://speech.cognitiveservices.azure.com/sts/v1.0/issuetoken`|
    |**Ключи**|Ключ API|Скопируйте 1 из двух ключей. Это строка, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`состоящая из 32 буквенно-цифровых символов без пробелов или дефисов.|
