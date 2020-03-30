---
title: Транскрибирование в реальном времени
titleSuffix: Azure Media Services
description: Узнайте о транскрипции медиа-служб Azure Media.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 11/19/2019
ms.author: juliako
ms.openlocfilehash: b364b6e70e3b5723c483bc3435f0c3a152c03aa9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79499877"
---
# <a name="live-transcription-preview"></a>Прямая транскрипция (предварительный просмотр)

Служба мультимедиа Azure предоставляет видео, аудио и текст в различных протоколах. Когда вы публикуете свой живой эфир с помощью MPEG-DASH или HLS/CMAF, а затем вместе с видео и аудио, наша служба обеспечивает транскрибированный текст в IMSC1.1 совместимый TTML. Доставка упакована в фрагменты MPEG-4 Часть 30 (ISO/IEC 14496-30). При использовании доставки через HLS/TS, то текст доставляется как chunked VTT.

В этой статье описывается, как включить живую транскрипцию при потоковой передаче Live Event с Azure Media Services v3. Прежде чем продолжить работу, убедитесь, что вы знакомы с использованием Медиа-услуг v3 REST AIS (см. [этот учебник](stream-files-tutorial-with-rest.md) для получения подробной информации). Вы также должны быть знакомы с концепцией [потоковой передачи в реальном времени.](live-streaming-overview.md) Рекомендуется завершить [поток жить с медиа-услуг](stream-live-tutorial-with-api.md) учебник.

> [!NOTE]
> В настоящее время, живая транскрипция доступна только в качестве функции предварительного просмотра в регионе Западного США 2. Он поддерживает транскрипцию произнесенных слов на английском языке к тексту. Ссылка API на эту функцию находится ниже- если утихнет ее в предварительном просмотре, детали не доступны с нашими документами REST.

## <a name="creating-the-live-event"></a>Создание события в прямом эфире

Чтобы создать Live Event, вы отправляете операцию PUT в версию 2019-05-01-preview, например:

```
PUT https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/liveEvents/:liveEventName?api-version=2019-05-01-preview&autoStart=true 
```

Операция имеет следующий орган (где сквозной Live Event создается с RTMP как протоколом глотания). Обратите внимание на добавление свойства транскрипции. Единственное допустимое значение для языка en-US.

```
{ 
  "properties": { 
    "description": "Demonstrate how to enable live transcriptions", 
    "input": { 
      "streamingProtocol": "RTMP", 
      "accessControl": { 
        "ip": { 
          "allow": [ 
            { 
              "name": "Allow All", 
              "address": "0.0.0.0", 
              "subnetPrefixLength": 0 
            } 
          ] 
        } 
      } 
    }, 
    "preview": { 
      "accessControl": { 
        "ip": { 
          "allow": [ 
            { 
              "name": "Allow All", 
              "address": "0.0.0.0", 
              "subnetPrefixLength": 0 
            } 
          ] 
        } 
      } 
    }, 
    "encoding": { 
      "encodingType": "None" 
    }, 
    "transcriptions": [ 
      { 
        "language": "en-US" 
      } 
    ], 
    "vanityUrl": false, 
    "streamOptions": [ 
      "Default" 
    ] 
  }, 
  "location": "West US 2" 
} 
```

Опромите состояние Live Event до тех пор, пока оно не перейдет в состояние "Бегущий", что указывает на то, что теперь вы можете отправить канал RTMP. Теперь вы можете следовать тем же шагам, что и в этом учебнике, например, проверять канал предварительного просмотра и создавать живые выходы.

## <a name="transcription-delivery-and-playback"></a>Транскрипция доставки и воспроизведения

Просмотрите обзорную статью [Dynamic packaging](dynamic-packaging-overview.md#to-prepare-your-source-files-for-delivery) о том, как наш сервис использует динамическую упаковку для доставки видео, аудио и текста в различных протоколах. Когда вы публикуете свой живой эфир с помощью MPEG-DASH или HLS/CMAF, а затем вместе с видео и аудио, наша служба обеспечивает транскрибированный текст в IMSC1.1 совместимый TTML. Эта поставка упакована в фрагменты MPEG-4 Часть 30 (ISO/IEC 14496-30). При использовании доставки через HLS/TS, то текст доставляется как chunked VTT. Для воспроизведения в потоке можно использовать веб-плеер, [например, Мультиплеер Azure.](use-azure-media-player.md)  

> [!NOTE]
> При использовании Медиаплеера Azure используйте версию 2.3.3 или позже.

## <a name="known-issues"></a>Известные проблемы

Для предварительного просмотра, известны следующие проблемы с живой транскрипции:

* Функция доступна только в West US 2.
* Приложениям необходимо использовать Предварительные API, описанные в [спецификации Media Services v3 OpenAPI.](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/preview/2019-05-01-preview/streamingservice.json)
* Единственным поддерживаемым языком является английский (en-us).
* При защите контента поддерживается только шифрование конвертов AES.

## <a name="next-steps"></a>Дальнейшие действия

* [Общие сведения о службах мультимедиа](media-services-overview.md)
