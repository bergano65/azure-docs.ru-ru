---
title: Приступая к работе с API распознавания речи Bing с помощью клиентских библиотек | Документация Майкрософт
titlesuffix: Azure Cognitive Services
description: Используйте клиентские библиотеки Распознавания речи Bing в Microsoft Cognitive Services для разработки приложений, которые преобразовывают устную речь в текст.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 89eb18a2b4af76f6489442dc66ab12d0840e92c7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60515232"
---
# <a name="get-started-with-bing-speech-service-client-libraries"></a>Знакомство с клиентскими библиотеками для службы "Распознавание речи Bing"

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Помимо перенаправления HTTP-запросов через REST API, служба "Распознавание речи Bing" предоставляет разработчикам клиентские библиотеки для работы с речью на разных языках. Клиентские библиотеки для работы с речью:

- Поддерживают дополнительные возможности при распознавании речи, такие как промежуточные результаты в режиме реального времени, длительный аудиопоток (до 10 минут) и непрерывное распознавание.
- Предоставляют простой и идиоматический программный API на выбранном языке.
- Скрывают подробности сообщения.

В настоящее время доступны следующие клиентские библиотеки для Распознавания речи Bing:

- [Библиотека для классических приложений C#](GetStartedCSharpDesktop.md)
- [Библиотека для служб C#](GetStartedCSharpServiceLibrary.md)
- [Библиотека для JavaScript](GetStartedJSWebsockets.md)
- [Библиотека Java для Android](GetStartedJavaAndroid.md)
- [Библиотека Objective-C для iOS](Get-Started-ObjectiveC-iOS.md)

## <a name="additional-resources"></a>Дополнительные ресурсы

- Страница [образцы](../samples.md) содержит полные образцы использования клиентских библиотек для работы с речью.
- Если вам нужна клиентская библиотека, которая еще не поддерживается, вы можете создать свой собственный пакет SDK. Реализуйте [протокол речи WebSocket ](../API-Reference-REST/websocketprotocol.md) на этой платформе и используйте нужный вам язык.

## <a name="license"></a>Лицензия

Все пакеты SDK и образцы Cognitive Services лицензированы лицензией MIT. Дополнительные сведения можно найти в разделе [Лицензия](https://github.com/Microsoft/Cognitive-Speech-STT-JavaScript/blob/master/LICENSE.md).
