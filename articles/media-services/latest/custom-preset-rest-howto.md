---
title: Закодировать пользовательские преобразования с помощью медиа-сервисов v3 REST - Azure Документы Майкрософт
description: В этой теме показано, как использовать Azure Media Services v3 для кодирования пользовательского преобразования с помощью REST.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "65761799"
---
# <a name="how-to-encode-with-a-custom-transform---rest"></a>Как закодировать с помощью пользовательского преобразования - ВЕДОМОСТИ

При кодировании с помощью Медиа-сервисов Azure можно быстро начать работу с одной из рекомендуемых встроенных пресетов, основываясь на рекомендациях отрасли, как показано в учебнике [по стриминговым](stream-files-tutorial-with-rest.md#create-a-transform) файлам. Можно также создать пользовательский предустановленный набор для таргетинга на конкретный сценарий или требования к устройству.

## <a name="considerations"></a>Рекомендации

При создании пользовательских пресетов применяются следующие соображения:

* Все значения для высоты и ширины содержимого AVC должны быть кратными 4.
* В Azure Media Services v3 все бираты кодирования кодируются по биту в секунду. Это отличается от пресетов с нашими v2 AIS, которые использовали килобиты в секунду в качестве устройства. Например, если битрат в v2 был указан как 128 (килобит в секунду), то в v3 он будет установлен до 128000 (бит в секунду).

## <a name="prerequisites"></a>Предварительные требования 

- [Создание учетной записи Служб мультимедиа](create-account-cli-how-to.md). <br/>Обязательно запомните имя группы ресурсов и имя учетной записи Служб мультимедиа. 
- [Настройте Postman для вызовов REST API Служб мультимедиа Azure](media-rest-apis-with-postman.md).<br/>Не забудьте выполнить последний шаг, указанный в разделе [Получение токена Azure AD](media-rest-apis-with-postman.md#get-azure-ad-token). 

## <a name="define-a-custom-preset"></a>Определите пользовательский предустановленный

Следующий пример определяет тело запроса нового Преобразования. Мы определяем набор выходов, которые мы хотим быть созданы, когда этот преобразование используется. 

В этом примере мы сначала добавляем слой AacAudio для кодирования звука и два слоя H264Video для кодирования видео. В слоях видео мы назначаем метки так, чтобы они могли использоваться в именах выходных файлов. Далее, мы хотим, чтобы выход также включал эскизы. В приведенном ниже примере мы указывать изображения в формате PNG, генерируемые при 50% разрешения входном видео, а в три временных метки - 25%, 50%, 75" длины вхоженного видео. Наконец, мы указываем формат для выходных файлов - один для видео-аудио, а другой для эскизов. Так как у нас есть несколько H264Layers, мы должны использовать макросы, которые производят уникальные имена на слой. Мы можем использовать `{Label}` `{Bitrate}` или макрос, пример показывает первый.

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

## <a name="create-a-new-transform"></a>Создание нового преобразования  

В этом примере **Transform** мы создаем Преобразование, основанное на пользовательском предустановленном наборе, который мы определили ранее. При создании Преобразования следует сначала использовать [Get,](https://docs.microsoft.com/rest/api/media/transforms/get) чтобы проверить, существует ли она уже. Если Преобразование существует, повторно используйте его повторно. 

В коллекции Почтальона, которую вы скачали, выберите **Преобразования и Рабочие места**->**Создать или обновить преобразование**.

Метод HTTP-запроса **PUT** аналогичен следующему:

```
PUT https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName?api-version={{api-version}}
```

Выберите вкладку **Body** и замените тело кодом Json, который вы [определили ранее.](#define-a-custom-preset) Для того, чтобы службы мультимедиа применили Преобразование к указанному видео или аудио, необходимо отправить вакансию в соответствии с этим преобразованием.

Выберите **Отправить**. 

Для того, чтобы службы мультимедиа применили Преобразование к указанному видео или аудио, необходимо отправить вакансию в соответствии с этим преобразованием. Для полного примера, который показывает, как отправить задание под преобразование, [см.](stream-files-tutorial-with-rest.md)

## <a name="next-steps"></a>Дальнейшие действия

Посмотреть [другие операции REST](https://docs.microsoft.com/rest/api/media/)
