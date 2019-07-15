---
title: Создать ресурс текстовая аналитика Cognitive Services
titleSuffix: Azure Cognitive Services
description: Узнайте, как создать ресурс текстовая аналитика Cognitive Services.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 7e8b4480911f00afa8524ef4b81d697bb8ee5bcc
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/15/2019
ms.locfileid: "67877455"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>Создать ресурс текстовая аналитика Cognitive Services

1. Войдите на [портале Azure](https://portal.azure.com).
1. Выберите **создать ресурс**, а затем перейдите к разделу **искусственный Интеллект и машинное Обучение** > **текстовая аналитика**.
   Или перейдите к [создать текстовая аналитика](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics).
1. Введите все необходимые параметры:

    |Параметр|Значение|
    |--|--|
    |ИМЯ|Введите имя (2-64 символов)|
    |Subscription|Выберите соответствующую подписку|
    |Местоположение|Выберите ближайшее расположение|
    |Ценовая категория| Введите **S**, уровень ценообразования standard|
    |Resource group|Выберите группу доступных ресурсов|

1. Выберите **создать** и ожидания ресурсов должен быть создан. Браузер автоматически перенаправляет на страницу нового созданного ресурса.
1. Собирать настроенного `endpoint` и ключ API:

    |Вкладка ресурсов на портале|Параметр|Значение|
    |--|--|--|
    |**Обзор**|Конечная точка|Копирование конечной точки. Он должен выглядеть аналогично `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0`|
    |**Ключи**|Ключ API|Скопируйте один из двух ключей. Это 32-значный алфавитно-цифровую строку без пробелов и дефисов: <`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`>.|
