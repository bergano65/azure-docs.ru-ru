---
title: Шифрование данных в состоянии покоя службы лица
titleSuffix: Azure Cognitive Services
description: Шифрование данных в состоянии покоя.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: egeaney
ms.openlocfilehash: 793c21bb2341033aa51ff8c639846e57ada4bae3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372219"
---
# <a name="face-service-encryption-of-data-at-rest"></a>Шифрование данных в состоянии покоя службы лица

Служба Face автоматически шифрует ваши данные, когда упорствует в облаке. Шифрование службы Face защищает ваши данные и помогает вам выполнять свои организационные обязательства по обеспечению безопасности и соблюдению нормативных требований.

[!INCLUDE [cognitive-services-about-encryption](../../../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Ключи, управляемые клиентами, доступны только на уровне ценообразования E0. Чтобы запросить возможность использовать клавиши, управляемые клиентом, заполните и отправьте [форму ключевого запроса, управляемую клиентом,](https://aka.ms/cogsvc-cmk)управляемую клиентом. Это займет около 3-5 рабочих дней, чтобы услышать о статусе вашего запроса. В зависимости от спроса, вы можете быть помещены в очередь и утверждены по мере поступления пространства. После одобрения использования CMK с помощью службы Face необходимо создать новый ресурс Face и выбрать E0 в качестве уровня ценообразования. После создания ресурса Face с уровнем цен E0 можно использовать Azure Key Vault для настройки управляемой идентификации.

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk.md)]

## <a name="next-steps"></a>Дальнейшие действия

* [Форма ключевого запроса, управляемая клиентом,](https://aka.ms/cogsvc-cmk)
* [Узнайте больше о Убежище ключей Azure](https://docs.microsoft.com/azure/key-vault/key-vault-overview)


