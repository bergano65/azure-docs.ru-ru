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
ms.openlocfilehash: 287344b325237843a549973ae61f569eae6dac93
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83202274"
---
# <a name="custom-vision-encryption-of-data-at-rest"></a>Пользовательское визуальное распознавание шифрование неактивных данных

Azure Пользовательское визуальное распознавание автоматически шифрует данные при их сохранении в облаке. Шифрование Пользовательское визуальное распознавание защищает данные и помогает удовлетворить ваши обязательства по обеспечению безопасности и соответствия требованиям Организации.

[!INCLUDE [cognitive-services-about-encryption](../../../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Ключи, управляемые клиентом, — это только доступные ресурсы, созданные после 11 мая 2020 г. Чтобы использовать CMK с Пользовательское визуальное распознавание, потребуется создать новый ресурс Пользовательское визуальное распознавание. После создания ресурса можно использовать Azure Key Vault для настройки управляемого удостоверения.

### <a name="regional-availability"></a>Доступность по регионам

Управляемые клиентом ключи в настоящее время доступны в следующих регионах:

* Центрально-южная часть США
* западная часть США 2
* Восточная часть США
* US Gov (Вирджиния)

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk.md)]

## <a name="next-steps"></a>Дальнейшие действия

* [Пользовательское визуальное распознаваниеная форма запроса ключа, управляемого клиентом](https://aka.ms/cogsvc-cmk)
* [Дополнительные сведения о Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)


