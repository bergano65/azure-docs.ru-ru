---
title: Получение ключей подписки — компьютерное зрение
titlesuffix: Azure Cognitive Services
description: Узнайте, как получить ключи подписки для вызовов API компьютерного зрения в Cognitive Services.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: article
ms.date: 05/19/2017
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: 08838ce0af16cc4ae768bd5d2ecf72c57f8fae97
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55858082"
---
# <a name="how-to-obtain-subscription-keys"></a>Получение ключей подписки

Для служб компьютерного зрения требуются специальные ключи подписки. Каждый вызов API компьютерного зрения требует ключ подписки. Этот ключ должен быть либо передан через параметр строки запроса, либо указан в заголовке запроса.

Сведения о том, как зарегистрироваться для получения ключей подписки, см. в статье [Подписки](https://azure.microsoft.com/try/cognitive-services/). Регистрация бесплатна. Цены на эти службы могут измениться.

>[!NOTE]
Ключи подписки действительны только для одного из перечисленных ниже [регионов Microsoft Azure](https://azure.microsoft.com/regions/). 

| Регион | Адрес |
|---|---|
| Запад США | westus.api.cognitive.microsoft.com |
| Восток США 2 | eastus2.api.cognitive.microsoft.com |
| Западно-центральная часть США | westcentralus.api.cognitive.microsoft.com |
| Западная Европа | westeurope.api.cognitive.microsoft.com |
| Юго-Восточная Азия | southeastasia.api.cognitive.microsoft.com |

Если вы зарегистрировались для получения бесплатной пробной версии компьютерного зрения, ваши ключи подписки действительны для региона **Западно-центральная часть США** (`https://westcentralus.api.cognitive.microsoft.com/`). Это наиболее распространенный случай. Однако если вы регистрируетесь для получения компьютерного зрения с помощью учетной записи Microsoft Azure через веб-сайт [https://azure.microsoft.com/](https://azure.microsoft.com/), то вы указываете регион из приведенного выше списка для пробной версии.

Например, если вы регистрируетесь для получения компьютерного зрения с помощью учетной записи Microsoft Azure и указываете регион `westus`, то для вызовов REST API необходимо использовать регион `westus` (`https://westus.api.cognitive.microsoft.com/`).

Если после получения ключа пробной версии вы забыли регион, связанный с ключом подписки, определить его можно на странице по адресу [https://azure.microsoft.com/try/cognitive-services/my-apis/](https://azure.microsoft.com/try/cognitive-services/my-apis/).

### <a name="related-links"></a>Связанные ссылки:

* [Варианты цен на API-интерфейсы Azure Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/)
