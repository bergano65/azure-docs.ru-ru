---
title: Получение ключа подписи из существующей политики с помощью пакета SDK для .NET Служб мультимедиа v3 в Azure | Документация Майкрософт
description: В этом разделе показано, как получить ключ подписи из существующей политики с помощью пакета SDK для .NET Служб мультимедиа v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: seodec18
ms.date: 04/15/2019
ms.author: juliako
ms.openlocfilehash: 58b6f49f4bbbd93fefb9b616f92baf7ef30f7deb
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2019
ms.locfileid: "59615835"
---
# <a name="get-a-signing-key-from-the-existing-policy"></a>Получение ключа подписи из существующей политики

Один из ключевых принципов проектирования API версии 3 — сделать API более безопасным. API-интерфейсы v3 не возвращают секретные данные или учетные данные на **получить** или **списка** операций. См. сведения: Дополнительные сведения см. в разделе [учетные записи служб мультимедиа и RBAC](rbac-overview.md)

В примере этой статьи показано, как использовать .NET для получения ключа подписи из существующей политики. 
 
## <a name="download"></a>Download (Скачать) 

Клонируйте репозиторий GitHub, содержащий пример .NET, на компьютер с помощью следующей команды.  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```
 
Пример ContentKeyPolicy с секретами расположен в папке [EncryptWithDRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM).

## <a name="get-contentkeypolicy-with-secrets"></a>Получение ContentKeyPolicy с секретами 

Чтобы получить ключ, используйте **GetPolicyPropertiesWithSecretsAsync**, как показано в примере, приведенном ниже.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="next-steps"></a>Дальнейшие действия

[Проектирование системы для защиты содержимого с несколькими подсистемами DRM и управлением доступом](design-multi-drm-system-with-access-control.md) 
