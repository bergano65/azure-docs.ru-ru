---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: c737447c3a3bd2d76d3ed620b7c61aaa81250130
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70393808"
---
## <a name="set-up"></a>Настройка

### <a name="create-a-translator-text-resource"></a>Создание ресурса для Перевода текстов

Ресурсами Azure, на которые вы подписаны, будет представлено семейство служб Azure Cognitive Services. Создайте ресурс для Перевода текстов с помощью [портала Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) или [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) на локальном компьютере. Кроме того, вы можете сделать следующее:

* получить бесплатный [ключ пробной версии](https://azure.microsoft.com/try/cognitive-services) на 7 дней. После регистрации он будет доступен на веб-сайте Azure.
* Просмотрите существующий ресурс на [портале Azure](https://portal.azure.com/).

После получения ключа из своего ресурса или пробной подписки создайте две [переменные среды](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication):

* `TRANSLATOR_TEXT_SUBSCRIPTION_KEY` — ключ подписки для ресурса Перевода текстов.
* `TRANSLATOR_TEXT_ENDPOINT` — глобальная конечная точка для Перевода текстов. Используйте `https://api.cognitive.microsofttranslator.com/`.
