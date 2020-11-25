---
title: Создание ресурса анализа текста Cognitive Services
titleSuffix: Azure Cognitive Services
description: Узнайте, как создать ресурс Анализа текста Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: daafea59b96cc8da6b78f0733c9f54e0e4d3a8c2
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2020
ms.locfileid: "96009941"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>Создание ресурса анализа текста Cognitive Services

1. Войдите на [портал Azure](https://portal.azure.com).
1. Выберите **Создать ресурс**, а затем последовательно выберите **Искусственный интеллект и машинное обучение** > **Анализ текста**.
   Или перейдите к разделу [создание анализ текста](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics).
1. Введите все необходимые параметры:

    |Параметр|Значение|
    |--|--|
    |Имя|Введите имя (2–64 символа).|
    |Подписка|Выберите соответствующую подписку.|
    |Расположение|Выберите ближайшее расположение.|
    |Ценовая категория| Введите **S** (ценовая категория "Стандартный").|
    |Группа ресурсов|Выберите доступную группу ресурсов.|

1. Нажмите **Создать** и подождите, пока ресурс будет создан. Браузер автоматически перенаправит на созданную страницу ресурсов.
1. Собирайте настроенный `endpoint` и ключ API:

    |Вкладка ресурсов на портале|Параметр|Значение|
    |--|--|--|
    |**Обзор**|Конечная точка|Скопируйте конечную точку. Он выглядит примерно так `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0` :.|
    |**Ключи**|Ключ API|Скопируйте один из двух ключей. Это буквенно-цифровая строка, состоящая из 32 символов без пробелов или дефисов: <`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`>.|
