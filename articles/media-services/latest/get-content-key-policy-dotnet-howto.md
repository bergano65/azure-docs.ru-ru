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
ms.date: 12/08/2018
ms.author: juliako
ms.openlocfilehash: 882f4650c0a3d558ee06c96658b779f9f0c76f76
ms.sourcegitcommit: 3ba9bb78e35c3c3c3c8991b64282f5001fd0a67b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/15/2019
ms.locfileid: "54322487"
---
# <a name="get-a-signing-key-from-the-existing-policy"></a>Получение ключа подписи из существующей политики

Один из ключевых принципов проектирования API версии 3 — сделать API более безопасным. API версии 3 не возвращает секреты или учетные данные в **Get** или в операции **List**. Ключи всегда являются NULL, пустыми или исключенными из ответа. Необходимо вызвать отдельный метод действий для получения секретов или учетных данных. Отдельные действия позволяют устанавливать разные разрешения безопасности RBAC в случае, если некоторые API извлекают или отображают секреты, в то время как другие API этого не делают. Сведения о том, как управлять доступом с помощью RBAC, см. в разделе [Управление доступом с помощью RBAC и REST API](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest).

Примеры включений 

* невыполнение возврата значений ContentKey в запросе Get StreamingLocator, 
* невыполнение возврата ключей ограничения в запросе Get ContentKeyPolicy, 
* невыполнение возврата части строки запроса URL-адреса (чтобы удалить подпись) URL-адресов ввода заданий HTTP.

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

## <a name="next-steps"></a>Дополнительная информация

[Проектирование системы для защиты содержимого с несколькими подсистемами DRM и управлением доступом](design-multi-drm-system-with-access-control.md) 
