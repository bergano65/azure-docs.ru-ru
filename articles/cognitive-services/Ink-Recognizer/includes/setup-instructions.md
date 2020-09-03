---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 06/20/2019
ms.openlocfilehash: 9d46b304d598b4830cf325909f77eea6b68af757
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/02/2020
ms.locfileid: "89304120"
---
>[!NOTE]
> Конечные точки для ресурсов, созданных после 1 июля 2019 г., поддерживают пользовательский формат поддомена, показанный ниже. Дополнительные сведения и полный список региональных конечных точек см. в статье [Custom subdomain names for Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains) (Пользовательские имена поддоменов для Cognitive Services). 

Ресурсами Azure, на которые вы подписаны, будет представлено семейство служб Azure Cognitive Services. Создайте ресурс для Распознавателя рукописного текста на [портале Azure](../../cognitive-services-apis-create-account.md).

После создания ресурса вы можете получить конечную точку и ключ, открыв ресурс на [портале Azure](https://ms.portal.azure.com#blade/HubsExtension/BrowseResourceGroupBlade) и щелкнув **Быстрый запуск**.

Создайте две переменные [среды](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource):

* `INK_RECOGNITION_SUBSCRIPTION_KEY` — ключ подписки для аутентификации запросов. 

* `INK_RECOGNITION_ENDPOINT` — конечная точка для вашего ресурса. Она должна выглядеть так: <br> `https://<your-custom-subdomain>.api.cognitive.microsoft.com`   
