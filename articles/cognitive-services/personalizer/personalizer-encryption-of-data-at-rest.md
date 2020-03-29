---
title: Сервис-шифрование данных в состоянии покоя
titleSuffix: Azure Cognitive Services
description: Сервис-шифрование данных в состоянии покоя.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: egeaney
ms.openlocfilehash: 63526454bb366b214c27bddce24ed9ebc09556b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80071092"
---
# <a name="personalizer-service-encryption-of-data-at-rest"></a>Сервис-шифрование данных в состоянии покоя

Служба Personalizer автоматически шифрует ваши данные, когда упорствует в облаке. Шифрование службы Personalizer защищает ваши данные и помогает вам выполнять свои организационные обязательства по обеспечению безопасности и соблюдению нормативных требований.

[!INCLUDE [cognitive-services-about-encryption](../../../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Ключи, управляемые клиентами, доступны только на уровне ценообразования E0. Чтобы запросить возможность использовать клавиши, управляемые клиентом, заполните и отправьте [форму ключевого запроса Personalizer Service Customer-Managed.](https://aka.ms/cogsvc-cmk) Это займет около 3-5 рабочих дней, чтобы услышать о статусе вашего запроса. В зависимости от спроса, вы можете быть помещены в очередь и утверждены по мере поступления пространства. После одобрения использования CMK с помощью сервиса Personalizer необходимо создать новый ресурс Personalizer и выбрать E0 в качестве уровня ценообразования. После создания ресурса Personalizer с уровнем ценообразования E0 можно использовать Azure Key Vault для настройки управляемой идентификации.

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk.md)]

## <a name="next-steps"></a>Дальнейшие действия

* [Форма ключевого запроса, управляемая клиентом Personalizer Service](https://aka.ms/cogsvc-cmk)
* [Узнайте больше о Убежище ключей Azure](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
