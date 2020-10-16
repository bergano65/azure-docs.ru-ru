---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 05/06/2019
ms.author: wolfma
ms.openlocfilehash: a1de313245e826511a7e9c864165ac44bcb48530
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "80234206"
---
### <a name="standard-and-neural-voices"></a>Стандартные и нейронные голоса

Используйте эту таблицу, чтобы определить доступность стандартных и нейронных голосов по регионам и конечным точкам:

| Регион | Конечная точка | Стандартные голоса | Нейронные голоса |
|--------|----------|-----------------|---------------|
| Восточная Австралия | `https://australiaeast.tts.speech.microsoft.com/cognitiveservices/v1` | Да | Да |
| Южная Бразилия | `https://brazilsouth.tts.speech.microsoft.com/cognitiveservices/v1` | Да | Нет |
| Центральная Канада | `https://canadacentral.tts.speech.microsoft.com/cognitiveservices/v1` | Да | Да |
| Центральная часть США | `https://centralus.tts.speech.microsoft.com/cognitiveservices/v1` | Да | Нет |
| Восточная Азия | `https://eastasia.tts.speech.microsoft.com/cognitiveservices/v1` | Да | Нет |
| Восточная часть США | `https://eastus.tts.speech.microsoft.com/cognitiveservices/v1` | Да | Да |
| восточная часть США 2 | `https://eastus2.tts.speech.microsoft.com/cognitiveservices/v1` | Да | Нет |
| Центральная Франция | `https://francecentral.tts.speech.microsoft.com/cognitiveservices/v1` | Да | Нет |
| Центральная Индия | `https://centralindia.tts.speech.microsoft.com/cognitiveservices/v1` | Да | Да |
| Japan East | `https://japaneast.tts.speech.microsoft.com/cognitiveservices/v1` | Да | Нет |
| Западная Япония | `https://japanwest.tts.speech.microsoft.com/cognitiveservices/v1` | Да | Нет |
| Республика Корея, центральный регион | `https://koreacentral.tts.speech.microsoft.com/cognitiveservices/v1` | Да | Нет |
| Центрально-северная часть США | `https://northcentralus.tts.speech.microsoft.com/cognitiveservices/v1` | Да | Нет |
| Северная Европа | `https://northeurope.tts.speech.microsoft.com/cognitiveservices/v1` | Да | Нет |
| Центрально-южная часть США | `https://southcentralus.tts.speech.microsoft.com/cognitiveservices/v1` | Да | Да |
| Юго-Восточная Азия | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/v1` | Да | Да |
| южная часть Соединенного Королевства | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/v1` | Да | Да |
| Западная Европа | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/v1` | Да | Да |
| западная часть США | `https://westus.tts.speech.microsoft.com/cognitiveservices/v1` | Да | Нет |
| западная часть США 2 | `https://westus2.tts.speech.microsoft.com/cognitiveservices/v1` | Да | Да |

### <a name="custom-voices"></a>Настраиваемое синтезирование голоса

Если вы создали пользовательский шрифт голоса, используйте созданную конечную точку. Вы также можете использовать перечисленные ниже конечные точки, заменив параметр на `{deploymentId}` идентификатор развертывания для модели голоса.

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
