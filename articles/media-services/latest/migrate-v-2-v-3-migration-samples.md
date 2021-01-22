---
title: Сравнение образцов миграции служб мультимедиа v2 и v3 | Документация Майкрософт
description: Набор примеров, которые помогут сравнить различия в коде между службами мультимедиа Azure версии 2 и 3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 1/14/2021
ms.author: inhenkel
ms.openlocfilehash: 7705443bbe810866cd5b52946b310b5b79bfa072
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/22/2021
ms.locfileid: "98690526"
---
# <a name="media-services-migration-code-sample-comparison"></a>Сравнение образца кода миграции служб мультимедиа

![логотип с руководством по миграции](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

Некоторые из наших примеров кода можно использовать для сравнения способов выполнения действий между пакетами SDK.

## <a name="samples-for-comparison"></a>Примеры для сравнения

В следующей таблице приведен список примеров для сравнения версий 2 и v3 для распространенных сценариев.

|Сценарий|API v2|API V3|
|---|---|---|
|Создайте ресурс и отправьте файл. |[Пример .NET версии 2](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L113)|[Пример .NET версии 3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L169)|
|Отправка задания|[Пример .NET версии 2](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L146)|[Пример .NET версии 3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L298)<br/><br/>Показано, как сначала создать преобразование, а затем отправить задание.|
|Опубликуйте ресурс с шифрованием AES. |1. Создание `ContentKeyAuthorizationPolicyOption`<br/>2. Создание `ContentKeyAuthorizationPolicy`<br/>3. Создание `AssetDeliveryPolicy`<br/>4. Создание `Asset` и отправка содержимого или отправка `Job` и использование `OutputAsset`<br/>5. Связывание `AssetDeliveryPolicy` с `Asset`<br/>6. Создание `ContentKey`<br/>7. присоединение `ContentKey` к `Asset`<br/>8. Создание `AccessPolicy`<br/>9. Создание `Locator`<br/><br/>[Пример .NET версии 2](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L64)|1. Создание `ContentKeyPolicy`<br/>2. Создание `Asset`<br/>3. Отправка содержимого или использование `Asset` as `JobOutput`<br/>4. Создание `StreamingLocator`<br/><br/>[Пример .NET версии 3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs#L105)|
|Получение сведений о заданиях и управление заданиями |[Управление заданиями с помощью версии 2](../previous/media-services-dotnet-manage-entities.md#get-a-job-reference) |[Управление заданиями с помощью v3](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L546)|

## <a name="next-steps"></a>Следующие шаги

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]
