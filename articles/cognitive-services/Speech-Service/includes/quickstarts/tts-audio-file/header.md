---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/31/2020
ms.author: dapine
ms.openlocfilehash: cd8da5eb9313685361ca56b56c024c2dfb37276e
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76961412"
---
В этом кратком руководстве показано, как использовать пакет [SDK службы "Речь"](~/articles/cognitive-services/speech-service/speech-sdk.md) для преобразования текста в синтезированную речь в звуковом файле. В службе преобразования текста в речь предоставляется множество вариантов синтезированных голосов в рамках [поддерживаемых языков для преобразования текста в речь](../../../language-support.md#text-to-speech). После выполнения нескольких предварительных требований для синтезирования речи в файл необходимо выполнить всего пять шагов:
> [!div class="checklist"]
> * Создайте объект `SpeechConfig`, содержащий ключ и регион подписки.
> * Создайте объект конфигурации звукового файла, указывающий имя WAV-файла.
> * Создайте объект `SpeechSynthesizer` с помощью объектов конфигурации, приведенных выше.
> * С помощью объекта `SpeechSynthesizer` преобразуйте текст в синтезированную речь, сохранив ее в указанном звуковом файле.
> * Проверьте возвращенный результат `SpeechSynthesizer` на наличие ошибок.
