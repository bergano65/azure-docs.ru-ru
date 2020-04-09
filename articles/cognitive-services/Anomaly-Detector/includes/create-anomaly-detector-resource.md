---
title: Поддержка контейнеров
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 8dbe36693e551bd03022f4a00044b75b912c834a
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875141"
---
## <a name="create-an-anomaly-detector-resource"></a>Создание ресурса "Детектор аномалий"

1. Войти на [портал Azure](https://portal.azure.com)
1. Нажмите Создать ресурс [ **детектор аномалий** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector)
1. Введите все необходимые настройки:

    |Параметр|Значение|
    |--|--|
    |Имя|Требуемое имя (от 2 до 64 символов)|
    |Подписка|Выберите соответствующую подписку|
    |Местоположение|Выберите доступное поблизости расположение|
    |Ценовая категория|`F0` — минимальная ценовая категория|
    |Группа ресурсов|Выберите доступную группу ресурсов|
    |Проверка подтверждения предварительного просмотра (требуется)|Читали ли вы уведомление о **предварительном просмотре** или нет|

1. Щелкните **Создать** и дождитесь создания ресурса. После создания перейдите на страницу ресурса
1. Соберите `endpoint` настроенный и ключ API:

    |Вкладка ресурсов в портале|Параметр|Значение|
    |--|--|--|
    |**Обзор**|Конечная точка|Копируйте конечную точку. Это похоже на`https://westus2.api.cognitive.microsoft.com/`|
    |**Ключи**|Ключ API|Копируй 1 из двух ключей. Это 32 алфавитного символа строки без каких-либо пробелов или тире, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|



