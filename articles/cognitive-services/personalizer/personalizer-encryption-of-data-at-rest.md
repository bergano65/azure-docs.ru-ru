---
title: Шифрование неактивных данных в службе персонализации
titleSuffix: Azure Cognitive Services
description: Шифрование неактивных данных в службе персонализации.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: egeaney
ms.openlocfilehash: 10eb627a340b45c93b2cfb2973e294d8d5d7c7e5
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/03/2020
ms.locfileid: "84307838"
---
# <a name="personalizer-service-encryption-of-data-at-rest"></a>Шифрование неактивных данных в службе персонализации

Служба персонализации автоматически шифрует данные при их сохранении в облаке. Шифрование службы персонализации защищает данные и помогает удовлетворить ваши обязательства по обеспечению безопасности и соответствия требованиям Организации.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Ключи, управляемые клиентом, доступны только в ценовой категории E0. Чтобы запросить возможность использования управляемых клиентом ключей, заполните и отправьте [форму запроса ключа, управляемого клиентом для службы персонализации](https://aka.ms/cogsvc-cmk). Для получения сведений о состоянии вашего запроса потребуется около 3-5 рабочих дней. В зависимости от спроса вы можете поместить в очередь и утвердить, как только пространство станет доступным. После утверждения для использования CMK со службой персонализации необходимо создать новый ресурс персонализации и выбрать в качестве ценовой категории E0. После создания ресурса персонализации с ценовой категорией E0 можно использовать Azure Key Vault для настройки управляемого удостоверения.

[!INCLUDE [cognitive-services-cmk](../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>Следующие шаги

* [Форма запроса ключа, управляемого клиентом службы персонализации](https://aka.ms/cogsvc-cmk)
* [Дополнительные сведения о Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
