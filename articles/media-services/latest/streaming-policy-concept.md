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
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/30/2019
ms.locfileid: "66392927"
---
# <a name="streaming-policies"></a>Политики потоковой передачи

[Политики потоковой передачи](https://docs.microsoft.com/rest/api/media/streamingpolicies) в Службах мультимедиа Azure версии 3 позволяют определять протоколы потоковой передачи и параметры шифрования для [указателей потоковой передачи](streaming-locators-concept.md). V3 службы мультимедиа предоставляют несколько готовых политик потоковой передачи, таким образом, чтобы их можно использовать непосредственно для пробной версии или рабочей среде. 

В настоящее время доступны стандартные политики потоковой передачи:<br/>
* «Predefined_DownloadOnly»
* «Predefined_ClearStreamingOnly»
* «Predefined_DownloadAndClearStreaming»
* «Predefined_ClearKey»
* «Predefined_MultiDrmCencStreaming» 
* «Predefined_MultiDrmStreaming»

Следующее дерево принятия решений «» вы сможете выбрать стандартную политику потоковой передачи для вашего сценария.

> [!IMPORTANT]
> * Свойства **политик потоковой передачи** типа Datetime всегда задаются в формате UTC.
> * Следует разработать ограниченный набор политик для учетной записи Служб мультимедиа и повторно использовать их для указателей потоковой передачи каждый раз, когда требуются те же параметры. Дополнительные сведения см. в статье [Квоты и ограничения](limits-quotas-constraints.md).

## <a name="decision-tree"></a>Дерево принятия решений

Щелкните изображение, чтобы просмотреть его полноразмерную версию.  

<a href="./media/streaming-policy/large.png" target="_blank"><img src="./media/streaming-policy/large.png"></a> 

Если шифрование содержимого, необходимо создать [политике ключ содержимого](content-key-policy-concept.md), **политике ключ содержимого** не требуется для очистки потоковой передачи или загрузки. 

Если у вас есть особые требования (например, если вы хотите указать разные протоколы, нужно использовать службу доставки пользовательских ключей или понадобится очистить звуковой дорожки с), вы можете [создать](https://docs.microsoft.com/rest/api/media/streamingpolicies/create) настраиваемую политику потоковой передачи. 

## <a name="get-a-streaming-policy-definition"></a>Получить определение политики потоковой передачи  

Если вы хотите просмотреть определение политики потоковой передачи, используйте [получить](https://docs.microsoft.com/rest/api/media/streamingpolicies/get) и укажите имя политики. Пример:

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
* [Использование динамического шифрования DRM и службы доставки лицензий](protect-with-drm.md)
