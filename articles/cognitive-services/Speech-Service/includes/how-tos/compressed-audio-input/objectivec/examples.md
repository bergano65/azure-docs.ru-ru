---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: dapine
ms.openlocfilehash: 788903d802ca47c763734e7cf6ddbbf3b0032203
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/09/2020
ms.locfileid: "78943837"
---
Чтобы выполнить потоковую передачу в сжатом аудио формате в службу распознавания речи, создайте `SPXPullAudioInputStream` или `SPXPushAudioInputStream`.

В следующем фрагменте кода показано, как создать `SPXAudioConfiguration` из экземпляра `SPXPushAudioInputStream`, указав MP3 в качестве формата сжатия потока.

[!code-objectivec[Set up the input stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=67-76&highlight=2-11)]

В следующем фрагменте кода показано, как можно считывать сжатые аудио-данные из файла и переносить их в `SPXPushAudioInputStream`.

[!code-objectivec[Push compressed audio data into the stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=106-150&highlight=19-44)]
