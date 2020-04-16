---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: f0baac79bfbfc06b24d692caef87a20013c4654c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422096"
---
Для потоковой передачи в сжатом аудиоформате на службу Speech создайте `SPXPullAudioInputStream` или `SPXPushAudioInputStream`.

Следующий фрагмент показывает, как `SPXAudioConfiguration` создать из `SPXPushAudioInputStream`экземпляра, указав MP3 как формат сжатия потока.

[!code-objectivec[Set up the input stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=67-76&highlight=2-11)]

Следующий фрагмент показывает, как сжатые аудио данные могут быть `SPXPushAudioInputStream`прочитаны из файла и закачивается в .

[!code-objectivec[Push compressed audio data into the stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=106-150&highlight=19-44)]
