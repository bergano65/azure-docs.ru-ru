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
ms.openlocfilehash: 2647c604bebc2885f5c8516eb997792b65751e88
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "80876102"
---
## <a name="create-an-computer-vision-resource"></a>Создание ресурса Компьютерное зрение

1. Войдите на [портал Azure](https://portal.azure.com).
1. Щелкните [создать **компьютерное зрение** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision) ресурс.
1. Введите все необходимые параметры:

    |Параметр|Значение|
    |--|--|
    |Имя|Требуемое имя (от 2 до 64 символов)|
    |Подписка|Выберите соответствующую подписку|
    |Расположение|Выберите доступное поблизости расположение|
    |Ценовая категория|`F0` — минимальная ценовая категория|
    |Группа ресурсов|Выберите доступную группу ресурсов|

1. Щелкните **Создать** и дождитесь создания ресурса. После создания перейдите на страницу ресурсов.
1. Собирать настройки `{ENDPOINT_URI}` и `{API_KEY}`, см. Дополнительные сведения в разделе [сбор обязательных параметров](../computer-vision-how-to-install-containers.md#gathering-required-parameters) .
