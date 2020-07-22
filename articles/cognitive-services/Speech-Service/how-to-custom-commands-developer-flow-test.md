---
title: Тестирование приложения пользовательских команд
titleSuffix: Azure Cognitive Services
description: В этой статье вы узнаете о различных подходах к тестированию приложения с пользовательскими командами.
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: a7992a2a27822f87cc8667ff86f642d1d47d09aa
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/24/2020
ms.locfileid: "85307801"
---
# <a name="test-your-custom-commands-application"></a>Тестирование приложения пользовательских команд

В этой статье вы узнаете о различных подходах к тестированию приложения с пользовательскими командами.

## <a name="test-in-the-portal"></a>Тестирование на портале

Тестирование на портале — самый простой и быстрый способ проверить, работает ли пользовательское командное приложение должным образом. После успешного обучения приложения нажмите кнопку Кнопка, `Test` чтобы начать тестирование.

> [!div class="mx-imgBorder"]
> ![Тестирование на портале](media/custom-commands/create-basic-test-chat.png)

## <a name="test-with-windows-voice-assistant-client"></a>Тестирование с помощью клиента Windows Voice Assistant

Клиентский помощник Windows — это приложение Windows Presentation Foundation (WPF) в C#, которое упрощает тестирование взаимодействия с программой-Bot перед созданием пользовательского клиентского приложения.

Средство может принять идентификатор настраиваемого командного приложения. Она позволяет протестировать завершение задач или сценарий управления, размещенный в службе пользовательских команд.

Чтобы настроить клиент, извлеките [клиент помощника по Windows Voice](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/tree/master/clients/csharp-wpf).

> [!div class="mx-imgBorder"]
> ![ВВАК создать профиль](media/custom-commands/conversation.png)

## <a name="test-with-speech-sdk-enabled-client-applications"></a>Тестирование с помощью клиентских приложений с поддержкой речевого SDK 
Пакет средств разработки речевых программ (SDK) предоставляет множество возможностей службы распознавания речи, которые позволяют разрабатывать приложения с поддержкой речи. Он также доступен во многих языках программирования и на всех платформах.

Чтобы настроить клиентское приложение универсальная платформа Windows (UWP) с помощью пакета SDK для распознавания речи и интегрировать его с пользовательским командным приложением:  
- [Как интегрироваться с клиентским приложением с помощью пакета SDK для речи](./how-to-custom-commands-setup-speech-sdk.md)
- [Как отправить действие клиентскому приложению](./how-to-custom-commands-send-activity-to-client.md)
- [Как настроить конечные точки веб-сайта](./how-to-custom-commands-setup-web-endpoints.md)

Для других языков программирования и платформ:
- [Языки программирования Speech SDK, платформы, производственные мощности сценариев](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk)
- [Примеры кодов для речевого помощника](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant)

## <a name="next-steps"></a>Следующие шаги

> [!div class="nextstepaction"]
> [См. примеры на GitHub](https://aka.ms/speech/cc-samples)

