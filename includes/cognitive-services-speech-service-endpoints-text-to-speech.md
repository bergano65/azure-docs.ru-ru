---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 05/06/2019
ms.author: wolfma
ms.openlocfilehash: 6b16dea3c4f9241133b91b092c90c9056da57de0
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100515119"
---
### <a name="standard-and-neural-voices"></a>Стандартные и нейронные голоса

Используйте эту таблицу, чтобы определить доступность стандартных и нейронных голосов по регионам и конечным точкам:

| Регион | Конечная точка | Нейронные голоса | Стандартные голоса |
|--------|----------|-----------------|---------------|
| Восточная Австралия | `https://australiaeast.tts.speech.microsoft.com/cognitiveservices/v1` | Да | Да |
| Brazil South | `https://brazilsouth.tts.speech.microsoft.com/cognitiveservices/v1` | Нет | Да |
| Центральная Канада | `https://canadacentral.tts.speech.microsoft.com/cognitiveservices/v1` | Да | Да |
| Центральная часть США | `https://centralus.tts.speech.microsoft.com/cognitiveservices/v1` | Нет | Да |
| Восточная Азия | `https://eastasia.tts.speech.microsoft.com/cognitiveservices/v1` | Нет | Да |
| Восточная часть США | `https://eastus.tts.speech.microsoft.com/cognitiveservices/v1` | Да | Да |
| восточная часть США 2 | `https://eastus2.tts.speech.microsoft.com/cognitiveservices/v1` | Нет | Да |
| Центральная Франция | `https://francecentral.tts.speech.microsoft.com/cognitiveservices/v1` | Нет | Да |
| Центральная Индия | `https://centralindia.tts.speech.microsoft.com/cognitiveservices/v1` | Да | Да |
| Japan East | `https://japaneast.tts.speech.microsoft.com/cognitiveservices/v1` | Нет | Да |
| Западная Япония | `https://japanwest.tts.speech.microsoft.com/cognitiveservices/v1` | Нет | Да |
| Республика Корея, центральный регион | `https://koreacentral.tts.speech.microsoft.com/cognitiveservices/v1` | Нет | Да |
| Центрально-северная часть США | `https://northcentralus.tts.speech.microsoft.com/cognitiveservices/v1` | Нет | Да |
| Северная Европа | `https://northeurope.tts.speech.microsoft.com/cognitiveservices/v1` | Нет | Да |
| Центрально-южная часть США | `https://southcentralus.tts.speech.microsoft.com/cognitiveservices/v1` | Да | Да |
| Southeast Asia | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/v1` | Да | Да |
| южная часть Соединенного Королевства | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/v1` | Да | Да |
| Западная Европа | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/v1` | Да | Да |
| Западная часть США | `https://westus.tts.speech.microsoft.com/cognitiveservices/v1` | Нет | Да |
| Западная часть США 2 | `https://westus2.tts.speech.microsoft.com/cognitiveservices/v1` | Да | Да |

> [!TIP]
> [Голоса в предварительной версии](../articles/cognitive-services/Speech-Service/language-support.md#neural-voices-in-preview) доступны только в следующих трех регионах: Восточная часть США, Западная Европа и Юго-Восточная Азия.

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

### <a name="custom-neural-voice"></a>Что такое Пользовательский нейронный голос

В следующей таблице приведены сведения о региональной поддержке пользовательских функций нейронной речи.

| Функция | Поддерживаемые регионы |
|---|---|
| Размещение модели речи | Восточная часть США, Западная часть США 2, Юго-Центральная часть США, Юго-Восточная Азия, южная часть Соединенного Королевства, Западная Европа, Восточная Австралия |
| Символы реального времени | Восточная часть США, Западная часть США 2, Юго-Центральная часть США, Юго-Восточная Азия, южная часть Соединенного Королевства, Западная Европа, Восточная Австралия |
| Длинные звуковые символы | Восточная часть США, Западная Европа, южная часть Соединенного Королевства, Юго-Восточная Азия, Центральная Индия |
| Пользовательское нейронное обучение | Восточная часть США, южная часть Соединенного Королевства |

### <a name="long-audio-api"></a>Длинный аудио API

Длинный аудио API доступен в нескольких регионах с уникальными конечными точками.

| Регион | Конечная точка |
|--------|----------|
| Восточная часть США | `https://eastus.customvoice.api.speech.microsoft.com` |
| Центральная Индия | `https://centralindia.customvoice.api.speech.microsoft.com` |
| Юго-Восточная Азия | `https://southeastasia.customvoice.api.speech.microsoft.com` |
| южная часть Соединенного Королевства | `https://uksouth.customvoice.api.speech.microsoft.com` |
| Западная Европа | `https://westeurope.customvoice.api.speech.microsoft.com` |
