---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 156486f4f4f0df3d4bb4ab76492709bbecfb8eb5
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/22/2019
ms.locfileid: "69906565"
---
## <a name="set-up"></a>Настройка

### <a name="create-a-translator-text-resource"></a>Создание ресурса для Перевода текстов

Ресурсами Azure, на которые вы подписаны, будет представлено семейство служб Azure Cognitive Services. Создайте ресурс для Перевода текстов с помощью [портала Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) или [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) на локальном компьютере. Также можно:

* получить бесплатный [ключ пробной версии](https://azure.microsoft.com/try/cognitive-services) на 7 дней. После регистрации он будет доступен на веб-сайте Azure.
* Просмотрите существующий ресурс на [портале Azure](https://portal.azure.com/).

После получения ключа из своего ресурса или пробной подписки создайте две [переменные среды](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication):

* `TRANSLATOR_TEXT_SUBSCRIPTION_KEY` — ключ подписки для ресурса Перевода текстов.
* `TRANSLATOR_TEXT_ENDPOINT` — региональная конечная точка или имя пользовательского поддомена для ресурса.
