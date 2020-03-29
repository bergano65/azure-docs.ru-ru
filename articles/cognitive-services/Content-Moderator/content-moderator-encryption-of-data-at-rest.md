---
title: Шифрование данных модератором содержимого в состоянии покоя
titleSuffix: Azure Cognitive Services
description: Шифрование данных модератором содержимого в состоянии покоя.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: b41d822791f61fce274f628b87c478c3a986f4c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372164"
---
# <a name="content-moderator-encryption-of-data-at-rest"></a>Шифрование данных модератором содержимого в состоянии покоя

Модератор содержимого автоматически шифрует ваши данные, когда они сохраняются в облаке, помогая достичь ваших целей организационной безопасности и соответствия требованиям.

[!INCLUDE [cognitive-services-about-encryption](../../../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Ключи, управляемые клиентами, доступны только на уровне ценообразования E0. Чтобы запросить возможность использовать клавиши, управляемые клиентом, заполните и отправьте [форму ключевого запроса, управляемую клиентом, управляемую клиентом.](https://aka.ms/cogsvc-cmk) Это займет около 3-5 рабочих дней, чтобы услышать о статусе вашего запроса. В зависимости от спроса, вы можете быть помещены в очередь и утверждены по мере поступления пространства. После утверждения для использования CMK с помощью службы модератора контента, вам нужно будет создать новый ресурс модератора контента и выбрать E0 в качестве уровня ценообразования. После создания ресурса «Модератор содержимого» с уровнем ценообразования E0 можно использовать Azure Key Vault для настройки управляемой идентификации.

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk.md)]

## <a name="enable-data-encryption-for-your-content-moderator-team"></a>Включить шифрование данных для вашей группы модераторов содержимого

Для обеспечения шифрования данных для [Quickstart: Try Content Moderator on the web](quick-start.md#create-a-review-team)вашей группы по обзору модератора контента см.  

> [!NOTE]
> Вам нужно будет предоставить _идентификатор ресурсов_ с уровнем ценообразования Модератора E0.


## <a name="next-steps"></a>Дальнейшие действия

* [Форма ключевого запроса Модератора Контента, управляемого клиентом](https://aka.ms/cogsvc-cmk)
* [Узнайте больше о Убежище ключей Azure](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
