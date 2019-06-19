---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 05/06/2019
ms.author: wolfma
ms.openlocfilehash: d5a4b3a07854c2664de7ec60f3677b666798a9bd
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67185577"
---
### <a name="standard-and-neural-voices"></a>"Стандартный" и нейронной голоса

Используйте эту таблицу для определения доступности "стандартный" и нейронной голоса регион/конечной точкой:

| Регион | Конечная точка | Стандартный голоса | Нейронной голоса |
|--------|----------|-----------------|---------------|
| Восточная часть Австралии | `https://australiaeast.tts.speech.microsoft.com/cognitiveservices/v1` | Да | Да |
| Центральная Канада | `https://canadacentral.tts.speech.microsoft.com/cognitiveservices/v1` | Да | Да |
| Центральный регион США | `https://centralus.tts.speech.microsoft.com/cognitiveservices/v1` | Да | Нет |
| Восточная Азия | `https://eastasia.tts.speech.microsoft.com/cognitiveservices/v1` | Да | Нет |
| Восточная часть США | `https://eastus.tts.speech.microsoft.com/cognitiveservices/v1` | Да | Да |
| Восток США 2 | `https://eastus2.tts.speech.microsoft.com/cognitiveservices/v1` | Да | Нет |
| Центральная Франция | `https://francecentral.tts.speech.microsoft.com/cognitiveservices/v1` | Да | Нет |
| Центральная Индия | `https://centralindia.tts.speech.microsoft.com/cognitiveservices/v1` | Да | Да |
| Восточная часть Японии | `https://japaneast.tts.speech.microsoft.com/cognitiveservices/v1` | Да | Нет |
| Центральная Корея | `https://koreacentral.tts.speech.microsoft.com/cognitiveservices/v1` | Да | Нет |
| Центрально-северная часть США | `https://northcentralus.tts.speech.microsoft.com/cognitiveservices/v1` | Да | Нет |
| Северная Европа | `https://northeurope.tts.speech.microsoft.com/cognitiveservices/v1` | Да | Нет |
| Центрально-южная часть США | `https://southcentralus.tts.speech.microsoft.com/cognitiveservices/v1` | Да | Да |
| Юго-Восточная Азия | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/v1` | Да | Да |
| Южная часть Великобритании | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/v1` | Да | Да |
| Западная Европа | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/v1` | Да | Да |
| Запад США | `https://westus.tts.speech.microsoft.com/cognitiveservices/v1` | Да | Нет |
| Западный регион США 2 | `https://westus2.tts.speech.microsoft.com/cognitiveservices/v1` | Да | Да |

### <a name="custom-voices"></a>Настраиваемое синтезирование голоса

Если вы создали настраиваемый голос, используйте конечной точки, которую вы создали. Можно также использовать конечные точки, перечисленные ниже, заменив `{deploymentId}` идентификатором развертывания для вашей голосовую модель.

| Регион | Конечная точка |
|--------|----------|
| Восточная часть Австралии | `https://australiaeast.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Центральная Канада | `https://canadacentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Центральный регион США | `https://centralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Восточная Азия | `https://eastasia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Восточная часть США | `https://eastus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Восток США 2 | `https://eastus2.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Центральная Франция | `https://francecentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Центральная Индия | `https://centralindia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Восточная часть Японии | `https://japaneast.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Центральная Корея | `https://koreacentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Центрально-северная часть США | `https://northcentralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Северная Европа | `https://northeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Центрально-южная часть США | `https://southcentralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Юго-Восточная Азия | `https://southeastasia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Южная часть Великобритании | `https://uksouth.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Западная Европа | `https://westeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Запад США | `https://westus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Западный регион США 2 | `https://westus2.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
