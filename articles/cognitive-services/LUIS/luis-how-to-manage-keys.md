---
title: Управление ключами
titleSuffix: Language Understanding - Azure Cognitive Services
description: После создания на портале Azure ключа конечной точки LUIS присвойте приложению LUIS ключ и получите правильный URL-адрес конечной точки. Используйте этот URL-адрес конечной точки, чтобы получать прогнозирование LUIS.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 0a278ded7ce290347645f345e4eee0b15972787f
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53088108"
---
# <a name="add-an-azure-luis-resource-to-app"></a>Добавление в приложение ресурса LUIS Azure

После создания на портале Azure ресурса LUIS назначьте приложению LUIS ресурс и получите правильный URL-адрес конечной точки. Используйте этот URL-адрес конечной точки, чтобы получать прогнозирование LUIS.

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


## <a name="assign-resource-in-luis-portal"></a>Назначение ресурса на портале LUIS

1. Создайте ключ LUIS на [портале Azure](https://portal.azure.com). Дальнейшие инструкции см. в разделе [Управление ключами подписки конечной точки Azure](luis-how-to-azure-subscription.md).
 
2. Выберите **Управление**в верхнем правом меню, а затем выберите **Keys and endpoints** (Ключи и конечные точки).

    [ ![Страница Keys and endpoints](./media/luis-manage-keys/keys-and-endpoints.png) ](./media/luis-manage-keys/keys-and-endpoints.png#lightbox) (Ключи и конечные точки)

3. Чтобы добавить LUIS, выберите **Assign Resource +** (Присвоить ресурс +).

    ![Присвоение ресурса приложению](./media/luis-manage-keys/assign-key.png)

4. В диалоговом окне выберите клиент, связанный с адресом электронной почты, с помощью которой вы входите на веб-сайт LUIS.  

5. Выберите **Имя подписки**, связанное с ресурсом Azure, который вы хотите добавить.

6. Выберите **LUIS resource name** (Имя ресурса LUIS). 

7. Выберите **Assign resource** (Присвоить ресурс). 

8. Найдите в таблице новую строку и скопируйте URL-адрес конечной точки. Он построен правильно для того, чтобы отправлять запрос HTTP GET в конечную точку LUIS для прогнозирования. 

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

    |Заголовок|Значение|
    |--|--|
    |`Authorization`|Значение заголовка `Authorization` — `Bearer {token}`. Обратите внимание, что перед значением маркера должны находиться слово `Bearer` и пробел.| 
    |`Ocp-Apim-Subscription-Key`|Ваш [ключ разработки](luis-how-to-account-settings.md).|

    Этот API возвращает массив объектов JSON ваших подписок LUIS, включая идентификатор подписки, группу ресурсов и имя ресурса, возвращаемое в виде имени учетной записи. Найдите в массиве один элемент, соответствующий ресурсу LUIS, который требуется назначить приложению LUIS. 

1. Назначьте маркер ресурсу LUIS с помощью API [назначения учетных записей Azure LUIS приложению](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32228e8473de116325515). 

    Этот API POST принимает указанные ниже параметры.

    |type|Параметр|Значение|
    |--|--|--|
    |Заголовок|`Authorization`|Значение заголовка `Authorization` — `Bearer {token}`. Обратите внимание, что перед значением маркера должны находиться слово `Bearer` и пробел.|
    |Заголовок|`Ocp-Apim-Subscription-Key`|Ваш [ключ разработки](luis-how-to-account-settings.md).|
    |Заголовок|`Content-type`|`application/json`|
    |Строка запроса|`appid`|Идентификатор приложения LUIS. 
    |Текст||{"AzureSubscriptionId":"ddda2925-af7f-4b05-9ba1-2155c5fe8a8e",<br>"ResourceGroup": "resourcegroup-2",<br>"AccountName": "luis-uswest-S0-2"}|

    При успешном выполнении этого API возвращается состояние "201 - created". 

## <a name="next-steps"></a>Дополнительная информация

Используйте ключ для публикации приложения на странице **Публикация приложения**. Инструкции по публикации см. в разделе [Публикация приложения](luis-how-to-publish-app.md).

Сведения о разработке приложений LUIS и работе с конечными точками приведены в разделе [Ключи в LUIS](luis-concept-keys.md).
