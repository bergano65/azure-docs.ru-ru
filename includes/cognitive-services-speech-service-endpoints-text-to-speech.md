---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 05/06/2019
ms.author: wolfma
ms.openlocfilehash: 4f48fc0ad65984df42e2a85853f39e49d7481302
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/02/2019
ms.locfileid: "65020439"
---
### <a name="standard-and-neural-voices"></a>"Стандартный" и нейронной голоса

Используйте эту таблицу для определения доступности "стандартный" и нейронной голоса регион/конечной точкой:

| Регион | Конечная точка | Стандартный голоса | Нейронной голоса |
|--------|----------|-----------------|---------------|
| Восточная часть Австралии | https://australiaeast.tts.speech.microsoft.com/cognitiveservices/v1 | Yes | Yes |
| Центральная Канада | https://canadacentral.tts.speech.microsoft.com/cognitiveservices/v1 | Yes | Yes |
| Центральный регион США | https://centralus.tts.speech.microsoft.com/cognitiveservices/v1 | Yes | Нет  |
| Восточная Азия | https://eastasia.tts.speech.microsoft.com/cognitiveservices/v1 | Yes | Нет  |
| Восточная часть США | https://eastus.tts.speech.microsoft.com/cognitiveservices/v1 | Yes | Yes |
| Восток США 2 | https://eastus2.tts.speech.microsoft.com/cognitiveservices/v1 | Yes | Нет  |
| Центральная Франция | https://francecentral.tts.speech.microsoft.com/cognitiveservices/v1 | Yes | Нет  |
| Центральная Индия | https://centralindia.tts.speech.microsoft.com/cognitiveservices/v1 | Yes | Yes |
| Восточная часть Японии | https://japaneast.tts.speech.microsoft.com/cognitiveservices/v1 | Yes | Нет  |
| Центральная Корея | https://koreacentral.tts.speech.microsoft.com/cognitiveservices/v1 | Yes | Нет  |
| Центрально-северная часть США | https://northcentralus.tts.speech.microsoft.com/cognitiveservices/v1 | Yes | Нет  |
| Северная Европа | https://northeurope.tts.speech.microsoft.com/cognitiveservices/v1 | Yes | Нет  |
| Центрально-южная часть США | https://southcentralus.tts.speech.microsoft.com/cognitiveservices/v1 | Yes | Yes |
| Юго-Восточная Азия | https://southeastasia.tts.speech.microsoft.com/cognitiveservices/v1 | Yes | Yes |
| Южная часть Великобритании | https://uksouth.tts.speech.microsoft.com/cognitiveservices/v1 | Yes | Yes |
| Западная Европа | https://westeurope.tts.speech.microsoft.com/cognitiveservices/v1 | Yes | Yes |
| Запад США | https://westus.tts.speech.microsoft.com/cognitiveservices/v1 | Yes | Нет  |
| Западный регион США 2 | https://westus2.tts.speech.microsoft.com/cognitiveservices/v1 | Yes | Yes |

### <a name="custom-voices"></a>Настраиваемое синтезирование голоса

Если вы создали настраиваемый голос, используйте конечной точки, которую вы создали. Можно также использовать конечные точки, перечисленные ниже, заменив `{deploymentId}` идентификатором развертывания для вашей голосовую модель.

| Регион | Конечная точка |
|--------|----------|
| Восточная часть Австралии | https://australiaeast.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId} |
| Центральная Канада | https://canadacentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId} |
| Центральный регион США | https://centralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId} |
| Восточная Азия | https://eastasia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId} |
| Восточная часть США | https://eastus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId} |
| Восток США 2 | https://eastus2.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId} |
| Центральная Франция | https://francecentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId} |
| Центральная Индия | https://centralindia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId} |
| Восточная часть Японии | https://japaneast.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId} |
| Центральная Корея | https://koreacentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId} |
| Центрально-северная часть США | https://northcentralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId} |
| Северная Европа | https://northeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId} |
| Центрально-южная часть США | https://southcentralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId} |
| Юго-Восточная Азия | https://southeastasia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId} |
| Южная часть Великобритании | https://uksouth.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId} |
| Западная Европа | https://westeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId} |
| Запад США | https://westus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId} |
| Западный регион США 2 | https://westus2.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId} |
