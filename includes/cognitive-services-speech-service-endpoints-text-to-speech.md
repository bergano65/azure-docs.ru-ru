---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 05/06/2019
ms.author: wolfma
ms.openlocfilehash: a1de313245e826511a7e9c864165ac44bcb48530
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80234206"
---
### <a name="standard-and-neural-voices"></a>Стандартные и нейронные голоса

Используйте эту таблицу для определения доступности стандартных и нейронных голосов по регионам/конечным точкам:

| Регион | Конечная точка | Стандартные голоса | Нейронные голоса |
|--------|----------|-----------------|---------------|
| Восточная Австралия | `https://australiaeast.tts.speech.microsoft.com/cognitiveservices/v1` | Да | Да |
| Южная Бразилия | `https://brazilsouth.tts.speech.microsoft.com/cognitiveservices/v1` | Да | нет |
| Центральная Канада | `https://canadacentral.tts.speech.microsoft.com/cognitiveservices/v1` | Да | Да |
| Центральная часть США | `https://centralus.tts.speech.microsoft.com/cognitiveservices/v1` | Да | нет |
| Восточная Азия | `https://eastasia.tts.speech.microsoft.com/cognitiveservices/v1` | Да | нет |
| Восточная часть США | `https://eastus.tts.speech.microsoft.com/cognitiveservices/v1` | Да | Да |
| восточная часть США 2 | `https://eastus2.tts.speech.microsoft.com/cognitiveservices/v1` | Да | нет |
| Центральная Франция | `https://francecentral.tts.speech.microsoft.com/cognitiveservices/v1` | Да | нет |
| Центральная Индия | `https://centralindia.tts.speech.microsoft.com/cognitiveservices/v1` | Да | Да |
| Восточная Япония | `https://japaneast.tts.speech.microsoft.com/cognitiveservices/v1` | Да | нет |
| Западная Япония | `https://japanwest.tts.speech.microsoft.com/cognitiveservices/v1` | Да | нет |
| Республика Корея, центральный регион | `https://koreacentral.tts.speech.microsoft.com/cognitiveservices/v1` | Да | нет |
| Центрально-северная часть США | `https://northcentralus.tts.speech.microsoft.com/cognitiveservices/v1` | Да | нет |
| Северная Европа | `https://northeurope.tts.speech.microsoft.com/cognitiveservices/v1` | Да | нет |
| Центрально-южная часть США | `https://southcentralus.tts.speech.microsoft.com/cognitiveservices/v1` | Да | Да |
| Юго-Восточная Азия | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/v1` | Да | Да |
| южная часть Соединенного Королевства | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/v1` | Да | Да |
| Западная Европа | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/v1` | Да | Да |
| западная часть США | `https://westus.tts.speech.microsoft.com/cognitiveservices/v1` | Да | нет |
| западная часть США 2 | `https://westus2.tts.speech.microsoft.com/cognitiveservices/v1` | Да | Да |

### <a name="custom-voices"></a>Настраиваемое синтезирование голоса

Если вы создали пользовательский голосовой шрифт, используйте конечную точку, которую вы создали. Вы также можете использовать конечные точки, `{deploymentId}` перечисленные ниже, заменив идентификатор развертывания для голосовой модели.

| Регион | Конечная точка |
|--------|----------|
| Восточная Австралия | `https://australiaeast.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Южная Бразилия | `https://brazilsouth.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Центральная Канада | `https://canadacentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Центральная часть США | `https://centralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Восточная Азия | `https://eastasia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Восточная часть США | `https://eastus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| восточная часть США 2 | `https://eastus2.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Центральная Франция | `https://francecentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Центральная Индия | `https://centralindia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Восточная Япония | `https://japaneast.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Западная Япония | `https://japanwest.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Республика Корея, центральный регион | `https://koreacentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Центрально-северная часть США | `https://northcentralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Северная Европа | `https://northeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Центрально-южная часть США | `https://southcentralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Юго-Восточная Азия | `https://southeastasia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| южная часть Соединенного Королевства | `https://uksouth.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Западная Европа | `https://westeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| западная часть США | `https://westus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| западная часть США 2 | `https://westus2.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
