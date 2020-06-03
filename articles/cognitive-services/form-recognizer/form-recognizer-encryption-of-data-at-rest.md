---
title: Шифрование неактивных данных в службе распознавателя форм
titleSuffix: Azure Cognitive Services
description: Расшифровка неактивных данных распознавателем форм.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: egeaney
ms.openlocfilehash: 782e03f572f007b8523c3ae797dd626c383a01f4
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/03/2020
ms.locfileid: "84310722"
---
# <a name="form-recognizer-encryption-of-data-at-rest"></a>Расшифровка неактивных данных распознавателем форм

Средство распознавания форм Azure автоматически шифрует данные при сохранении их в облаке. Шифрование форм обеспечивает защиту данных и помогает удовлетворить ваши обязательства по обеспечению безопасности и соответствия требованиям Организации.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Ключи, управляемые клиентом, — это только доступные ресурсы, созданные после 11 мая 2020 г. Чтобы использовать CMK с распознавателем форм, необходимо создать новый ресурс распознавателя форм. После создания ресурса можно использовать Azure Key Vault для настройки управляемого удостоверения.

[!INCLUDE [cognitive-services-cmk](../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>Следующие шаги

* [Форма запроса ключа, управляемого клиентом распознавателя форм](https://aka.ms/cogsvc-cmk)
* [Дополнительные сведения о Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)


