---
title: Ключи подписки
titleSuffix: Language Understanding - Azure Cognitive Services
description: Вам не нужно создавать ключи подписки для использования первых бесплатных 1000 запросов конечной точки. Если вы получили ошибку _израсходования квоты_ в форме HTTP 403 или 429, вам необходимо создать ключ и назначить его приложению.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 03/01/2019
ms.author: diberry
ms.openlocfilehash: 7315c80ad74eae07e41577fb2ac13742002e729e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60198654"
---
# <a name="using-subscription-keys-with-your-luis-app"></a>Использование ключей подписки с приложением LUIS

Вам не нужно создавать ключи подписки для использования первых бесплатных 1000 запросов конечной точки. После использования этих запросов конечной точки создайте ресурс Azure на [портале Azure](https://portal.azure.com), а затем назначьте его приложению LUIS на [портале LUIS](https://www.luis.ai).

Если вы получили ошибку _израсходования квоты_ в форме HTTP 403 или 429, вам необходимо создать ключ и назначить его приложению. 

Бесплатная ценовая категория (F0) используется только для тестирования и создания прототипов. Для рабочих систем используется [платная](https://aka.ms/luis-price-tier) категория. Не используйте [ключ разработки](luis-concept-keys.md#authoring-key) для запросов конечных точек в рабочей среде.

<a name="create-luis-service"></a>
<a name="create-language-understanding-endpoint-key-in-the-azure-portal"/>

## <a name="create-prediction-endpoint-runtime-resource-in-the-azure-portal"></a>Создание прогноза конечной точки среды выполнения ресурса на портале Azure

Дополнительные сведения с [построение приложения](get-started-portal-build-app.md) быстрого запуска.

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


## <a name="assign-resource-key-to-luis-app-in-luis-portal"></a>Назначение ключа ресурса для приложения LUIS на портале LUIS

Дополнительные сведения с [развертывания](get-started-portal-deploy-app.md) быстрого запуска.

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
Флажок **Проверка орфографии Bing** в **настройках URL-адреса конечной точки** позволяет LUIS проверять орфографию перед прогнозированием. Создайте **[ключ API проверки орфографии Bing](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api)**. 

Добавьте параметры строки запроса **spellCheck=true** и **bing-spell-check-subscription-key={YOUR_BING_KEY_HERE}**. Замените `{YOUR_BING_KEY_HERE}` своим ключом проверки правописания Bing.

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

    |Верхний колонтитул|Value|
    |--|--|
    |`Authorization`|Значение заголовка `Authorization` — `Bearer {token}`. Обратите внимание, что перед значением маркера должны находиться слово `Bearer` и пробел.| 
    |`Ocp-Apim-Subscription-Key`|Ваш [ключ разработки](luis-how-to-account-settings.md).|

    Этот API возвращает массив объектов JSON ваших подписок LUIS, включая идентификатор подписки, группу ресурсов и имя ресурса, возвращаемое в виде имени учетной записи. Найдите в массиве один элемент, соответствующий ресурсу LUIS, который требуется назначить приложению LUIS. 

1. Назначьте маркер ресурсу LUIS с помощью API [назначения учетных записей Azure LUIS приложению](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32228e8473de116325515). 

    Этот API POST принимает указанные ниже параметры.

    |type|Параметр|Value|
    |--|--|--|
    |Верхний колонтитул|`Authorization`|Значение заголовка `Authorization` — `Bearer {token}`. Обратите внимание, что перед значением маркера должны находиться слово `Bearer` и пробел.|
    |Верхний колонтитул|`Ocp-Apim-Subscription-Key`|Ваш [ключ разработки](luis-how-to-account-settings.md).|
    |Верхний колонтитул|`Content-type`|`application/json`|
    |Строка запроса|`appid`|Идентификатор приложения LUIS. 
    |Текст||{"AzureSubscriptionId":"ddda2925-af7f-4b05-9ba1-2155c5fe8a8e",<br>"ResourceGroup": "resourcegroup-2",<br>"AccountName": "luis-uswest-S0-2"}|

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

## <a name="how-to-fix-out-of-quota-errors-when-the-key-exceeds-pricing-tier-usage"></a>Способы устранения ошибок израсходования квоты, когда ключ превышает предел использования в определенной ценовой категории
Каждая категория поддерживает определенную частоту запросов к конечной точке в учетной записи LUIS. Если частота запросов (в минуту или в месяц) превышает допустимую частоту тарифицируемой учетной записи, для запросов выводится сообщение об ошибке HTTP "429: слишком много запросов".

Каждая категория позволяет накапливать запросы за месяц. Если общее число запросов превышает допустимую частоту, выдается ошибка HTTP "403: запрещено".  

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

## <a name="next-steps"></a>Дальнейшие действия

Сведения об использовании [версий](luis-how-to-manage-versions.md) для управления изменениями в приложении LUIS.
