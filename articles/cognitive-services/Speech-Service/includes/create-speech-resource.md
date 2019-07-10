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
ms.openlocfilehash: 3c42bf2b2acc2472741bd603ea9d653a314ecc40
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711827"
---
## <a name="create-a-speech-resource"></a>Создать ресурс речи

1. Войдите на [портал Azure](https://portal.azure.com).
1. Нажмите кнопку [создать **речи** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices) ресурсов
1. Введите все необходимые параметры:

    |Параметр|Значение|
    |--|--|
    |ИМЯ|Желаемое имя (2-64 символов)|
    |Подписка|Выберите соответствующую подписку|
    |Location|Выберите любое расположение рядом и доступность|
    |Ценовая категория|`F0` -Минимальный ценовой категории|
    |Группа ресурсов|Выберите группу доступных ресурсов|

1. Нажмите кнопку **создать** и ожидания ресурсов должен быть создан. После его создания, перейдите на страницу ресурсов
1. Сбор настроен `endpoint` и ключ API:

    |Вкладка ресурсов на портале|Параметр|Значение|
    |--|--|--|
    |**Обзор набора средств Visual Studio для Unity**|Конечная точка|Копирование конечной точки. Выглядит `https://speech.cognitiveservices.azure.com/sts/v1.0/issuetoken`|
    |**Ключи**|Ключ API|Скопируйте 1 из двух ключей. Это строка 32 буквенно-цифровых символов без пробелов и дефисов, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
