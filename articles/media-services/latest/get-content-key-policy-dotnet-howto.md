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
ms.date: 04/09/2019
ms.author: juliako
ms.openlocfilehash: 49cc2b8c151053377f8f1da0792f10a06695b332
ms.sourcegitcommit: 6e32f493eb32f93f71d425497752e84763070fad
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/10/2019
ms.locfileid: "59471177"
---
# <a name="get-a-signing-key-from-the-existing-policy"></a>Получение ключа подписи из существующей политики

Один из ключевых принципов проектирования API версии 3 — сделать API более безопасным. API-интерфейсы v3 не возвращают секретные данные или учетные данные на **получить** или **списка** операций. Ключи всегда являются NULL, пустыми или исключенными из ответа. Пользователь должен вызвать метод отдельное действие для получения секретов или учетные данные. **Чтения** роли не может вызывать операции, поэтому не может вызвать операции, такие как ContentKeyPolicies.GetPolicyPropertiesWithSecrets Asset.ListContainerSas StreamingLocator.ListContentKeys,. Наличие отдельных действий позволяет при необходимости задайте более детализированных разрешений системы безопасности RBAC в пользовательскую роль.

Дополнительные сведения см. в разделе [учетные записи служб мультимедиа и RBAC](rbac-overview.md)

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
