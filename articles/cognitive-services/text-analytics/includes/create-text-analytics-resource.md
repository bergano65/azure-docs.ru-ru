---
title: Создание ресурса Анализ текста Cognitive Services
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
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383436"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>Создание ресурса Анализ текста Cognitive Services

1. Войдите на [портале Azure](https://portal.azure.com).
1. Выберите **создать ресурс**, а затем выберите **AI + машинное обучение** > **анализ текста**.
   Или перейдите к разделу [создание анализ текста](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics).
1. Введите все необходимые параметры:

    |Настройка|Значение|
    |--|--|
    |имя|Введите имя (2-64 символов).|
    |подписку|Выберите подходящую подписку.|
    |Место проведения|Выберите ближайшее расположение.|
    |Ценовая категория| Введите **S**, ценовую категорию "Стандартный".|
    |Группа ресурсов|Выберите доступную группу ресурсов.|

1. Выберите **создать**и дождитесь создания ресурса. Браузер автоматически перенаправит на созданную страницу ресурсов.
1. Собирайте настроенные `endpoint` и ключ API:

    |Вкладка ресурсов на портале|Настройка|Значение|
    |--|--|--|
    |**Обзор**|Конечная точка|Скопируйте конечную точку. Он выглядит так же, как `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0`.|
    |**Ключи**|Ключ API|Скопируйте один из двух ключей. Это строка, состоящая из 32 символов и не имеющая пробелов или дефисов: <`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`>.|
