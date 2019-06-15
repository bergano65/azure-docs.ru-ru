---
title: Кодирование пользовательского преобразования, с помощью служб мультимедиа v3 REST в Azure | Документация Майкрософт
description: В этом разделе показано, как использовать службы мультимедиа Azure v3 для кодирования пользовательское преобразование, с помощью REST.
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
ms.openlocfilehash: 30e22cb786e5dc2a667fe41ca8edf398cf0b7613
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65761799"
---
# <a name="how-to-encode-with-a-custom-transform---rest"></a>Как кодировать с помощью пользовательского преобразования — REST

При кодировании с помощью служб мультимедиа Azure, вы можете быстро начать работу с один из рекомендуемых встроенных стилей, на основе отраслевых рекомендаций, как показано в [потоковая передача файлов](stream-files-tutorial-with-rest.md#create-a-transform) руководства. Можно также создать пользовательскую предустановку для конкретных требований сценария или устройства.

## <a name="considerations"></a>Рекомендации

Создавая пользовательские предустановки, следующие соображения.

* Все значения высоты и ширины в содержимом AVC, должны быть кратна 4.
* В Azure Media Services v3 все кодировки скоростей, в битах в секунду. Это отличается от предустановки с наши API версии 2, который используется в качестве единицы килобит в секунду. Например если скорости в версии 2 было указано как 128 (килобит в секунду), в версии 3 он устанавливается для 128000 (бит/с).

## <a name="prerequisites"></a>Технические условия 

- [Создание учетной записи Служб мультимедиа](create-account-cli-how-to.md). <br/>Обязательно запомните имя группы ресурсов и имя учетной записи Служб мультимедиа. 
- [Настройте Postman для вызовов REST API Служб мультимедиа Azure](media-rest-apis-with-postman.md).<br/>Не забудьте выполнить последний шаг, указанный в разделе [Получение токена Azure AD](media-rest-apis-with-postman.md#get-azure-ad-token). 

## <a name="define-a-custom-preset"></a>Определение пользовательской предустановки

В следующем примере определяется в тексте запроса новое преобразование. Мы определяем набор выходных данных, которые должны создаваться, если это преобразование используется. 

В этом примере мы сначала добавить к слою AacAudio для кодирования, аудио и два уровня H264Video, для кодирования видео. В слои видео мы присваивать метки, они могут использоваться в именах файлов выходных данных. Далее нам нужно, выходные данные для включения эскизов. В приведенном ниже примере мы указываем изображения в формате PNG, созданный на 50% от разрешения входного видео, а три метки времени — {25%, 50%, 75} Длина входного видео. Наконец, мы указываем формат для выходных файлов - один для видео и аудио, а другой для изображений эскизов. Так как у нас есть несколько H264Layers, нужно использовать макросы, которые создают уникальные имена на один уровень. Мы можем использовать `{Label}` или `{Bitrate}` макрос, в примере первый вариант.

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
                            "filenamePattern": "Video-{Basename}-{Label}-{Bitrate}{Extension}",
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

В этом примере мы создадим **преобразования** , основанный на пользовательской предустановки, определенных ранее. При создании преобразования, необходимо воспользоваться [получить](https://docs.microsoft.com/rest/api/media/transforms/get) для проверки, если он уже существует. Если существует преобразование, повторно. 

В коллекцию Postman, которую вы скачали, выберите **преобразует и заданий**->**Create или Update преобразования**.

Метод HTTP-запроса **PUT** аналогичен следующему:

```
PUT https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName?api-version={{api-version}}
```

Выберите **текст** вкладка и заменить текст с JSON-файл кода, которые [определенный ранее](#define-a-custom-preset). Для служб мультимедиа применить преобразование к указанного видео или аудио необходимо отправить задание в это преобразование.

Нажмите кнопку **Отправить**. 

Для служб мультимедиа применить преобразование к указанного видео или аудио необходимо отправить задание в это преобразование. Полный пример, в котором показано, как отправить задание в разделе преобразования, см. в разделе [руководства: Stream видеофайлов - REST](stream-files-tutorial-with-rest.md).

## <a name="next-steps"></a>Дальнейшие действия

См. в разделе [другие операции REST](https://docs.microsoft.com/rest/api/media/)
