---
title: Часто задаваемые вопросы о речи прямой линии
titleSuffix: Azure Cognitive Services
description: Получите ответы на самые популярные вопросы о виртуальных помощники voice-first с помощью канала прямой линии речи.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: travisw
ms.openlocfilehash: 8427417c9b579c7dfa21f834ce1ca77099159eb2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65072609"
---
# <a name="voice-first-virtual-assistants-preview-frequently-asked-questions"></a>Голоса с начала виртуального помощники предварительной версии. Часто задаваемые вопросы

Если не удается найти ответы на вопросы в этом документе, см. статью [другие варианты поддержки](support.md).

## <a name="general"></a>Общие сведения

**Вопрос. Что такое прямой линии речи?**

**Ответ.** `SpeechBotConnector` Speech SDK предоставляет двунаправленный, асинхронное взаимодействие с программами-роботами, подключенные к канала прямой линии речи на Bot Framework. Этот канал предоставляет координируемый доступ к речи в текст и преобразования текста в речь из служб Azure речи, позволяющие программы-роботы становятся полностью голос в, голосовая связь out естественного взаимодействия. С поддержкой пробуждения слова и пробуждения проверки Word это решение позволяет создавать настраиваемый виртуальный помощники по voice-first для вашей торговой марке или продукта.

**Вопрос. Как начать работу?**

**Ответ.** Лучший способ начинается с создания виртуальные помощники voice-first — начать с [Создание основные программы-робота, Bot Framework](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0). Подключите ботом [канала прямой линии речи](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech).

## <a name="debugging"></a>Отладка

**Вопрос. Я получаю ошибку 401 при подключении и ничего не работает. Я знаю, что мой ключ речи подписки является допустимым. Что происходит?**

**Ответ.** В предварительной версии прямой линии имеет очень особые ограничения на подписку, использованную. Убедитесь, вы используете **речи** ресурсов (Microsoft.CognitiveServicesSpeechServices, «Речь») и *не* **Cognitive Services** ресурса () Microsoft.CognitiveServicesAllInOne, «Все службы Cognitive Services»). Кроме того, обратите внимание, что только **westus2** регион в настоящее время поддерживается.

![Исправьте подписки для распознавания речи прямую линию](media/voice-first-virtual-assistants/faq-supported-subscription.png "пример совместимых подписки речи")

**Вопрос. Вернуть распознавания текста из речи прямой линии, но я вижу ошибку "1011" и ничего из моей программ-роботов. Почему?**

**Ответ.** Эта ошибка указывает на проблему между программ-роботов и прямой линии речи. Убедитесь, что [подключения канала прямой линии речи](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech), [добавлена поддержка протокола потоковой передачи](https://aka.ms/botframework/addstreamingprotocolsupport) для программ-роботов (с соответствующим поддержкой веб-сокет), а затем убедитесь, что ваш бот отвечает на входящие запросы из канала.

**Вопрос. Это по-прежнему не работает и/или другую ошибку при использовании SpeechBotConnector и порождаются неясно, что следует сделать. Что *следует* делать?**

**Ответ.** Ведение журнала на основе файлов обеспечивает существенно более подробно и поможет ускорить запросы в службу поддержки. Чтобы включить эту функцию, см. в разделе [как использовать ведение журнала](how-to-use-logging.md).

## <a name="next-steps"></a>Дальнейшие действия

* [Устранение неполадок](troubleshooting.md)
* [Заметки о выпуске](releasenotes.md)
