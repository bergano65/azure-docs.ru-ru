---
title: Суверенные облака - Речевой сервис
titleSuffix: Azure Cognitive Services
description: Узнайте, как использовать Суверенные облака
services: cognitive-services
author: cbasoglu
manager: xdh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 1/14/2020
ms.author: cbasoglu
ms.openlocfilehash: b41967033b00144ca5bd52ce23cf8aabcea6749e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78228080"
---
# <a name="speech-services-with-sovereign-clouds"></a>Речевые услуги с суверенными облаками

## <a name="azure-government-united-states"></a>Правительство Лазурного государства (Соединенные Штаты Америки)

Только федеральные, государственные, местные и племенные правительства США и их партнеры имеют доступ к этому специализированному экземпляру с операциями, контролируемыми проверенными гражданами США.
- Регионы: ГУБЕРНАТОР США Вирджиния
- SR в SpeechSDK:*конфигурация. FromHost("wss://virginia.stt.speech.azure.us",\<"ваш\>ключ");*
- TTS в SpeechSDK: *конфигурация. FromHost("https://virginia.tts.speech.azure.us",[]()\<"Your-key");\>*
- Токены аутентификации: https:/virginia.api.cognitive.microsoft.us/sts/v1.0/issueToken[]()
- Портал Azure: https://portal.azure.us  
- Пользовательские речи портал:https://virginia.cris.azure.us/Home/CustomSpeech
- Доступные SKUS: S0
- Поддерживаемые функции
  - Преобразование речи в текст
  - Пользовательская речь (акустическая/языковая адаптация)
  - Преобразование текста в речь
  - Переводчик речи
- Неподдерживаемые функции
  - Custom Voice (Пользовательские голосовые модели)
  - Нейронные голоса для текста к речи
- Поддерживаемые локализов: Поддерживаются языковые продукты для следующих языков.
  - Арабский (ар-з)
  - Китайский (zh-)
  - Английский (ан-з)
  - Французский (фр-з)
  - Немецкий (де-з)
  - Hindi
  - Корейский
  - Русский
  - Испанский язык (es-)

## <a name="microsoft-azure-china"></a>Microsoft Azure Китай

Расположенный в Китае, центр обработки данных Azure с прямым доступом к China Mobile, China Telecom, China Unicom и другим крупным магистральным сетям оператора, для китайских пользователей, чтобы обеспечить высокоскоростной и стабильный доступ к локальной сети.
- Регионы: Китай Восток 2 (Шанхай)
- SR в SpeechSDK: *конфигурация. FromHost("wss://chinaeast2.stt.speech.azure.cn",\<"ваш\>ключ");*
- TTS в SpeechSDK: *конфигурация. FromHost("https://chinaeast2.tts.speech.azure.cn",[]()\<"Your-key");\>*
- Токены аутентификации: https:/chinaeast2.api.cognitive.azure.cn/sts/v1.0/issueToken[]()
- Портал Azure: https://portal.azure.cn
- Пользовательские речи портал:https://speech.azure.cn/CustomSpeech
- Доступные SKUS: S0
- Поддерживаемые функции
  - Преобразование речи в текст
  - Пользовательская речь (акустическая/языковая адаптация)
  - Преобразование текста в речь
  - Переводчик речи
- Неподдерживаемые функции
  - Custom Voice (Пользовательские голосовые модели)
  - Нейронные голоса для текста к речи
- Поддерживаемые локализов: Поддерживаются языковые продукты для следующих языков.
  - Арабский (ар-з)
  - Китайский (zh-)
  - Английский (ан-з)
  - Французский (фр-з)
  - Немецкий (де-з)
  - Hindi
  - Корейский
  - Русский
  - Испанский язык (es-)

