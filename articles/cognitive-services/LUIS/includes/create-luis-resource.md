---
title: Создание ресурса LUIS
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/23/2019
ms.author: dapine
ms.openlocfilehash: a765ac27936da9da5a2f41464c17491e3561f44b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73465931"
---
## <a name="create-a-luis-resource"></a>Создание ресурса LUIS

1. Войдите на [портал Azure](https://portal.azure.com).
1. Нажмите кнопку [создать **Language Understanding** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
1. Введите все необходимые параметры:

    |Настройка|Значение|
    |--|--|
    |Имя|Требуемое имя (2-64 символов)|
    |Подписка|Выберите подходящую подписку|
    |Расположение|Выберите все расположенные поблизости и доступные расположения|
    |Уровень ценообразования|`F0` — минимальная ценовая категория|
    |Группа ресурсов|Выберите доступную группу ресурсов|

1. Щелкните **создать** и дождитесь создания ресурса. После создания перейдите на страницу ресурсов.
1. Сбор настроенных `endpoint` и ключа API см. в разделе [сбор обязательных параметров](#gathering-required-parameters).

[!INCLUDE [Gathering required parameters](../../containers/includes/container-gathering-required-parameters.md)]
