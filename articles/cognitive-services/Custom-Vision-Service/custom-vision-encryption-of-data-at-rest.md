---
title: Пользовательское визуальное распознавание шифрование неактивных данных
titleSuffix: Azure Cognitive Services
description: Корпорация Майкрософт предлагает ключи шифрования, управляемые корпорацией Майкрософт, а также позволяет управлять Cognitive Services подписками с помощью собственных ключей, называемых управляемыми клиентом ключами (CMK). В этой статье описывается шифрование неактивных данных для Пользовательское визуальное распознавание и включение и управление CMK.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: 39257419f179bdce8c94f2ddb3a7cd8f5ac2d34f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "89077761"
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

## <a name="next-steps"></a>Дальнейшие шаги

* Полный список служб, поддерживающих CMK, см. в разделе [управляемые клиентом ключи для Cognitive Services](../encryption/cognitive-services-encryption-keys-portal.md)
* [Об Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
* [Форма запроса ключа Cognitive Services Customer-Managed](https://aka.ms/cogsvc-cmk)
