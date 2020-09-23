---
title: Поддержка контейнеров
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/10/2020
ms.author: aahi
ms.openlocfilehash: 8f93e0ffe74ade79059fc845788faf36ab5b4f13
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/22/2020
ms.locfileid: "91025215"
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

1. Щелкните **Создать** и дождитесь создания ресурса. После создания перейдите на страницу ресурсов.
1. Собираются настройки `endpoint` и ключ API:

    |Вкладка ресурсов на портале|Параметр|Значение|
    |--|--|--|
    |**Обзор**|Конечная точка|Скопируйте конечную точку. Он выглядит примерно так. `https://westus2.api.cognitive.microsoft.com/`|
    |**Ключи**|Ключ API|Скопируйте 1 из двух ключей. Это строка, состоящая из 32 буквенно-цифровых символов без пробелов или дефисов `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` .|



