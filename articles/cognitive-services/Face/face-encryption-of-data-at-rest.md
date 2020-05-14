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
ms.openlocfilehash: 1e0275c91b2243132650be7af256071589091c4b
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83201942"
---
# <a name="face-service-encryption-of-data-at-rest"></a>Шифрование неактивных данных в службе распознавания лиц

Служба распознавания лиц автоматически шифрует ваши данные при сохранении в облаке. Шифрование службы лиц защищает ваши данные и помогает удовлетворить ваши обязательства по обеспечению безопасности и соответствия требованиям Организации.

[!INCLUDE [cognitive-services-about-encryption](../../../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Ключи, управляемые клиентом, доступны только в ценовой категории E0. Чтобы запросить возможность использования ключей, управляемых клиентом, заполните и отправьте [форму запроса ключа, управляемого клиентом лиц](https://aka.ms/cogsvc-cmk). Для получения сведений о состоянии вашего запроса потребуется около 3-5 рабочих дней. В зависимости от спроса вы можете поместить в очередь и утвердить, как только пространство станет доступным. После утверждения для использования CMK со службой распознавания лиц необходимо создать новый ресурс для лиц и выбрать E0 в качестве ценовой категории. После создания ресурса лицевой стороны с ценовой категорией E0 можно использовать Azure Key Vault для настройки управляемого удостоверения.

### <a name="regional-availability"></a>Доступность по регионам

Управляемые клиентом ключи в настоящее время доступны в следующих регионах:

* Центрально-южная часть США
* западная часть США 2
* Восточная часть США
* US Gov (Вирджиния)

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk.md)]

## <a name="next-steps"></a>Дальнейшие действия

* [Пользователь службы лицевой стороны — управляемая форма запроса ключа](https://aka.ms/cogsvc-cmk)
* [Дополнительные сведения о Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)


