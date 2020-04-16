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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422325"
---
## <a name="create-a-speech-resource"></a>Создание ресурса речи

1. Войти на [портал Azure](https://portal.azure.com)
1. Нажмите Создать ресурс [ **речи** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices)
1. Введите все необходимые параметры:

    |Параметр|Значение|
    |--|--|
    |Имя|Требуемое имя (от 2 до 64 символов)|
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
