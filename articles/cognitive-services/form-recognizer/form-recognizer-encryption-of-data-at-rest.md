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
ms.openlocfilehash: b897de94ad8cdb628520f9386b076f762a5cc230
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86537958"
---
# <a name="form-recognizer-encryption-of-data-at-rest"></a>Расшифровка неактивных данных распознавателем форм

Средство распознавания форм Azure автоматически шифрует данные при сохранении их в облаке. Шифрование форм обеспечивает защиту данных и помогает удовлетворить ваши обязательства по обеспечению безопасности и соответствия требованиям Организации.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Ключи, управляемые клиентом, — это только доступные ресурсы, созданные после 11 мая 2020 г. Чтобы использовать CMK с распознавателем форм, необходимо создать новый ресурс распознавателя форм. После создания ресурса можно использовать Azure Key Vault для настройки управляемого удостоверения.

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>Дальнейшие действия

* [Форма запроса ключа, управляемого клиентом распознавателя форм](https://aka.ms/cogsvc-cmk)
* [Дополнительные сведения о Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)


