---
title: Создание ресурса текстовых аналитики когнитивных служб
titleSuffix: Azure Cognitive Services
description: Узнайте, как создать ресурс Анализа текста Cognitive Services.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: d3b65174f3d161e0b8780a890f297d9d1c812b44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "74383436"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>Создание ресурса текстовых аналитики когнитивных служб

1. Войдите на [портал Azure](https://portal.azure.com).
1. Выберите **Создать ресурс,** а затем перейти к **ИИ и машинного обучения** > **Текст Analytics**.
   Или перейдите к [созданию текстовой аналитики](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics).
1. Введите все необходимые настройки:

    |Параметр|Значение|
    |--|--|
    |name|Введите имя (2-64 символа).|
    |Подписка|Выберите соответствующую подписку.|
    |Расположение|Выберите место, расположенное поблизости.|
    |Ценовая категория| Введите **S**, стандартный уровень ценообразования.|
    |Группа ресурсов|Выберите доступную группу ресурсов.|

1. Выберите **Создать**и ждать создания ресурса. Браузер автоматически перенаправляет сят в новь созданной странице ресурса.
1. Соберите настроенный `endpoint` ключ ИП:

    |Вкладка ресурса на портале|Параметр|Значение|
    |--|--|--|
    |**Обзор**|Конечная точка|Копируйте конечную точку. Похоже, похож `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0`на .|
    |**Ключи**|Ключ API|Копируйте один из двух ключей. Это 32-символ акэтомра строка без каких-либо пробелов или тире: <`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`>.|
