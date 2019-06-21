---
title: Субклип видео при кодировании с помощью API REST служб мультимедиа Azure
description: В этом разделе описывается субклипа видео при кодировании с помощью служб мультимедиа Azure с помощью REST
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
ms.date: 06/10/2019
ms.author: juliako
ms.openlocfilehash: df8c8a4040b4aae4379b4bfe0e9a16337588dd1b
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67304928"
---
# <a name="subclip-a-video-when-encoding-with-media-services---rest"></a>Субклип видео при кодировании с помощью служб мультимедиа — REST

Можно обрезать или субклипа видео при кодировании с помощью [задания](https://docs.microsoft.com/rest/api/media/jobs). Эта функция работает с любым [преобразования](https://docs.microsoft.com/rest/api/media/transforms) , построенный с помощью [BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) предустановки, или [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) предустановок. 

В примере REST в этом разделе создается задание, которое урезает видео, так как он отправляет задание кодирования. 

## <a name="prerequisites"></a>Технические условия

Чтобы выполнить действия, описанные в этом разделе, необходимо сделать следующее:

- [Создание учетной записи служб мультимедиа Azure](create-account-cli-how-to.md).
- [Настройте Postman для вызовов REST API Служб мультимедиа Azure](media-rest-apis-with-postman.md).
    
    Не забудьте выполнить последний шаг, указанный в разделе [Получение токена Azure AD](media-rest-apis-with-postman.md#get-azure-ad-token). 
- Создание преобразования и выходные данные средства. Можно узнать, как создать преобразование и выходные данные средств в [кодирование удаленного файла по URL-адрес и потоковая передача видео - REST](stream-files-tutorial-with-rest.md) руководства.
- Просмотрите [концепция кодировка](encoding-concept.md) раздела.

## <a name="create-a-subclipping-job"></a>Создание задания подклипов

1. В коллекцию Postman, которую вы скачали, выберите **преобразует и заданий** -> **создать задание с обрезки Sub**.
    
    **ПОМЕСТИТЬ** запрос выглядит следующим образом:
    
    ```
    https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName/jobs/:jobName?api-version={{api-version}}
    ```
1. Обновите значение переменной среды «Имя_преобразования» именем центра преобразования. 
1. Выберите **текст** вкладку и замените «myOutputAsset» выходные данные имя ресурса-контейнера.

    ```
    {
      "properties": {
        "description": "A Job with transform cb9599fb-03b3-40eb-a2ff-7ea909f53735 and single clip.",
       
        "input": {
          "@odata.type": "#Microsoft.Media.JobInputHttp",
          "baseUri": "https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/",
          "files": [
                "Ignite-short.mp4"
            ],
          "start": {
            "@odata.type": "#Microsoft.Media.AbsoluteClipTime",
            "time": "PT10S"
          },
          "end": {
            "@odata.type": "#Microsoft.Media.AbsoluteClipTime",
            "time": "PT40S"
          }
        },
      
        "outputs": [
          {
            "@odata.type": "#Microsoft.Media.JobOutputAsset",
            "assetName": "myOutputAsset"
          }
        ],
        "priority": "Normal"
      }
    }
    ```
1. Нажмите кнопку **Отправить**.

    Вы увидите **ответа** с информации о задании, который был создан и отправлен и состояние задания. 

## <a name="next-steps"></a>Дальнейшие действия

[Как кодировать с помощью пользовательского преобразования](custom-preset-rest-howto.md) 
