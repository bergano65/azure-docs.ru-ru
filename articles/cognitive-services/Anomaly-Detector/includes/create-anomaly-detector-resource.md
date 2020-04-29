---
title: Поддержка контейнеров
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 8dbe36693e551bd03022f4a00044b75b912c834a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "80875141"
---
## <a name="create-an-anomaly-detector-resource"></a>Создание ресурса "Детектор аномалий"

1. Войдите на [портал Azure](https://portal.azure.com).
1. Щелкните [создать ресурс **детектора аномалий** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector)
1. Введите все необходимые параметры:

    |Параметр|Значение|
    |--|--|
    |Имя|Требуемое имя (от 2 до 64 символов)|
    |Подписка|Выберите соответствующую подписку|
    |Расположение|Выберите доступное поблизости расположение|
    |Ценовая категория|`F0` — минимальная ценовая категория|
    |Группа ресурсов|Выберите доступную группу ресурсов|
    |Флажок подтверждения предварительного просмотра (обязательно)|Прочитано ли уведомление **предварительной версии**|

1. Щелкните **Создать** и дождитесь создания ресурса. После создания перейдите на страницу ресурсов.
1. Собираются `endpoint` настройки и ключ API:

    |Вкладка ресурсов на портале|Параметр|Применение|
    |--|--|--|
    |**Обзор**|Конечная точка|Скопируйте конечную точку. Он выглядит примерно так.`https://westus2.api.cognitive.microsoft.com/`|
    |**Ключи**|Ключ API|Скопируйте 1 из двух ключей. Это строка, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`состоящая из 32 буквенно-цифровых символов без пробелов или дефисов.|



