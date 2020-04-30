---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: f0baac79bfbfc06b24d692caef87a20013c4654c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "81422096"
---
Чтобы выполнить потоковую передачу в сжатом аудио формате в службу распознавания речи `SPXPullAudioInputStream` , `SPXPushAudioInputStream`Создайте или.

В следующем фрагменте кода показано `SPXAudioConfiguration` `SPXPushAudioInputStream`, как создать объект из экземпляра, указав MP3 в качестве формата сжатия потока.

[!code-objectivec[Set up the input stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=67-76&highlight=2-11)]

В следующем фрагменте кода показано, как можно считывать сжатые аудиоданные из файла и передавать их `SPXPushAudioInputStream`в.

[!code-objectivec[Push compressed audio data into the stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=106-150&highlight=19-44)]
