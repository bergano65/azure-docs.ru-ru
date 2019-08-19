---
title: Ключи подписки — LUIS
titleSuffix: Azure Cognitive Services
description: Вам не нужно создавать ключи подписки для использования первых бесплатных 1000 запросов конечной точки. Если вы получили ошибку _израсходования квоты_ в форме HTTP 403 или 429, вам необходимо создать ключ и назначить его приложению.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: diberry
ms.openlocfilehash: 1f8b84722c881cee1fe196e5a614b58cf3c19031
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68932864"
---
# <a name="using-subscription-keys-with-your-luis-app"></a>Использование ключей подписки с приложением LUIS

При первом использовании Language Understanding (LUIS) не нужно создавать ключи подписки. Вы получили запросы к конечной точке 1000, начиная с. 

Бесплатная ценовая категория (F0) используется только для тестирования и создания прототипов. Для рабочих систем используется [платная](https://aka.ms/luis-price-tier) категория. Не используйте [ключ разработки](luis-concept-keys.md#authoring-key) для запросов конечных точек в рабочей среде.


<a name="create-luis-service"></a>
<a name="create-language-understanding-endpoint-key-in-the-azure-portal"/>

## <a name="create-prediction-endpoint-runtime-resource-in-the-azure-portal"></a>Создание ресурса среды выполнения прогнозирующих точек на портал Azure

Создайте ресурс « [прогнозная конечная точка](get-started-portal-deploy-app.md#create-the-endpoint-resource) » в портал Azure. Этот ресурс следует использовать только для запросов прогнозирования конечной точки. Не используйте этот ресурс для внесения изменений в приложение.

Можно создать Language Understanding ресурс или ресурс Cognitive Services. При создании Language Understanding ресурса рекомендуется постпенд тип ресурса в имя ресурса. 

<a name="programmatic-key" ></a>
<a name="authoring-key" ></a>
<a name="endpoint-key" ></a>
<a name="use-endpoint-key-in-query" ></a>
<a name="api-usage-of-ocp-apim-subscription-key" ></a>
<a name="key-limits" ></a>
<a name="key-limit-errors" ></a>
<a name="key-concepts"></a>
<a name="authoring-key"></a>
<a name="create-and-use-an-endpoint-key"></a>
<a name="assign-endpoint-key"></a>
<a name="assign-resource"></a>

### <a name="using-resource-from-luis-portal"></a>Использование ресурса из портала LUIS

Если вы используете ресурс на портале LUIS, вам не нужно знать ключ и расположение. Вместо этого необходимо знать имя клиента ресурса, подписки и имени ресурса.

Когда вы [назначите](#assign-resource-key-to-luis-app-in-luis-portal) ресурс для приложения Luis на портале Luis, ключ и расположение предоставляются как часть URL-адреса конечной точки прогнозирования запросов на странице "**ключи и параметры конечной точки**" раздела "Управление".
 
### <a name="using-resource-from-rest-api-or-sdk"></a>Использование ресурса из REST API или пакета SDK

Если вы используете ресурс из REST API или пакета SDK, необходимо знать ключ и расположение. Эти сведения предоставляются как часть URL-адреса конечной точки прогнозирования запросов в разделе " **ключи и параметры конечной точки** " раздела "Управление", а также в портал Azure на страницах "Обзор ресурсов" и "ключи".

## <a name="assign-resource-key-to-luis-app-in-luis-portal"></a>Назначение ключа ресурса для приложения LUIS на портале LUIS

Каждый раз при создании нового ресурса для LUIS необходимо [назначить ресурс для приложения Luis](get-started-portal-deploy-app.md#assign-the-resource-key-to-the-luis-app-in-the-luis-portal). После этого вам не нужно будет еще раз выполнять этот шаг до создания следующего ресурса. Вы можете создать ресурс для расширения регионов приложения или для поддержки большего количества запросов прогнозирования.

<!-- content moved to luis-reference-regions.md, need replacement links-->
<a name="regions-and-keys"></a>
<a name="publishing-to-europe"></a>
<a name="publishing-to-australia"></a>

### <a name="unassign-resource"></a>Отменить присвоение ресурса
Когда вы отмените назначение ключа конечной точки, он не удалится из Azure. Он только потеряет связь из LUIS. 

Если ключ конечной точки не назначен или не присвоен приложению, то любой запрос к URL-адресу конечной точки возвращает ошибку: `401 This application cannot be accessed with the current subscription`. 

### <a name="include-all-predicted-intent-scores"></a>Включение всех прогнозируемых оценок намерения
Флажок **Включить все прогнозируемые оценки намерения** позволяет ответить на запрос конечной точки, чтобы включить оценку прогноза для каждого намерения. 

Этот параметр позволяет вашему чат-боту или приложению LUIS принимать программное решение, основанное на оценках возвращенных намерений. Как правило, два верхние намерения являются наиболее интересными. Если верхний показатель намерения — None, ваш чат-бот может задать вопрос, который сделает окончательным выбор между намерением None и другим намерением с высокой оценкой. 

Намерения и их оценки также включены в журналы конечных точек. Вы можете [экспортировать](luis-how-to-start-new-app.md#export-app) эти журналы и проанализировать оценки. 

```JSON
{
  "query": "book a flight to Cairo",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.5223427
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.5223427
    },
    {
      "intent": "BookFlight",
      "score": 0.372391433
    }
  ],
  "entities": []
}
```

### <a name="enable-bing-spell-checker"></a>Включение проверки орфографии Bing 
Флажок **Проверка орфографии Bing** в **настройках URL-адреса конечной точки** позволяет LUIS проверять орфографию перед прогнозированием. Создайте **[ключ API проверки орфографии Bing](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api)** . 

Добавьте параметры строки запроса **spellCheck=true** и **bing-spell-check-subscription-key={YOUR_BING_KEY_HERE}** . Замените `{YOUR_BING_KEY_HERE}` своим ключом проверки правописания Bing.

```JSON
{
  "query": "Book a flite to London?",
  "alteredQuery": "Book a flight to London?",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.780123
  },
  "entities": []
}
```

### <a name="publishing-regions"></a>Регионы публикации

Ознакомьтесь с дополнительными сведениями о [регионах](luis-reference-regions.md) публикации, включая публикацию в [Европе](luis-reference-regions.md#publishing-to-europe) и [Австралии](luis-reference-regions.md#publishing-to-australia). Регионы публикации отличаются от регионов разработки. Убедитесь, что вы разрабатываете приложение в регионе разработки, что соответствует региону публикации конечной точки запроса.

## <a name="assign-resource-without-luis-portal"></a>Назначение ресурса без использования портала LUIS

В целях автоматизации (например, для конвейера CI/CD) вам может потребоваться автоматизировать назначение ресурса LUIS приложению LUIS. Для этого необходимо выполнить указанные ниже действия.

1. Запросите маркер Azure Resource Manager с этого [веб-сайта](https://resources.azure.com/api/token?plaintext=true). Срок действия этого маркера ограничен, поэтому его следует использовать сразу. По запросу возвращается маркер Azure Resource Manager.

    ![Запрашивание и получение маркера Azure Resource Manager](./media/luis-manage-keys/get-arm-token.png)

1. Используя маркер, запросите ресурсы LUIS из разных подписок, доступных вашей учетной записи, при помощи [API получения учетных записей Azure LUIS](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be313cec181ae720aa2b26c). 

    Этот API POST принимает указанные ниже параметры.

    |Header|Значение|
    |--|--|
    |`Authorization`|Значение заголовка `Authorization` — `Bearer {token}`. Обратите внимание, что перед значением маркера должны находиться слово `Bearer` и пробел.| 
    |`Ocp-Apim-Subscription-Key`|Ваш [ключ разработки](luis-how-to-account-settings.md).|

    Этот API возвращает массив объектов JSON ваших подписок LUIS, включая идентификатор подписки, группу ресурсов и имя ресурса, возвращаемое в виде имени учетной записи. Найдите в массиве один элемент, соответствующий ресурсу LUIS, который требуется назначить приложению LUIS. 

1. Назначьте маркер ресурсу LUIS с помощью API [назначения учетных записей Azure LUIS приложению](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32228e8473de116325515). 

    Этот API POST принимает указанные ниже параметры.

    |Type|Параметр|Значение|
    |--|--|--|
    |Header|`Authorization`|Значение заголовка `Authorization` — `Bearer {token}`. Обратите внимание, что перед значением маркера должны находиться слово `Bearer` и пробел.|
    |Header|`Ocp-Apim-Subscription-Key`|Ваш [ключ разработки](luis-how-to-account-settings.md).|
    |Header|`Content-type`|`application/json`|
    |Строка запроса|`appid`|Идентификатор приложения LUIS. 
    |Body||{"AzureSubscriptionId":"ddda2925-af7f-4b05-9ba1-2155c5fe8a8e",<br>"ResourceGroup": "resourcegroup-2",<br>"AccountName": "luis-uswest-S0-2"}|

    При успешном выполнении этого API возвращается состояние "201 - created". 

## <a name="change-pricing-tier"></a>Смена ценовой категории

1.  В [Azure](https://portal.azure.com) найдите подписку LUIS. Выберите подписку LUIS.
    ![Найдите подписку LUIS](./media/luis-usage-tiers/find.png)
1.  Выберите **Ценовая категория**, чтобы просмотреть доступные ценовые категории. 
    ![Просмотрите ценовые категории](./media/luis-usage-tiers/subscription.png)
1.  Выберите ценовую категорию и нажмите кнопку **Выбрать**, чтобы сохранить внесенные изменения. 
    ![Измените ценовую категорию LUIS](./media/luis-usage-tiers/plans.png)
1.  После изменения ценовой категории появится всплывающее окно с новой ценовой категорией. 
    ![Проверьте ценовую категорию LUIS](./media/luis-usage-tiers/updated.png)
1. Не забудьте [назначить этот ключ конечной точки](#assign-endpoint-key) на странице **публикации** и использовать его во всех запросах конечной точки. 

## <a name="fix-http-status-code-403-and-429"></a>Исправление кода состояния HTTP 403 и 429

Вы получаете коды состояния ошибок 403 и 429 при превышении числа транзакций в секунду или транзакций в месяц для ценовой категории.

### <a name="when-you-receive-an-http-403-error-status-code"></a>При получении кода состояния ошибки HTTP 403

При использовании всех этих бесплатных запросов к конечной точке 1000 или превышении квоты месячных транзакций для ценовой категории вы получаете код состояния ошибки HTTP 403. 

Чтобы устранить эту ошибку, необходимо либо [изменить ценовую](luis-how-to-azure-subscription.md#change-pricing-tier) категорию на более высокий уровень, либо [создать новый ресурс](get-started-portal-deploy-app.md#create-the-endpoint-resource) и [назначить его приложению](get-started-portal-deploy-app.md#assign-the-resource-key-to-the-luis-app-in-the-luis-portal).

Ниже приведены решения для этой ошибки.

* В [портал Azure](https://portal.azure.com)на ресурсе Language Understanding в **ценовой категории управление ресурсами — >** измените ценовую категорию на более высокий уровень технической спецификации. Вам не нужно ничего делать на портале Language Understanding, если ресурс уже назначен вашему Language Understanding приложению.
*  Если вы превысили максимальную ценовую категорию, добавьте дополнительные Language Understanding ресурсы с подсистемой балансировки нагрузки перед ними. В этом может помочь [контейнер Language Understanding](luis-container-howto.md) с Kubernetes или DOCKER Compose.

### <a name="when-you-receive-an-http-429-error-status-code"></a>При получении кода состояния ошибки HTTP 429

Этот код состояния возвращается, если количество транзакций в секунду превышает вашу ценовую категорию.  

Решения включают:

* Вы можете [увеличить ценовую категорию](#change-pricing-tier), если вы не на самом высшем уровне.
* Если вы превысили максимальную ценовую категорию, добавьте дополнительные Language Understanding ресурсы с подсистемой балансировки нагрузки перед ними. В этом может помочь [контейнер Language Understanding](luis-container-howto.md) с Kubernetes или DOCKER Compose.
* Вы можете настроить запрос клиентского приложения с помощью [политики повтора](https://docs.microsoft.com/azure/architecture/best-practices/transient-faults#general-guidelines) , которая реализуется при получении этого кода состояния. 

## <a name="viewing-summary-usage"></a>Просмотр сводки использования
Вы можете просмотреть сведения об использовании службы LUIS в Azure. На странице **Обзор** отображаются последние сводные сведения, включая вызовы и ошибки. При отправке запроса конечной точки LUIS сведения об использовании на **странице обзора** отображаются в течение пяти минут.

![Просмотр сводки использования](./media/luis-usage-tiers/overview.png)

## <a name="customizing-usage-charts"></a>Настройка диаграмм использования
Метрики обеспечивают более подробное представление данных.

![Метрики по умолчанию](./media/luis-usage-tiers/metrics-default.png)

Для диаграмм метрик можно настроить тип метрики и период времени. 

![Настраиваемые метрики](./media/luis-usage-tiers/metrics-custom.png)

## <a name="total-transactions-threshold-alert"></a>Оповещение о пороговом значении "Всего транзакций"
Если вы хотите знать, когда будет достигнуто определенное пороговое значение транзакций (например, 10 000), вы можете создать оповещение. 

![Оповещения по умолчанию](./media/luis-usage-tiers/alert-default.png)

Добавьте оповещение для метрики **общее число вызовов** за определенный период времени. Добавьте адреса электронной почты всех пользователей, которые должны получать оповещение. Добавьте веб-привязки для всех систем, которые должны получать оповещения. При запуске оповещения также может выполняться приложение логики. 

## <a name="next-steps"></a>Следующие шаги

Сведения об использовании [версий](luis-how-to-manage-versions.md) для управления изменениями в приложении LUIS.
