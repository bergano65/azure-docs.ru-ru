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
ms.date: 02/03/2019
ms.author: juliako
ms.openlocfilehash: 10600d8f3ff4e08b8d90f28ec15d3cb0c56bcae0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "61230902"
---
# <a name="streaming-policies"></a>Политики потоковой передачи

[Политики потоковой передачи](https://docs.microsoft.com/rest/api/media/streamingpolicies) в Службах мультимедиа Azure версии 3 позволяют определять протоколы потоковой передачи и параметры шифрования для [указателей потоковой передачи](streaming-locators-concept.md). Можно использовать одну из предопределенных политик потоковой передачи или создать настраиваемую политику. Сейчас доступны следующие предопределенные политики потоковой передачи: Predefined_DownloadOnly, Predefined_ClearStreamingOnly, Predefined_DownloadAndClearStreaming, Predefined_ClearKey, Predefined_MultiDrmCencStreaming и Predefined_MultiDrmStreaming.

> [!IMPORTANT]
> * Свойства **политик потоковой передачи** типа Datetime всегда задаются в формате UTC.
> * Следует разработать ограниченный набор политик для учетной записи Служб мультимедиа и повторно использовать их для указателей потоковой передачи каждый раз, когда требуются те же параметры. 

## <a name="examples"></a>Примеры

### <a name="not-encrypted"></a>Не зашифровано

Если нужно передать необработанный файл (без шифрования), установите предопределенную политику потоковой передачи без шифрования: Predefined_ClearStreamingOnly (в .NET можно использовать PredefinedStreamingPolicy.ClearStreamingOnly).

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

### <a name="encrypted"></a>Зашифрованный 

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
