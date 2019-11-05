---
title: Поддержка контейнеров
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/07/2019
ms.author: dapine
ms.openlocfilehash: 2cb2cfbdfbac5d496f109d85977b41a050766ab0
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499107"
---
## <a name="create-an-computer-vision-resource"></a>Создание ресурса Компьютерное зрение

1. Войдите на [портал Azure](https://portal.azure.com).
1. Щелкните [создать **компьютерное зрение** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision) ресурс.
1. Введите все необходимые параметры:

    |Настройка|Значение|
    |--|--|
    |Имя|Требуемое имя (2-64 символов)|
    |Подписка|Выберите подходящую подписку|
    |Расположение|Выберите все расположенные поблизости и доступные расположения|
    |Уровень ценообразования|`F0` — минимальная ценовая категория|
    |Группа ресурсов|Выберите доступную группу ресурсов|

1. Щелкните **создать** и дождитесь создания ресурса. После создания перейдите на страницу ресурсов.
1. Сбор настроенных `{ENDPOINT_URI}` и `{API_KEY}`см. в разделе [сбор необходимых параметров](../computer-vision-how-to-install-containers.md#gathering-required-parameters) для получения дополнительных сведений.
