---
title: Content Moderator шифрование неактивных данных
titleSuffix: Azure Cognitive Services
description: Content Moderator шифрование неактивных данных.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: 38fc21ee45db25f015a6b8b534b0d922efa636f2
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/03/2020
ms.locfileid: "84310552"
---
# <a name="content-moderator-encryption-of-data-at-rest"></a>Content Moderator шифрование неактивных данных

Content Moderator автоматически шифрует данные при сохранении в облаке, помогая обеспечить безопасность и соответствие требованиям Организации.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Ключи, управляемые клиентом, доступны только в ценовой категории E0. Чтобы запросить возможность использования управляемых клиентом ключей, заполните и отправьте [Content Moderator форму запроса ключа, управляемого клиентом](https://aka.ms/cogsvc-cmk). Для получения сведений о состоянии вашего запроса потребуется около 3-5 рабочих дней. В зависимости от спроса вы можете поместить в очередь и утвердить, как только пространство станет доступным. После утверждения для использования CMK со службой Content Moderator необходимо создать новый ресурс Content Moderator и выбрать в качестве ценовой категории значение E0. После создания ресурса Content Moderator с ценовой категорией "E0" можно использовать Azure Key Vault для настройки управляемого удостоверения.

[!INCLUDE [cognitive-services-cmk](../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="enable-data-encryption-for-your-content-moderator-team"></a>Включение шифрования данных для команды Content Moderator

Чтобы включить шифрование данных для группы Content Moderatorной проверки, см. раздел Краткое руководство по началу работы [Content Moderator в Интернете](quick-start.md#create-a-review-team).  

> [!NOTE]
> Вам потребуется указать _идентификатор ресурса_ в ценовой категории Content Moderator E0.

## <a name="next-steps"></a>Следующие шаги

* Полный список служб, поддерживающих CMK, см. в разделе [управляемые клиентом ключи для Cognitive Services](../encryption/cognitive-services-encryption-keys-portal.md)
* [Об Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
* [Cognitive Servicesная форма запроса ключа, управляемого клиентом](https://aka.ms/cogsvc-cmk)

