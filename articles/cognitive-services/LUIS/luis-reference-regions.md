---
title: Регионы и конечные точки публикации
titleSuffix: Azure Cognitive Services
description: Регион, в который публикуется приложение LUIS, соответствует региону или расположению, указываемому на портале Azure при создании ключа конечной точки LUIS Azure. Когда вы публикуете приложение, LUIS автоматически создает URL-адрес конечной точки для региона, связанного с ключом.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 01/10/2019
ms.author: diberry
ms.openlocfilehash: 655a2c0e2a7ee483dccbf129f36611d0b36df61f
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/12/2019
ms.locfileid: "54246319"
---
# <a name="authoring-and-publishing-regions-and-the-associated-keys"></a>Регионы создания и публикации и связанные ключи

Регион, в который публикуется приложение LUIS, соответствует региону или расположению, указываемому на портале Azure при создании ключа конечной точки LUIS Azure. Когда вы [публикуете приложение](./luis-how-to-publish-app.md), LUIS автоматически создает URL-адрес конечной точки для региона, связанного с ключом. Чтобы опубликовать приложение LUIS в несколько регионов, требуется по меньшей мере один ключ для каждого региона. 

## <a name="luis-website"></a>Веб-сайт LUIS
Существует три веб-сайта LUIS на основе региона. Разработку и публикацию следует выполнять в одном регионе. 

|LUIS|Регион|
|--|--|
|[www.luis.ai][www.luis.ai]|Данные учреждений<br>не Европа<br>не Австралия|
|[au.luis.ai][au.luis.ai]|Австралия|
|[eu.luis.ai][eu.luis.ai]|Европа|

## <a name="regions-and-azure-resources"></a>Регионы и ресурсы Azure
Приложения можно публиковать во всех регионах, которые связаны с ресурсами LUIS, добавленными на портале LUIS. Например, если создать ресурс LUIS в регионе **westus** и добавить его в приложение, созданное по адресу [www.luis.ai][www.luis.ai], то приложение опубликуется в этом регионе. 

## <a name="public-apps"></a>Общедоступные приложения
Общедоступное приложение можно публиковать во всех регионах, чтобы пользователь с ключом ресурса LUIS для определенного региона смог получить доступ к приложению в том регионе, который связан с ключом ресурса.

## <a name="publishing-regions"></a>Регионы публикации

Приложения LUIS, созданные в https://www.luis.ai, могут быть опубликованы во всех конечных точках, за исключением [европейских](#publishing-to-europe) и [австралийских](#publishing-to-australia) регионов. 

Приложение региона разработки можно публиковать только в соответствующий регион публикации. Если приложение находится в неправильном регионе разработки, экспортируйте его, а затем импортируйте в правильный регион разработки для региона публикации. 

 Глобальный регион | Регион разработки API и разработки веб-сайта| Регион публикации и запроса<br>`API region name`   |  Формат URL-адреса конечной точки   |
|-----|------|------|------|
| Азия | `westus`<br>[www.luis.ai][www.luis.ai]| Центральная Индия<br>`centralindia` |  https://centralindia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Азия | `westus`<br>[www.luis.ai][www.luis.ai]| Восточная Азия<br>`eastasia`     |  https://eastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Азия | `westus`<br>[www.luis.ai][www.luis.ai]| Восточная часть Японии<br>`japaneast`     |   https://japaneast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Азия | `westus`<br>[www.luis.ai][www.luis.ai]| Западная часть Японии<br>`japanwest`     |   https://japanwest.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Азия | `westus`<br>[www.luis.ai][www.luis.ai]| Центральная Корея<br>`koreacentral`     |   https://koreacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Азия | `westus`<br>[www.luis.ai][www.luis.ai]| Юго-Восточная Азия<br>`southeastasia`     |   https://southeastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| *[Австралия](#publishing-to-australia) | `australiaeast`<br>[au.luis.ai][au.luis.ai]| Восточная часть Австралии<br>`australiaeast`     |  https://australiaeast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| *[Европа](#publishing-to-europe)| `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Центральная Франция<br>`francecentral`     | https://francecentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| *[Европа](#publishing-to-europe)| `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Северная Европа<br>`northeurope`     | https://northeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| *[Европа](#publishing-to-europe) | `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Западная Европа<br>`westeurope`    |  https://westeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| *[Европа](#publishing-to-europe) | `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Южная часть Великобритании<br>`uksouth`    |  https://uksouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| Северная Америка |`westus`<br>[www.luis.ai][www.luis.ai] | Центральная Канада<br>`canadacentral`     |   https://canadacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Северная Америка |`westus`<br>[www.luis.ai][www.luis.ai] | Центральный регион США<br>`centralus`     |   https://centralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Северная Америка |`westus`<br>[www.luis.ai][www.luis.ai] | Восточная часть США<br>`eastus`      |  https://eastus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Северная Америка | `westus`<br>[www.luis.ai][www.luis.ai] | Восток США 2<br>`eastus2`     |  https://eastus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Северная Америка | `westus`<br>[www.luis.ai][www.luis.ai] | Центрально-северная часть США<br>`northcentralus`  |  https://northcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| Северная Америка | `westus`<br>[www.luis.ai][www.luis.ai] | Центрально-южная часть США<br>`southcentralus`  |  https://southcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| Северная Америка |`westus`<br>[www.luis.ai][www.luis.ai] | Западно-центральная часть США<br>`westcentralus`    |  https://westcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Северная Америка | `westus`<br>[www.luis.ai][www.luis.ai] | Запад США<br>`westus`  |   https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| Северная Америка |`westus`<br>[www.luis.ai][www.luis.ai] | Западный регион США 2<br>`westus2`    |  https://westus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| Северная Америка | `westus`<br>[www.luis.ai][www.luis.ai] | Южная часть Бразилии<br>`brazilsouth`    |  https://brazilsouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |




## <a name="publishing-to-europe"></a>Публикация в Европе

Для публикации в европейские регионы приложения LUIS нужно создавать только по адресу https://eu.luis.ai. При попытке выполнить публикацию в другое место с помощью ключа в европейском регионе LUIS отображает предупреждающее сообщение. Вместо этого используйте https://eu.luis.ai. Приложения LUIS, созданные по адресу [https://eu.luis.ai][eu.luis.ai], не переносятся в другие регионы автоматически. Чтобы перенести приложение LUIS, экспортируйте, а затем импортируйте его.

## <a name="publishing-to-australia"></a>Публикация в Австралии

Для публикации в австралийские регионы приложения LUIS нужно создавать только по адресу https://au.luis.ai. При попытке выполнить публикацию в другое место с помощью ключа в австралийском регионе LUIS отображает предупреждающее сообщение. Вместо этого используйте https://au.luis.ai. Приложения LUIS, созданные по адресу [https://au.luis.ai][au.luis.ai], не переносятся в другие регионы автоматически. Чтобы перенести приложение LUIS, экспортируйте, а затем импортируйте его.

## <a name="endpoints"></a>Конечные точки

В настоящее время у LUIS есть 2 конечные точки: для разработки и для анализа текста.

|Назначение|URL-адрес|
|--|--|
|Разработка|`https://{region}.api.cognitive.microsoft.com/luis/api/v2.0/apps/{appID}/`|
|Анализ текста (прогнозирование запроса)|`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q={q}[&timezoneOffset][&verbose][&spellCheck][&staging][&bing-spell-check-subscription-key][&log]`|

В следующей таблице описаны параметры, обозначенные фигурными скобками `{}` в предыдущей таблице.

|Параметр|Назначение|
|--|--|
|region|Регион Azure: для разработки и публикации используются разные регионы|
|appID|Идентификатор приложения LUIS на панели мониторинга, используемый в маршруте URL-адреса|
|q|Текст фразы, отправляемый из клиентского приложения, такого как чат-бот|


## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Справочник по предварительно созданным сущностям](./luis-reference-prebuilt-entities.md)

 [www.luis.ai]: https://www.luis.ai
 [au.luis.ai]: https://au.luis.ai
 [eu.luis.ai]: https://eu.luis.ai