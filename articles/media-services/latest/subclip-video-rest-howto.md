---
title: Подрезать видео при кодировании с помощью REST API Служб мультимедиа Azure
description: В этом разделе описывается, как подрезать видео при кодировании со службами мультимедиа Azure с помощью функции RESTFUL.
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
ms.openlocfilehash: 3557aef6213955ef77542bffafe0a2b0c374ed68
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704443"
---
# <a name="subclip-a-video-when-encoding-with-media-services---rest"></a>Подрезать видео при кодировании со службами мультимедиа — остальное

Вы можете обрезать или подрезать видео при его кодировании с помощью [задания](https://docs.microsoft.com/rest/api/media/jobs). Эта функция работает с любым [преобразованием](https://docs.microsoft.com/rest/api/media/transforms), построенным с помощью предустановок [BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) или [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset). 

Пример RESTFUL в этом разделе создает задание, которое обрезает видео по мере отправки задания кодирования. 

## <a name="prerequisites"></a>Предварительные требования

Чтобы выполнить действия, описанные в этом разделе, необходимо сделать следующее:

- [Создайте учетную запись служб мультимедиа Azure](create-account-cli-how-to.md).
- [Настройте Postman для вызовов REST API Служб мультимедиа Azure](media-rest-apis-with-postman.md).
    
    Не забудьте выполнить последний шаг, указанный в разделе [Получение токена Azure AD](media-rest-apis-with-postman.md#get-azure-ad-token). 
- Создание преобразования и выходных ресурсов. Вы можете увидеть, как создать преобразование и выходные ресурсы в [кодировке удаленного файла на основе URL-адреса и потока руководства по](stream-files-tutorial-with-rest.md) работе с видео.
- Ознакомьтесь с разделом [концепция кодирования](encoding-concept.md) .

## <a name="create-a-subclipping-job"></a>Создание задания подрезки

1. В скачанной коллекции после этого выберите **преобразования и задания** -> **создать задание с вложенной обрезки**.
    
    Запрос на **Размещение** выглядит следующим образом:
    
    ```
    https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName/jobs/:jobName?api-version={{api-version}}
    ```
1. Обновите значение переменной среды "Трансформнаме", указав имя преобразования. 
1. Перейдите на вкладку **текст** и измените "мйоутпутассет" на имя выходного ресурса.

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

    Вы увидите **ответ** с информацией о созданном и отправленном задании, а также о состоянии задания. 

## <a name="next-steps"></a>Следующие шаги

[Кодирование с помощью пользовательского преобразования](custom-preset-rest-howto.md) 
