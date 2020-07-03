---
title: Поддержка контейнеров
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/5/2019
ms.author: aahi
ms.openlocfilehash: 0a330c2401412e1e3d5e2e49bf8121a681342808
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "80878377"
---
## <a name="create-an-face-resource"></a>Создание ресурса лицом

1. Войдите на [портал Azure](https://portal.azure.com).
1. Щелкните [создать ресурс **лица** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFace) .
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
    |**Обзор**|Конечная точка|Скопируйте конечную точку. Он выглядит примерно так.`https://face.cognitiveservices.azure.com/face/v1.0`|
    |**Ключи**|Ключ API|Скопируйте 1 из двух ключей. Это строка, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`состоящая из 32 буквенно-цифровых символов без пробелов или дефисов.|
