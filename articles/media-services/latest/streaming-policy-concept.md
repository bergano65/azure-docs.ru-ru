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
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 510899e44e4ea4a90e21473ee6af546744c2be2a
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66120195"
---
# <a name="streaming-policies"></a>Политики потоковой передачи

[Политики потоковой передачи](https://docs.microsoft.com/rest/api/media/streamingpolicies) в Службах мультимедиа Azure версии 3 позволяют определять протоколы потоковой передачи и параметры шифрования для [указателей потоковой передачи](streaming-locators-concept.md). V3 службы мультимедиа предоставляют несколько готовых политик потоковой передачи, таким образом, чтобы их можно использовать непосредственно для пробной версии или рабочей среде. 

В настоящее время доступны стандартные политики потоковой передачи:<br/>Predefined_DownloadOnly, Predefined_ClearStreamingOnly, Predefined_DownloadAndClearStreaming, Predefined_ClearKey, Predefined_MultiDrmCencStreaming и Predefined_MultiDrmStreaming.

При наличии особых требований (например, если вы хотите указать разные протоколы, нужно использовать службу доставки пользовательских ключей или понадобится очистить звуковой дорожки с), можно создать настраиваемую политику потоковой передачи. 

 
> [!IMPORTANT]
> * Свойства **политик потоковой передачи** типа Datetime всегда задаются в формате UTC.
> * Следует разработать ограниченный набор политик для учетной записи Служб мультимедиа и повторно использовать их для указателей потоковой передачи каждый раз, когда требуются те же параметры. Дополнительные сведения см. в статье [Квоты и ограничения](limits-quotas-constraints.md).

## <a name="decision-tree"></a>Дерево принятия решений

Следующее дерево принятия решений поможет выбрать стандартную политику потоковой передачи для вашего сценария.

Щелкните изображение, чтобы просмотреть его полноразмерную версию.  <br/>
<a href="./media/streaming-policy/large.png" target="_blank"><img src="./media/streaming-policy/small.png"></a> 

## <a name="examples"></a>Примеры

### <a name="not-encrypted"></a>Не зашифровано

Если нужно передавать потоком вашего файла в-clear (без шифрования), набор предопределенных четкую политику потоковой передачи: для «Predefined_ClearStreamingOnly» (в .NET, можно использовать перечисления PredefinedStreamingPolicy.ClearStreamingOnly).

```csharp
StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = PredefinedStreamingPolicy.ClearStreamingOnly
    });
```

### <a name="encrypted"></a>Зашифровано 

Если нужно зашифровать содержимое с помощью шифрования конверта и CENC, установите политику Predefined_MultiDrmCencStreaming. Эта политика указывает, что для получения созданного и настроенного указателя требуются два ключа содержимого (конверт и CENC). Таким образом применяются шифрования типа конверт, PlayReady и Widevine (ключ доставляется клиенту воспроизведения на основе настроенных лицензий DRM).

```csharp
StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = "Predefined_MultiDrmCencStreaming",
        DefaultContentKeyPolicyName = contentPolicyName
    });
```

Если необходимо выполнить шифрование потока с помощью CBCS (FairPlay), используйте Predefined_MultiDrmStreaming.

## <a name="filtering-ordering-paging"></a>Фильтрации, упорядочивание, разбиение по страницам

Ознакомьтесь с разделом [Фильтрация, упорядочивание и разбиение по страницам сущностей Служб мультимедиа](entities-overview.md).

## <a name="next-steps"></a>Дальнейшие действия

* [Краткое руководство по потоковой передаче видеофайлов — .NET](stream-files-dotnet-quickstart.md)
* [Использование динамического шифрования AES-128 и службы доставки ключей](protect-with-aes128.md)
* [Использование динамического шифрования DRM и службы доставки лицензий](protect-with-drm.md)
