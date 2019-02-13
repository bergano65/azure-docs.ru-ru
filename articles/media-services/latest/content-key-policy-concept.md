---
title: Политики ключей содержимого в Службах мультимедиа — Azure | Документация Майкрософт
description: В этой статье приведены общие сведения о политиках ключей содержимого и их использовании в Службах мультимедиа Azure.
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
ms.custom: seodec18
ms.openlocfilehash: d9e86c45d535862e0c3d02b3f331bc40ebb7f6c7
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/05/2019
ms.locfileid: "55745127"
---
# <a name="content-key-policies"></a>Политики ключа содержимого

а также доставлять в режиме реального времени и по требованию содержимое, зашифрованное динамически с помощью Advanced Encryption Standard (AES-128) или трех основных систем управления цифровыми правами (DRM): Microsoft PlayReady, Google Widevine и Apple FairPlay. Они также обеспечивают службу доставки ключей AES и лицензий DRM (PlayReady, Widevine и FairPlay) авторизованным клиентам.

Чтобы задать параметры шифрования потока, необходимо создать [политику ключей содержимого](https://docs.microsoft.com/rest/api/media/contentkeypolicies) и связать ее с **указателем потоковой передачи**. **Политика ключей содержимого** позволяет указать, каким образом ключ содержимого доставляется конечным клиентам через компонент доставки ключей Служб мультимедиа. Можно разрешить Службам мультимедиа автоматически создать ключ содержимого. Как правило, будет использоваться долговременной ключ и проверяться наличие операции Get в политике. Чтобы получить ключ, необходимо вызвать отдельный метод действий для получения секретов или учетных данных. Это показано в примере ниже.

**Политики ключей содержимого** являются обновляемыми. Например, может потребоваться обновить политику, если необходимо сменить ключи. Или можно обновить первичный ключ проверки и список альтернативных ключей проверки в существующей политике. Обновление и получение обновленной политики кэшей доставки ключей может занять до 15 минут. 

> [!IMPORTANT]
> * Свойства **политик ключей содержимого** типа Datetime всегда задаются в формате UTC.
> * Следует разработать ограниченный набор политик для учетной записи Служб мультимедиа и повторно использовать их для указателей потоковой передачи каждый раз, когда требуются те же параметры. 

## <a name="example"></a>Пример

Чтобы получить ключ, используйте **GetPolicyPropertiesWithSecretsAsync**, как показано в примере, приведенном ниже.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="filtering-ordering-paging"></a>Фильтрации, упорядочивание, разбиение по страницам

Ознакомьтесь с разделом [Фильтрация, упорядочивание и разбиение по страницам сущностей Служб мультимедиа](entities-overview.md).

## <a name="next-steps"></a>Дополнительная информация

* [Использование динамического шифрования AES-128 и службы доставки ключей](protect-with-aes128.md)
* [Использование динамического шифрования DRM и службы доставки лицензий](protect-with-drm.md)
* [EncodeHTTPAndPublishAESEncrypted](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/EncodeHTTPAndPublishAESEncrypted)
