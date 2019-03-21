---
title: Кодирование пользовательского преобразования, с помощью служб мультимедиа v3 CLI — Azure | Документация Майкрософт
description: В этом разделе показано, как использовать службы мультимедиа Azure v3 для кодирования пользовательское преобразование, с помощью интерфейса командной строки.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 03/12/2019
ms.author: juliako
ms.openlocfilehash: 7160b7c9cdb82d378870edb1750c9d9016aa2d18
ms.sourcegitcommit: 4133f375862fdbdec07b70de047d70c66ac29d50
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/15/2019
ms.locfileid: "58006855"
---
# <a name="how-to-encode-with-a-custom-transform-by-using-cli"></a>Как кодировать с помощью пользовательского преобразования с помощью интерфейса командной строки

При кодировании с помощью служб мультимедиа Azure, вы можете быстро начать работу с один из рекомендуемых встроенных стилей, на основе отраслевых рекомендаций, как показано в [потоковая передача файлов](stream-files-cli-quickstart.md#create-a-transform-for-adaptive-bitrate-encoding) быстрого запуска. Можно также создать пользовательскую предустановку для конкретных требований сценария или устройства.

## <a name="considerations"></a>Рекомендации

Создавая пользовательские предустановки, следующие соображения.

* Все значения высоты и ширины в содержимом AVC, должны быть кратна 4.
* В Azure Media Services v3 все кодировки скоростей, в битах в секунду. Это отличается от предустановки с наши API версии 2, который используется в качестве единицы килобит в секунду. Например если скорости в версии 2 было указано как 128 (килобит в секунду), в версии 3 он устанавливается для 128000 (бит/с).

## <a name="prerequisites"></a>Технические условия 

[Создание учетной записи Служб мультимедиа](create-account-cli-how-to.md). <br/>Обязательно запомните имя группы ресурсов и имя учетной записи Служб мультимедиа. 

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="define-a-custom-preset"></a>Определение пользовательской предустановки

В следующем примере определяется в тексте запроса новое преобразование. Мы определяем набор выходных данных, которые должны создаваться, если это преобразование используется. 

В этом примере мы сначала добавить к слою AacAudio для кодирования, аудио и два уровня H264Video, для кодирования видео. В слои видео мы присваивать метки, они могут использоваться в именах файлов выходных данных. Далее нам нужно, выходные данные для включения эскизов. В приведенном ниже примере мы указываем изображения в формате PNG, созданный на 50% от разрешения входного видео, а три метки времени — {25%, 50%, 75} Длина входного видео. Наконец, мы указываем формат для выходных файлов - один для видео и аудио, а другой для изображений эскизов. Так как у нас есть несколько H264Layers, нужно использовать макросы, которые создают уникальные имена на один уровень. Мы можем использовать `{Label}` или `{Bitrate}` макрос, в примере первый вариант.

Мы хотим сохранить это преобразование в файле. В этом примере мы назовите файл `customPreset.json`. 

```json
{
    "properties": {
        "description": "Basic Transform using a custom encoding preset",
        "outputs": [
            {
                "onError": "StopProcessingJob",
                "relativePriority": "Normal",
                "preset": {
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
                            "filenamePattern": "Video-{Basename}-{Label}{Extension}",
                            "outputFiles": []
                        },
                        {
                            "@odata.type": "#Microsoft.Media.PngFormat",
                            "filenamePattern": "Thumbnail-{Basename}-{Index}{Extension}"
                        }
                    ]
                }
            }
        ]
    }
}

```

## <a name="create-a-new-transform"></a>Создать новое преобразование  

В этом примере мы создадим **преобразования** , основанный на пользовательской предустановки, определенных ранее. При создании преобразования, вы должны сначала проверять, если таковая существует. Если существует преобразование, повторно. Следующие `show` командой `customTransformName` преобразования, если он существует:

```cli
az ams transform show -a amsaccount -g amsResourceGroup -n customTransformName
```

Приведенная ниже команда интерфейса командной строки создает преобразования, в зависимости от пользовательской предустановки, (определенного ранее). 

```cli
az ams transform create -a amsaccount -g amsResourceGroup -n customTransformName --description "Basic Transform using a custom encoding preset" --preset customPreset.json
```

Для служб мультимедиа применить преобразование к указанного видео или аудио необходимо отправить задание в это преобразование. Полный пример, в котором показано, как отправить задание в разделе преобразования, см. в разделе [краткое руководство: Stream видеофайлов - CLI](stream-files-cli-quickstart.md).

## <a name="see-also"></a>См. также

[Интерфейс командной строки Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
