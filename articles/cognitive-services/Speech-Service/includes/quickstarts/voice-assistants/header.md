---
title: Краткое руководство. Создание пользовательского голосового помощника в службе "Речь"
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 03/20/2020
ms.author: travisw
ms.openlocfilehash: bb88ef7f048b2454f05ef33286e59277070c3ea0
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80241765"
---
В этом кратком руководстве вы будете использовать [пакет SDK для службы "Речь"](~/articles/cognitive-services/speech-service/speech-sdk.md) для создания пользовательского приложения голосового помощника, которое подключается к боту, который вы уже создали и настроили. Если необходимо создать бот, см. дополнительные сведения в [исчерпывающем руководстве](~/articles/cognitive-services/speech-service/tutorial-voice-enable-your-bot-speech-sdk.md).

После выполнения нескольких предварительных требований подключение пользовательского интерфейса пользователя займет всего несколько шагов:
> [!div class="checklist"]
> * Создайте объект `BotFrameworkConfig`, содержащий ключ и регион подписки.
> * Создайте объект `DialogServiceConnector`, используя приведенный выше объект `BotFrameworkConfig`.
> * С помощью объекта `DialogServiceConnector` запустите процесс прослушивания для одного речевого фрагмента.
> * Проверьте возвращенный результат `ActivityReceivedEventArgs`.
