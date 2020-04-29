---
title: Создание ресурса Анализ текста Cognitive Services
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "80876458"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>Создание ресурса Анализ текста Cognitive Services

1. Войдите на [портал Azure](https://portal.azure.com).
1. Выберите **создать ресурс**, а затем перейдите в анализ текста **AI + машинное обучение** > **Text Analytics**.
   Или перейдите к разделу [создание анализ текста](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics).
1. Введите все необходимые параметры:

    |Параметр|Значение|
    |--|--|
    |Имя|Введите имя (2-64 символов).|
    |Подписка|Выберите соответствующую подписку.|
    |Расположение|Выберите ближайшее расположение.|
    |Ценовая категория| Введите **S**, ценовую категорию "Стандартный".|
    |Группа ресурсов|Выберите доступную группу ресурсов.|

1. Выберите **создать**и дождитесь создания ресурса. Браузер автоматически перенаправит на созданную страницу ресурсов.
1. Собирайте настроенный `endpoint` и ключ API:

    |Вкладка ресурсов на портале|Параметр|Применение|
    |--|--|--|
    |**Обзор**|Конечная точка|Скопируйте конечную точку. Он выглядит примерно так `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0`:.|
    |**Ключи**|Ключ API|Скопируйте один из двух ключей. Это буквенно-цифровая строка, состоящая из 32 символов без пробелов `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` или дефисов: <>.|
