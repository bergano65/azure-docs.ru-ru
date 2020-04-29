---
title: Шифрование неактивных данных в службе распознавания лиц
titleSuffix: Azure Cognitive Services
description: Шифрование неактивных данных в службе распознавания лиц.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: egeaney
ms.openlocfilehash: 793c21bb2341033aa51ff8c639846e57ada4bae3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "79372219"
---
# <a name="face-service-encryption-of-data-at-rest"></a>Шифрование неактивных данных в службе распознавания лиц

Служба распознавания лиц автоматически шифрует ваши данные при сохранении в облаке. Шифрование службы лиц защищает ваши данные и помогает удовлетворить ваши обязательства по обеспечению безопасности и соответствия требованиям Организации.

[!INCLUDE [cognitive-services-about-encryption](../../../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Ключи, управляемые клиентом, доступны только в ценовой категории E0. Чтобы запросить возможность использования ключей, управляемых клиентом, заполните и отправьте [форму запроса ключа, управляемого клиентом лиц](https://aka.ms/cogsvc-cmk). Для получения сведений о состоянии вашего запроса потребуется около 3-5 рабочих дней. В зависимости от спроса вы можете поместить в очередь и утвердить, как только пространство станет доступным. После утверждения для использования CMK со службой распознавания лиц необходимо создать новый ресурс для лиц и выбрать E0 в качестве ценовой категории. После создания ресурса лицевой стороны с ценовой категорией E0 можно использовать Azure Key Vault для настройки управляемого удостоверения.

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk.md)]

## <a name="next-steps"></a>Следующие шаги

* [Пользователь службы лицевой стороны — управляемая форма запроса ключа](https://aka.ms/cogsvc-cmk)
* [Дополнительные сведения о Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)


