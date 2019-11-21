---
title: Publish app - LUIS
titleSuffix: Azure Cognitive Services
description: После завершения создания и проверки рабочего приложения LUIS, сделайте его доступным для приложения клиента путем его публикации в конечной точке.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: f213c1d43930075c78cf81de345f612e46bbfb1c
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74221721"
---
# <a name="publish-your-active-trained-app-to-a-staging-or-production-endpoint"></a>Публикация активного обученного приложения в промежуточной или рабочей конечной точке

When you finish building, training, and testing your active LUIS app, make it available to your client application by publishing it to the endpoint. 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="publishing"></a>Публикация

1. Чтобы опубликовать его в конечной точке, на верхней правой панели выберите **Опубликовать**. 

    ![Publish button in top, right nav bar](./media/luis-how-to-publish-app/publish-top-nav-bar.png)

1. Select your settings for the published prediction endpoint, then select **Publish**.

    ![Select publish settings then select Publish button](./media/luis-how-to-publish-app/publish-pop-up.png)

### <a name="publishing-slots"></a>Publishing slots

Select the correct slot when the pop-up window displays: 

* Промежуточное хранение
* Производство 

By using both publishing slots, this allows you to have two different versions of your app available at the published endpoints or the same version on two different endpoints. 

### <a name="publishing-regions"></a>Регионы публикации

The app is published to all regions associated with the LUIS prediction endpoint resources added in the LUIS portal from the **Manage** ->  **[Azure Resources](/luis-how-to-azure-subscription.md#assign-a-resource-to-an-app)** page. 

For example, for an app created on [www.luis.ai](https://www.luis.ai), if you create a LUIS resource in two regions, **westus** and **eastus**, and add these to the app as resources, the app is published in both regions. Дополнительные сведения о регионах LUIS см. в статье [Регионы и ключи](luis-reference-regions.md).

> [!TIP]
> There are 3 authoring regions. You must author in the region you intend to publish to. If you need to publish to all regions, you need to manage your authoring process and the resulting trained model in all 3 authoring regions. 


## <a name="configuring-publish-settings"></a>Настройка параметров публикации

After you select the slot, configure the publish settings for:

* Анализ мнений
* Spelling correction - v2 prediction endpoint only
* Подготовка речи 

After you publish, these settings are available for review from the **Manage** section's **Publish settings** page. You can change the settings with every publish. If you cancel a publish, any changes you made during the publish are also canceled. 

### <a name="when-your-app-is-published"></a>When your app is published

When your app is successfully published, a success notification appears at the top of the browser. The notification also includes a link to the endpoints. 

Если требуется узнать URL-адрес конечной точки, выберите ссылку. You can also get to the endpoint URLs by selecting **Manage** in the top menu, then select **Azure Resources** in the left menu. 

## <a name="sentiment-analysis"></a>Анализ мнений

<a name="enable-sentiment-analysis"></a>

Анализ тональности позволяет LUIS интегрироваться с функцией [Анализ текста](https://azure.microsoft.com/services/cognitive-services/text-analytics/), чтобы обеспечить анализ тональности и ключевых фраз. 

Вам не нужно предоставлять ключ Анализа текста и за использование этой службы в вашей учетной записи Azure нет оплаты. 

Данные тональности представляют собой оценку между 1 и 0, означающую положительную (ближе к 1) или отрицательную (ближе к 0) тональность данных. Метки тональности `positive`, `neutral` и `negative` соответствуют поддерживаемой культуре. Сейчас поддерживаются только английские метки тональности. 

Для получения дополнительной информации об ответе конечной точки JSON с анализом мнений см. раздел[Анализ мнений](luis-concept-data-extraction.md#sentiment-analysis)

## <a name="spelling-correction"></a>Spelling correction

[!INCLUDE [Not supported in V3 API prediction endpoint](./includes/v2-support-only.md)]

Corrections to spelling are made before the LUIS user utterance prediction. You can see any changes to the original utterance, including spelling, in the response.

## <a name="speech-priming"></a>Подготовка речи

Speech priming is the process of using sending the LUIS model to Speech services prior to conversion of text to speech. This allows the speech service to provide speech conversion more accurately for your model. This allows bot Speech and LUIS requests and responses in one call by making one speech call and getting back a LUIS response. It provides less latency overall.

## <a name="next-steps"></a>Дальнейшие действия

* Чтобы узнать, как добавить ключи подписки Azure в LUIS и настроить ключ проверки орфографии Bing, а также как включить все намерения в результаты, см. статью [Управление ключами конечной точки в LUIS](./luis-how-to-azure-subscription.md).
* Инструкции по тестированию приложения с помощью консоли тестирования см. в статье [Test your LUIS app](luis-interactive-test.md) (Протестируйте свое приложение LUIS).

