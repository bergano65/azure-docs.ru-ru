---
title: Закодировать пользовательские преобразования с помощью служб ы сми v3 Azure CLI (ru) Документы Майкрософт
description: В этой теме показано, как использовать Azure Media Services v3 для кодирования пользовательского преобразования с помощью Azure CLI.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 05/14/2019
ms.author: juliako
ms.openlocfilehash: 7c1b446ccf04199449f012e738f6a03660735f50
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/29/2020
ms.locfileid: "80382959"
---
# <a name="how-to-encode-with-a-custom-transform---azure-cli"></a>Как закодировать с помощью пользовательского преобразования - Azure CLI

При кодировании с помощью медиа-служб Azure можно быстро начать работу с одной из рекомендуемых встроенных пресетов, основываясь на рекомендациях отрасли, как показано в быстром запуске [потоков.](stream-files-cli-quickstart.md#create-a-transform-for-adaptive-bitrate-encoding) Можно также создать пользовательский предустановленный набор для таргетинга на конкретный сценарий или требования к устройству.

## <a name="considerations"></a>Рекомендации

При создании пользовательских пресетов применяются следующие соображения:

* Все значения для высоты и ширины содержимого AVC должны быть кратными 4.
* В Azure Media Services v3 все бираты кодирования кодируются по биту в секунду. Это отличается от пресетов с нашими v2 AIS, которые использовали килобиты в секунду в качестве устройства. Например, если битрат в v2 был указан как 128 (килобит в секунду), то в v3 он будет установлен до 128000 (бит в секунду).

## <a name="prerequisites"></a>Предварительные требования

[Создание учетной записи Служб мультимедиа](create-account-cli-how-to.md).

Обязательно запомните имя группы ресурсов и имя учетной записи Служб мультимедиа.

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="define-a-custom-preset"></a>Определите пользовательский предустановленный

Следующий пример определяет тело запроса нового Преобразования. Мы определяем набор выходов, которые мы хотим быть созданы, когда этот преобразование используется.

В этом примере мы сначала добавляем слой AacAudio для кодирования звука и два слоя H264Video для кодирования видео. В слоях видео мы назначаем метки так, чтобы они могли использоваться в именах выходных файлов. Далее, мы хотим, чтобы выход также включал эскизы. В приведенном ниже примере мы указывать изображения в формате PNG, генерируемые при 50% разрешения входном видео, а в три временных метки - 25%, 50%, 75" длины вхоженного видео. Наконец, мы указываем формат для выходных файлов - один для видео-аудио, а другой для эскизов. Так как у нас есть несколько H264Layers, мы должны использовать макросы, которые производят уникальные имена на слой. Мы можем использовать `{Label}` `{Bitrate}` или макрос, пример показывает первый.

Мы сохраним это преобразование в файле. В этом примере мы `customPreset.json`назовем файл .

```json
{
    "@odata.type": "#Microsoft.Media.StandardEncoderPreset",
    "codecs": [
        {
            "@odata.type": "#Microsoft.Media.AacAudio",
            "channels": 2,
            "samplingRate": 48000,
            "bitrate": 128000,
            "profile": "AacLc"
        },
        {
            "@odata.type": "#Microsoft.Media.H264Video",
            "keyFrameInterval": "PT2S",
            "stretchMode": "AutoSize",
            "sceneChangeDetection": false,
            "complexity": "Balanced",
            "layers": [
                {
                    "width": "1280",
                    "height": "720",
                    "label": "HD",
                    "bitrate": 3400000,
                    "maxBitrate": 3400000,
                    "bFrames": 3,
                    "slices": 0,
                    "adaptiveBFrame": true,
                    "profile": "Auto",
                    "level": "auto",
                    "bufferWindow": "PT5S",
                    "referenceFrames": 3,
                    "entropyMode": "Cabac"
                },
                {
                    "width": "640",
                    "height": "360",
                    "label": "SD",
                    "bitrate": 1000000,
                    "maxBitrate": 1000000,
                    "bFrames": 3,
                    "slices": 0,
                    "adaptiveBFrame": true,
                    "profile": "Auto",
                    "level": "auto",
                    "bufferWindow": "PT5S",
                    "referenceFrames": 3,
                    "entropyMode": "Cabac"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Media.PngImage",
            "stretchMode": "AutoSize",
            "start": "25%",
            "step": "25%",
            "range": "80%",
            "layers": [
                {
                    "width": "50%",
                    "height": "50%"
                }
            ]
        }
    ],
    "formats": [
        {
            "@odata.type": "#Microsoft.Media.Mp4Format",
            "filenamePattern": "Video-{Basename}-{Label}-{Bitrate}{Extension}",
            "outputFiles": []
        },
        {
            "@odata.type": "#Microsoft.Media.PngFormat",
            "filenamePattern": "Thumbnail-{Basename}-{Index}{Extension}"
        }
    ]
}
```

## <a name="create-a-new-transform"></a>Создание нового преобразования  

В этом примере **Transform** мы создаем Преобразование, основанное на пользовательском предустановленном наборе, который мы определили ранее. При создании Преобразования следует сначала проверить, существует ли она уже. Если Преобразование существует, повторно используйте его повторно. Следующая `show` команда `customTransformName` возвращает преобразование, если оно существует:

```azurecli-interactive
az ams transform show -a amsaccount -g amsResourceGroup -n customTransformName
```

Следующая команда Azure CLI создает Преобразование на основе пользовательского предустановленного (определяемого ранее).

```azurecli-interactive
az ams transform create -a amsaccount -g amsResourceGroup -n customTransformName --description "Basic Transform using a custom encoding preset" --preset customPreset.json
```

Для того, чтобы службы мультимедиа применили Преобразование к указанному видео или аудио, необходимо отправить вакансию в соответствии с этим преобразованием. Для полного примера, который показывает, как отправить задание под преобразование, [см.](stream-files-cli-quickstart.md)

## <a name="see-also"></a>См. также

[Лазурный CLI](/cli/azure/ams)
