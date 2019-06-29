---
title: Поддержка контейнеров
titleSuffix: Azure Cognitive Services
description: Узнайте, как создать ресурс cognitive services text analytics.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 022ffcd806d4d4f89f8a8cf256a541518ea12602
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67455107"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>Создать ресурс текстовая аналитика Cognitive Services

1. Войдите на [портал Azure](https://portal.azure.com).
1. Выберите **+ создать ресурс**, перейдите к **искусственный Интеллект и машинное обучение > текстовая аналитика**, или нажмите кнопку [создать **анализа текста**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)
1. Введите все необходимые параметры:

    |Параметр|Значение|
    |--|--|
    |ИМЯ|Желаемое имя (2-64 символов)|
    |Подписка|Выберите соответствующую подписку|
    |Location|Выберите ближайшее расположение|
    |Ценовая категория|`S` -уровень ценообразования standard|
    |Группа ресурсов|Выберите группу доступных ресурсов|

1. Нажмите кнопку **создать** и ожидания ресурсов должен быть создан. После его создания, браузер автоматически перенаправляет на страницу нового созданного ресурса
1. Сбор настроен `endpoint` и ключ API:

    |Вкладка ресурсов на портале|Параметр|Значение|
    |--|--|--|
    |**Обзор набора средств Visual Studio для Unity**|Конечная точка|Копирование конечной точки. Выглядит `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0`|
    |**Ключи**|Ключ API|Скопируйте 1 из двух ключей. Это строка 32 буквенно-цифровых символов без пробелов и дефисов, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|