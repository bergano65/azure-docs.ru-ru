---
title: Получите ключ подписи от политики с помощью Службы мультимедиа Azure v3 .NET
description: В этом разделе показано, как получить ключ подписи из существующей политики с помощью пакета SDK для .NET Служб мультимедиа v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/15/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 2dea262fadb61adc9e219b76f9ac048c11e650d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80065964"
---
# <a name="get-a-signing-key-from-the-existing-policy"></a>Получение ключа подписи из существующей политики

Один из ключевых принципов проектирования API версии 3 — сделать API более безопасным. aIS v3 не возвращают секреты или учетные данные в операциях **Get** или **List.** Подробнее об этом читайте в приведенном ниже: Более подробную информацию можно узнать из [аккаунтов RBAC и Media Services](rbac-overview.md)

В примере этой статьи показано, как использовать .NET для получения ключа подписи из существующей политики. 
 
## <a name="download"></a>Скачивание 

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
