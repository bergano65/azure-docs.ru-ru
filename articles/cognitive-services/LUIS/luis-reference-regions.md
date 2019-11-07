---
title: Регионы публикации & конечные точки — LUIS
titleSuffix: Azure Cognitive Services
description: Регион, указанный в портал Azure, совпадает с областью, в которой будет опубликовано приложение LUIS, а для этого же региона создается URL-адрес конечной точки.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: diberry
ms.openlocfilehash: fe4436afe56e631ae308b70ae35b1fb4a7c32e11
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73669186"
---
# <a name="authoring-and-publishing-regions-and-the-associated-keys"></a>Регионы создания и публикации и связанные ключи

Соответствующие порталы LUIS поддерживают три области разработки. Чтобы опубликовать приложение LUIS в несколько регионов, требуется по меньшей мере один ключ для каждого региона. 

<a name="luis-website"></a>

## <a name="luis-authoring-regions"></a>LUIS области разработки
На основе региона существует три LUIS портала для создания порталов. Разработку и публикацию следует выполнять в одном регионе. 

|LUIS|Регион разработки|Имя региона Azure|
|--|--|--|
|[www.luis.ai][www.luis.ai]|Данные учреждений<br>не Европа<br>не Австралия| `westus`|
|[au.luis.ai][au.luis.ai]|Австралия| `australiaeast`|
|[eu.luis.ai][eu.luis.ai]|Европа|`westeurope`|

Области разработки имеют [парные регионы отработки отказа](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 

<a name="regions-and-azure-resources"></a>

## <a name="publishing-regions-and-azure-resources"></a>Регионы публикации и ресурсы Azure
Приложения можно публиковать во всех регионах, которые связаны с ресурсами LUIS, добавленными на портале LUIS. Например, для приложения, созданного на [www.Luis.AI][www.luis.ai], при создании ресурса Luis или службы в **westus** и [добавлении его в приложение в качестве ресурса](luis-how-to-azure-subscription.md)приложение публикуется в этом регионе. 

## <a name="public-apps"></a>Общедоступные приложения
Общедоступное приложение можно публиковать во всех регионах, чтобы пользователь с ключом ресурса LUIS для определенного региона смог получить доступ к приложению в том регионе, который связан с ключом ресурса.

<a name="publishing-regions"></a>

## <a name="publishing-regions-are-tied-to-authoring-regions"></a>Регионы публикации привязаны к областям разработки

Приложение региона разработки можно публиковать только в соответствующий регион публикации. Если приложение находится в неправильном регионе разработки, экспортируйте его, а затем импортируйте в правильный регион разработки для региона публикации. 

Приложения LUIS, созданные в https://www.luis.ai, могут быть опубликованы во всех конечных точках, за исключением [европейских](#publishing-to-europe) и [австралийских](#publishing-to-australia) регионов. 

## <a name="publishing-to-europe"></a>Публикация в Европе

Для публикации в европейские регионы приложения LUIS нужно создавать только по адресу https://eu.luis.ai. При попытке выполнить публикацию в другое место с помощью ключа в европейском регионе LUIS отображает предупреждающее сообщение. Вместо этого используйте https://eu.luis.ai. Приложения LUIS, созданные в [https://eu.luis.ai][eu.luis.ai] , не переносятся автоматически в другие регионы. Чтобы перенести приложение LUIS, экспортируйте, а затем импортируйте его.

## <a name="europe-publishing-regions"></a>Регионы публикации в Европе

 Глобальный регион | Регион разработки API и разработки веб-сайта| Регион публикации и запроса<br>`API region name`   |  Формат URL-адреса конечной точки   |
|-----|------|------|------|
| [Европа](#publishing-to-europe)| `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Центральная Франция<br>`francecentral`     | https://francecentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| [Европа](#publishing-to-europe)| `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Северная Европа<br>`northeurope`     | https://northeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| [Европа](#publishing-to-europe) | `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Западная Европа<br>`westeurope`    |  https://westeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| [Европа](#publishing-to-europe) | `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Южная часть Великобритании<br>`uksouth`    |  https://uksouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="publishing-to-australia"></a>Публикация в Австралии

Для публикации в австралийские регионы приложения LUIS нужно создавать только по адресу https://au.luis.ai. При попытке выполнить публикацию в другое место с помощью ключа в австралийском регионе LUIS отображает предупреждающее сообщение. Вместо этого используйте https://au.luis.ai. Приложения LUIS, созданные в [https://au.luis.ai][au.luis.ai] , не переносятся автоматически в другие регионы. Чтобы перенести приложение LUIS, экспортируйте, а затем импортируйте его.

## <a name="australia-publishing-regions"></a>Регионы публикации Австралии

 Глобальный регион | Регион разработки API и разработки веб-сайта| Регион публикации и запроса<br>`API region name`   |  Формат URL-адреса конечной точки   |
|-----|------|------|------|
| [Австралия](#publishing-to-australia) | `australiaeast`<br>[au.luis.ai][au.luis.ai]| Восточная часть Австралии<br>`australiaeast`     |  https://australiaeast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="publishing-to-other-regions"></a>Публикация в других регионах

Для публикации в других регионах вы создаете приложения LUIS только в [https://www.luis.ai](https://www.luis.ai) . 

## <a name="other-publishing-regions"></a>Другие регионы публикации

 Глобальный регион | Регион разработки API и разработки веб-сайта| Регион публикации и запроса<br>`API region name`   |  Формат URL-адреса конечной точки   |
|-----|------|------|------|
| Африка | `westus`<br>[www.luis.ai][www.luis.ai]| Северная часть ЮАР<br>`southafricanorth` |  https://southafricanorth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Азия | `westus`<br>[www.luis.ai][www.luis.ai]| Центральная Индия<br>`centralindia` |  https://centralindia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Азия | `westus`<br>[www.luis.ai][www.luis.ai]| Восточная Азия<br>`eastasia`     |  https://eastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Азия | `westus`<br>[www.luis.ai][www.luis.ai]| Восточная часть Японии<br>`japaneast`     |   https://japaneast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Азия | `westus`<br>[www.luis.ai][www.luis.ai]| Западная часть Японии<br>`japanwest`     |   https://japanwest.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Азия | `westus`<br>[www.luis.ai][www.luis.ai]| Центральная Корея<br>`koreacentral`     |   https://koreacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Азия | `westus`<br>[www.luis.ai][www.luis.ai]| Юго-Восточная Азия<br>`southeastasia`     |   https://southeastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Северная Америка |`westus`<br>[www.luis.ai][www.luis.ai] | Центральная Канада<br>`canadacentral`     |   https://canadacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Северная Америка |`westus`<br>[www.luis.ai][www.luis.ai] | Центральный регион США<br>`centralus`     |   https://centralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Северная Америка |`westus`<br>[www.luis.ai][www.luis.ai] | Восток США<br>`eastus`      |  https://eastus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Северная Америка | `westus`<br>[www.luis.ai][www.luis.ai] | Восток США 2<br>`eastus2`     |  https://eastus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Северная Америка | `westus`<br>[www.luis.ai][www.luis.ai] | Центрально-северная часть США<br>`northcentralus`  |  https://northcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| Северная Америка | `westus`<br>[www.luis.ai][www.luis.ai] | Центрально-южная часть США<br>`southcentralus`  |  https://southcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| Северная Америка |`westus`<br>[www.luis.ai][www.luis.ai] | Западно-центральная часть США<br>`westcentralus`    |  https://westcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Северная Америка | `westus`<br>[www.luis.ai][www.luis.ai] | Запад США<br>`westus`  |   https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| Северная Америка |`westus`<br>[www.luis.ai][www.luis.ai] | Западный регион США 2<br>`westus2`    |  https://westus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| Северная Америка | `westus`<br>[www.luis.ai][www.luis.ai] | Южная Бразилия<br>`brazilsouth`    |  https://brazilsouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="endpoints"></a>Endpoints

В настоящее время у LUIS есть 2 конечных точки: одна для разработки, а другая — для анализа прогнозов запросов.

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

## <a name="failover-regions"></a>Регионы отработки отказа

Каждый регион имеет дополнительный регион для отработки отказа. При отработки отказа Европы в Европе и Австралии происходит сбой в Австралии.

Области разработки имеют [парные регионы отработки отказа](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Справочник по предварительно созданным сущностям](./luis-reference-prebuilt-entities.md)

 [www.luis.ai]: https://www.luis.ai
 [au.luis.ai]: https://au.luis.ai
 [eu.luis.ai]: https://eu.luis.ai
