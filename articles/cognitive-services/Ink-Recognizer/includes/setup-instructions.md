---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: include
ms.date: 06/20/2019
ms.openlocfilehash: c68e5b7ab24e2d7e7f30ddc356ae3c4382137507
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/08/2020
ms.locfileid: "94369083"
---
>[!NOTE]
> Конечные точки для ресурсов, созданных после 1 июля 2019 г., поддерживают пользовательский формат поддомена, показанный ниже. Дополнительные сведения и полный список региональных конечных точек см. в статье [Custom subdomain names for Cognitive Services](../../cognitive-services-custom-subdomains.md) (Пользовательские имена поддоменов для Cognitive Services). 

Ресурсами Azure, на которые вы подписаны, будет представлено семейство служб Azure Cognitive Services. Создайте ресурс для Распознавателя рукописного текста на [портале Azure](../../cognitive-services-apis-create-account.md).

После создания ресурса вы можете получить конечную точку и ключ, открыв ресурс на [портале Azure](https://ms.portal.azure.com#blade/HubsExtension/BrowseResourceGroupBlade) и щелкнув **Быстрый запуск**.

Создайте две переменные [среды](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource):

* `INK_RECOGNITION_SUBSCRIPTION_KEY` — ключ подписки для аутентификации запросов. 

* `INK_RECOGNITION_ENDPOINT` — конечная точка для вашего ресурса. Она должна выглядеть так: <br> `https://<your-custom-subdomain>.api.cognitive.microsoft.com`