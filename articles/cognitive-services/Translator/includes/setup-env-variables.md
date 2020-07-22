---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 54ee19f3e278b424384ff55d7065713584235df1
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86144235"
---
## <a name="set-up"></a>Настройка

### <a name="create-a-translator-resource"></a>Создание ресурса Переводчика

Ресурсами Azure, на которые вы подписаны, будет представлено семейство служб Azure Cognitive Services. Создайте на локальном компьютере ресурс Переводчика с помощью [портала Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) или [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli). Кроме того, вы можете сделать следующее:

* Просмотрите существующий ресурс на [портале Azure](https://portal.azure.com/).

После получения ключа из своего ресурса или пробной подписки создайте две [переменные среды](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication):

* `TRANSLATOR_TEXT_SUBSCRIPTION_KEY` — ключ подписки для ресурса Переводчика.
* `TRANSLATOR_TEXT_ENDPOINT` — глобальная конечная точка для Переводчика. Используйте `https://api.cognitive.microsofttranslator.com/`.
