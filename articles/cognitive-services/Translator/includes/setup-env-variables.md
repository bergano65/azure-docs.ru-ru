---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: a936afc611462b6ec3c3de9021d517ccf66f666b
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83586558"
---
## <a name="set-up"></a>Настройка

### <a name="create-a-translator-resource"></a>Создание ресурса Переводчика

Ресурсами Azure, на которые вы подписаны, будет представлено семейство служб Azure Cognitive Services. Создайте на локальном компьютере ресурс Переводчика с помощью [портала Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) или [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli). Кроме того, вы можете сделать следующее:

* получить бесплатный [ключ пробной версии](https://azure.microsoft.com/try/cognitive-services) на 7 дней. После регистрации он будет доступен на веб-сайте Azure.
* Просмотрите существующий ресурс на [портале Azure](https://portal.azure.com/).

После получения ключа из своего ресурса или пробной подписки создайте две [переменные среды](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication):

* `TRANSLATOR_TEXT_SUBSCRIPTION_KEY` — ключ подписки для ресурса Переводчика.
* `TRANSLATOR_TEXT_ENDPOINT` — глобальная конечная точка для Переводчика. Используйте `https://api.cognitive.microsofttranslator.com/`.
