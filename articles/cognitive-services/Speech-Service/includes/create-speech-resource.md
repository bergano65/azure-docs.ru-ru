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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "73523755"
---
## <a name="create-a-speech-resource"></a>Создание ресурса речи

1. Войти на [портал Azure](https://portal.azure.com)
1. Нажмите Создать ресурс [ **речи** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices)
1. Введите все необходимые настройки:

    |Параметр|Значение|
    |--|--|
    |name|Требуемое имя (от 2 до 64 символов)|
    |Подписка|Выберите соответствующую подписку|
    |Расположение|Выберите доступное поблизости расположение|
    |Ценовая категория|`F0` — минимальная ценовая категория|
    |Группа ресурсов|Выберите доступную группу ресурсов|

1. Щелкните **Создать** и дождитесь создания ресурса. После создания перейдите на страницу ресурса
1. Соберите `endpoint` настроенный и ключ API:

    |Вкладка ресурсов в портале|Параметр|Значение|
    |--|--|--|
    |**Обзор**|Конечная точка|Копируйте конечную точку. Это похоже на`https://speech.cognitiveservices.azure.com/sts/v1.0/issuetoken`|
    |**Ключи**|Ключ API|Копируй 1 из двух ключей. Это 32 алфавитного символа строки без каких-либо пробелов или тире, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
