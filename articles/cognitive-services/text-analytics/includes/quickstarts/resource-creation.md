---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 10/28/2019
ms.author: aahi
ms.openlocfilehash: 2fe3104d61b5fe2fbf9624ed2fd4fdb2de5686a2
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73750127"
---
Получение ключа и конечной точки для аутентификации приложений. Создайте ресурс для Аналитики текста с помощью [портала Azure](../../../cognitive-services-apis-create-account.md) или [интерфейса командной строки Azure (CLI)](../../../cognitive-services-apis-create-account-cli.md) на локальном компьютере. Также можно:

* получить бесплатный [ключ пробной версии](https://azure.microsoft.com/try/cognitive-services/#decision) на 7 дней. (после регистрации он будет доступен на [веб-сайте Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/));  
* Просмотреть этот ресурс на [портале Azure](https://portal.azure.com/).

Получив ключ и конечную точку для ресурса или пробной подписки, создайте две [переменные среды](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication). Одна из них, с именем `TEXT_ANALYTICS_SUBSCRIPTION_KEY`, предназначена для ключа, а вторая с именем `TEXT_ANALYTICS_ENDPOINT` — для конечной точки.