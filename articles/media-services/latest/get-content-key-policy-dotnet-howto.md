---
title: Получение ключа подписывания из политики с помощью служб мультимедиа Azure v3 .NET
description: В этом разделе показано, как получить ключ подписи из существующей политики с помощью пакета SDK для .NET Служб мультимедиа v3.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: d98f008e77fd56a369d298e26165ca29007c8128
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/01/2020
ms.locfileid: "89267352"
---
# <a name="get-a-signing-key-from-the-existing-policy"></a>Получение ключа подписи из существующей политики

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Один из ключевых принципов проектирования API версии 3 — сделать API более безопасным. API V3 не возвращают секреты или учетные данные при операциях **Get** или **List** . См. подробное описание этой статье. Дополнительные сведения см. в разделе [учетные записи RBAC и служб мультимедиа](rbac-overview.md) .

В примере этой статьи показано, как использовать .NET для получения ключа подписи из существующей политики. 
 
## <a name="download"></a>Скачать 

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
