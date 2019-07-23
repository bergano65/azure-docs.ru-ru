---
title: Создание ресурса Анализ текста Cognitive Services
titleSuffix: Azure Cognitive Services
description: Узнайте, как создать ресурс Анализ текста Cognitive Services.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 6a9363472e1d5770232ca9a0b151fb309d9c4c75
ms.sourcegitcommit: b49431b29a53efaa5b82f9be0f8a714f668c38ab
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/22/2019
ms.locfileid: "68377419"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>Создание ресурса Анализ текста Cognitive Services

1. Войдите на [портале Azure](https://portal.azure.com).
1. Выберите **создать ресурс**, а затем перейдите в**анализ текста** **AI + машинное обучение** > .
   Или перейдите к разделу [создание анализ текста](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics).
1. Введите все необходимые параметры:

    |Параметр|Значение|
    |--|--|
    |Имя|Введите имя (2-64 символов).|
    |Подписка|Выберите подходящую подписку.|
    |Местоположение|Выберите ближайшее расположение.|
    |Ценовая категория| Введите **S**, ценовую категорию "Стандартный".|
    |Группа ресурсов|Выберите доступную группу ресурсов.|

1. Выберите **создать**и дождитесь создания ресурса. Браузер автоматически перенаправит на созданную страницу ресурсов.
1. Собирайте настроенный `endpoint` и ключ API:

    |Вкладка ресурсов на портале|Параметр|Значение|
    |--|--|--|
    |**Обзор**|Конечная точка|Скопируйте конечную точку. Он выглядит примерно так `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0`:.|
    |**Ключи**|Ключ API|Скопируйте один из двух ключей. Это буквенно-цифровая строка, состоящая из 32 символов без пробелов`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`или дефисов: < >.|
