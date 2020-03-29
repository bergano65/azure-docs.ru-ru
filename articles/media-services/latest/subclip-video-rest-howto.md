---
title: Subclip видео при кодировании с Azure Медиа Услуги REST
description: В этой теме описывается, как подстегивать видео при кодировании с помощью медиа-служб Azure с помощью REST
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
ms.openlocfilehash: c39aded55fe36cb130459a4f6f119f872b1adbc4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76514329"
---
# <a name="subclip-a-video-when-encoding-with-media-services---rest"></a>Subclip видео при кодировании со средствами массовой информации - ВЕДОМОСТИ

Вы можете обрезать или подстричь видео при кодировании его с помощью [работы.](https://docs.microsoft.com/rest/api/media/jobs) Эта функциональность работает [Transform](https://docs.microsoft.com/rest/api/media/transforms) с любым преобразованием, который построен с использованием либо presets [builtInStandardset,](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) либо пресетов [StandardEnCoderPreset.](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) 

Пример REST в этой теме создает задание, которое обрезает видео при отправке задания кодирования. 

## <a name="prerequisites"></a>Предварительные требования

Чтобы выполнить действия, описанные в этом разделе, необходимо сделать следующее:

- [Создайте учетную запись Медиа-службы Azure.](create-account-cli-how-to.md)
- [Настройте Postman для вызовов REST API Служб мультимедиа Azure](media-rest-apis-with-postman.md).
    
    Не забудьте выполнить последний шаг, указанный в разделе [Получение токена Azure AD](media-rest-apis-with-postman.md#get-azure-ad-token). 
- Создайте трансформатор и выходные активы. Вы можете увидеть, как создать Преобразование и выходные активы в [Кодировании удаленного файла на основе URL и потокового видео - REST](stream-files-tutorial-with-rest.md) учебник.
- Просмотрите тему [концепции кодирования.](encoding-concept.md)

## <a name="create-a-subclipping-job"></a>Создать задание подсечения

1. В коллекции Postman, которую вы скачали, выберите **Преобразования и задания** -> **Создать работу с помощью Sub Clipping.**
    
    Запрос **PUT** выглядит следующим образом:
    
    ```
    https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName/jobs/:jobName?api-version={{api-version}}
    ```
1. Обновите значение переменной среды "transformName" с помощью имени преобразования. 
1. Выберите вкладку **Body** и обновите "myOutputAsset" с вашим выходным именем актива.

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

    Вы видите **ответ** с информацией о работе, которая была создана и представлена, и статусе задания. 

## <a name="next-steps"></a>Дальнейшие действия

[Как закодировать с помощью пользовательского преобразования](custom-preset-rest-howto.md) 
