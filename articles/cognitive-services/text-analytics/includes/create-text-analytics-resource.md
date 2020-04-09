---
title: Создание ресурса текстовых аналитики когнитивных служб
titleSuffix: Azure Cognitive Services
description: Узнайте, как создать ресурс Анализа текста Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 6cd653909e26dc5e0484ca289a1d2ab47e20457f
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876458"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>Создание ресурса текстовых аналитики когнитивных служб

1. Войдите на [портал Azure](https://portal.azure.com).
1. Выберите **Создать ресурс,** а затем перейти к **ИИ и машинного обучения** > **Текст Analytics**.
   Или перейдите к [созданию текстовой аналитики](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics).
1. Введите все необходимые настройки:

    |Параметр|Значение|
    |--|--|
    |Имя|Введите имя (2-64 символа).|
    |Подписка|Выберите соответствующую подписку.|
    |Местоположение|Выберите место, расположенное поблизости.|
    |Ценовая категория| Введите **S**, стандартный уровень ценообразования.|
    |Группа ресурсов|Выберите доступную группу ресурсов.|

1. Выберите **Создать**и ждать создания ресурса. Браузер автоматически перенаправляет сят в новь созданной странице ресурса.
1. Соберите настроенный `endpoint` ключ ИП:

    |Вкладка ресурса на портале|Параметр|Значение|
    |--|--|--|
    |**Обзор**|Конечная точка|Копируйте конечную точку. Похоже, похож `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0`на .|
    |**Ключи**|Ключ API|Копируйте один из двух ключей. Это 32-символ акэтомра строка без каких-либо пробелов или тире: <`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`>.|
