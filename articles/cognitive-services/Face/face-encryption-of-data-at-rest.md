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
ms.openlocfilehash: 33495dd5b092cb51b3421e7204f3b529077d63b3
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/03/2020
ms.locfileid: "84309039"
---
# <a name="face-service-encryption-of-data-at-rest"></a>Шифрование неактивных данных в службе распознавания лиц

Служба распознавания лиц автоматически шифрует ваши данные при сохранении в облаке. Шифрование службы лиц защищает ваши данные и помогает удовлетворить ваши обязательства по обеспечению безопасности и соответствия требованиям Организации.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Ключи, управляемые клиентом, доступны только в ценовой категории E0. Чтобы запросить возможность использования ключей, управляемых клиентом, заполните и отправьте [форму запроса ключа, управляемого клиентом лиц](https://aka.ms/cogsvc-cmk). Для получения сведений о состоянии вашего запроса потребуется около 3-5 рабочих дней. В зависимости от спроса вы можете поместить в очередь и утвердить, как только пространство станет доступным. После утверждения для использования CMK со службой распознавания лиц необходимо создать новый ресурс для лиц и выбрать E0 в качестве ценовой категории. После создания ресурса лицевой стороны с ценовой категорией E0 можно использовать Azure Key Vault для настройки управляемого удостоверения.

## <a name="regional-availability"></a>Доступность по регионам

Управляемые клиентом ключи в настоящее время доступны в следующих регионах:

* Центрально-южная часть США
* западная часть США 2
* Восточная часть США
* US Gov (Вирджиния)

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>Следующие шаги

* Полный список служб, поддерживающих CMK, см. в разделе [управляемые клиентом ключи для Cognitive Services](../encryption/cognitive-services-encryption-keys-portal.md)
* [Об Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
* [Cognitive Servicesная форма запроса ключа, управляемого клиентом](https://aka.ms/cogsvc-cmk)
