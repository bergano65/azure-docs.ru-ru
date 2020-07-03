---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/31/2020
ms.author: trbye
ms.openlocfilehash: 005cf83508d25e8f44190e07336fbb4e444f8e6b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400386"
---
При работе с этим кратким руководством будет использоваться [пакет SDK службы "Речь"](~/articles/cognitive-services/speech-service/speech-sdk.md) для преобразования текста в синтезированную речь. В службе преобразования текста в речь предоставляется множество вариантов синтезированных голосов в рамках [поддерживаемых языков для преобразования текста в речь](../../../language-support.md#text-to-speech). После выполнения нескольких предварительных требований преобразование синтезированной речи с помощью говорящих по умолчанию укладывается всего в четыре шага:
> [!div class="checklist"]
> * Создайте объект `SpeechConfig`, содержащий ключ и регион подписки.
> * Создайте объект `SpeechSynthesizer`, используя приведенный выше объект `SpeechConfig`.
> * Используйте объект `SpeechSynthesizer` для диктовки текста.
> * Проверьте возвращенный результат `SpeechSynthesisResult` на наличие ошибок.
