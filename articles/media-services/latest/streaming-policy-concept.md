---
title: Политики потоковой передачи в Службах мультимедиа Azure | Документация Майкрософт
description: В этой статье приведены общие сведения о политиках потоковой передачи и их использовании в Службах мультимедиа Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/28/2019
ms.author: juliako
ms.openlocfilehash: a813c77e81e51bfe13e75ed6c8d0e24b4d0fa645
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "66392927"
---
# <a name="streaming-policies"></a>Политики потоковой передачи

[Политики потоковой передачи](https://docs.microsoft.com/rest/api/media/streamingpolicies) в Службах мультимедиа Azure версии 3 позволяют определять протоколы потоковой передачи и параметры шифрования для [указателей потоковой передачи](streaming-locators-concept.md). Media Services v3 предоставляет некоторые предопределенные политики потоковой передачи, так что вы можете использовать их непосредственно для пробной версии или производства. 

Доступные в настоящее время предопределенные политики потоковой передачи:<br/>
* 'Predefined_DownloadOnly'
* 'Predefined_ClearStreamingOnly'
* 'Predefined_DownloadAndClearStreaming'
* 'Predefined_ClearKey'
* 'Predefined_MultiDrmCencStreaming' 
* 'Predefined_MultiDrmStreaming'

Следующее "Дерево решений" поможет вам выбрать предопределенную политику потоковой передачи для вашего сценария.

> [!IMPORTANT]
> * Свойства **политик потоковой передачи** типа Datetime всегда задаются в формате UTC.
> * Следует разработать ограниченный набор политик для учетной записи Служб мультимедиа и повторно использовать их для указателей потоковой передачи каждый раз, когда требуются те же параметры. Дополнительные сведения см. в статье [Квоты и ограничения](limits-quotas-constraints.md).

## <a name="decision-tree"></a>Дерево решений

Щелкните изображение, чтобы просмотреть его полноразмерную версию.  

<a href="./media/streaming-policy/large.png" target="_blank"><img src="./media/streaming-policy/large.png"></a> 

При шифровании содержимого необходимо создать [политику ключей содержимого,](content-key-policy-concept.md) **политика ключей содержимого** не требуется для четкой потоковой передачи или загрузки. 

Если у вас есть особые требования (например, если вы хотите указать различные протоколы, необходимо использовать пользовательский сервис доставки ключей или использовать четкую звуковую дорожку), можно [создать](https://docs.microsoft.com/rest/api/media/streamingpolicies/create) пользовательскую политику потоковой передачи. 

## <a name="get-a-streaming-policy-definition"></a>Получить определение политики потоковой передачи  

Если вы хотите увидеть определение политики потоковой передачи, используйте [Get](https://docs.microsoft.com/rest/api/media/streamingpolicies/get) и укажите имя политики. Пример:

### <a name="rest"></a>REST

Запрос:

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Media/mediaServices/contosomedia/streamingPolicies/clearStreamingPolicy?api-version=2018-07-01
```

Ответ:

```
{
  "name": "clearStreamingPolicy",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Media/mediaservices/contosomedia/streamingPolicies/clearStreamingPolicy",
  "type": "Microsoft.Media/mediaservices/streamingPolicies",
  "properties": {
    "created": "2018-08-08T18:29:30.8501486Z",
    "noEncryption": {
      "enabledProtocols": {
        "download": true,
        "dash": true,
        "hls": true,
        "smoothStreaming": true
      }
    }
  }
}
```

## <a name="filtering-ordering-paging"></a>Фильтрации, упорядочивание, разбиение по страницам

Ознакомьтесь с разделом [Фильтрация, упорядочивание и разбиение по страницам сущностей Служб мультимедиа](entities-overview.md).

## <a name="next-steps"></a>Дальнейшие действия

* [Краткое руководство по потоковой передаче видеофайлов — .NET](stream-files-dotnet-quickstart.md)
* [Использование динамического шифрования AES-128 и службы доставки ключей](protect-with-aes128.md)
* [Использование службы динамического шифрования DRM и доставки лицензий](protect-with-drm.md)
