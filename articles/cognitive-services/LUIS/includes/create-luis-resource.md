---
title: Создание ресурса LUIS
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/23/2019
ms.author: dapine
ms.openlocfilehash: a765ac27936da9da5a2f41464c17491e3561f44b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "73465931"
---
## <a name="create-a-luis-resource"></a>Создание ресурса LUIS

1. Войти на [портал Azure](https://portal.azure.com)
1. Щелкните [Создать **Распознавание речи**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne).
1. Введите все необходимые настройки:

    |Параметр|Значение|
    |--|--|
    |name|Требуемое имя (от 2 до 64 символов)|
    |Подписка|Выберите соответствующую подписку|
    |Расположение|Выберите доступное поблизости расположение|
    |Ценовая категория|`F0` — минимальная ценовая категория|
    |Группа ресурсов|Выберите доступную группу ресурсов|

1. Щелкните **Создать** и дождитесь создания ресурса. После создания перейдите на страницу ресурсов.
1. Соберите `endpoint` настроенный и aPI ключ, [см. сбор необходимых параметров.](#gathering-required-parameters)

[!INCLUDE [Gathering required parameters](../../containers/includes/container-gathering-required-parameters.md)]
