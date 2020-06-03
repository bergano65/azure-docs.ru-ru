---
title: Пользовательское визуальное распознавание шифрование неактивных данных
titleSuffix: Azure Cognitive Services
description: Пользовательское визуальное распознавание шифрование неактивных данных.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: egeaney
ms.openlocfilehash: 85cf251db69d33f02e928eaea6a848f20ae7d923
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/03/2020
ms.locfileid: "84310297"
---
# <a name="custom-vision-encryption-of-data-at-rest"></a>Пользовательское визуальное распознавание шифрование неактивных данных

Azure Пользовательское визуальное распознавание автоматически шифрует данные при их сохранении в облаке. Шифрование Пользовательское визуальное распознавание защищает данные и помогает удовлетворить ваши обязательства по обеспечению безопасности и соответствия требованиям Организации.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Ключи, управляемые клиентом, — это только доступные ресурсы, созданные после 11 мая 2020 г. Чтобы использовать CMK с Пользовательское визуальное распознавание, потребуется создать новый ресурс Пользовательское визуальное распознавание. После создания ресурса можно использовать Azure Key Vault для настройки управляемого удостоверения.

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
