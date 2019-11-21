---
title: Publishing regions & endpoints - LUIS
titleSuffix: Azure Cognitive Services
description: The region specified in the Azure portal is the same where you will publish the LUIS app and an endpoint URL is generated for this same region.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: 4f8e5d1c5ac3b0383fee5a901d2bb34edab8dae2
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225492"
---
# <a name="authoring-and-publishing-regions-and-the-associated-keys"></a>Регионы создания и публикации и связанные ключи

Three authoring regions are supported by corresponding LUIS portals. Чтобы опубликовать приложение LUIS в несколько регионов, требуется по меньшей мере один ключ для каждого региона. 

<a name="luis-website"></a>

## <a name="luis-authoring-regions"></a>LUIS Authoring regions
There are three LUIS authoring portals, based on region. Разработку и публикацию следует выполнять в одном регионе. 

|LUIS|Регион разработки|Azure region name|
|--|--|--|
|[www.luis.ai][www.luis.ai] <br>[preview.luis.ai](https://preview.luis.ai)|Внешние<br>не Европа<br>не Австралия| `westus`|
|[au.luis.ai][au.luis.ai] <br>[preview.au.luis.ai](https://preview.au.luis.ai)|Австралия| `australiaeast`|
|[eu.luis.ai][eu.luis.ai] <br>[preview.eu.luis.ai](https://preview.eu.luis.ai)|Европа|`westeurope`|

Authoring regions have [paired fail-over regions](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 

<a name="regions-and-azure-resources"></a>

## <a name="publishing-regions-and-azure-resources"></a>Publishing regions and Azure resources
Приложения можно публиковать во всех регионах, которые связаны с ресурсами LUIS, добавленными на портале LUIS. For example, for an app created on [www.luis.ai][www.luis.ai], if you create a LUIS or Cognitive Service resource in **westus** and [add it to the app as a resource](luis-how-to-azure-subscription.md), the app is published in that region. 

## <a name="public-apps"></a>Общедоступные приложения
Общедоступное приложение можно публиковать во всех регионах, чтобы пользователь с ключом ресурса LUIS для определенного региона смог получить доступ к приложению в том регионе, который связан с ключом ресурса.

<a name="publishing-regions"></a>

## <a name="publishing-regions-are-tied-to-authoring-regions"></a>Publishing regions are tied to authoring regions

Приложение региона разработки можно публиковать только в соответствующий регион публикации. Если приложение находится в неправильном регионе разработки, экспортируйте его, а затем импортируйте в правильный регион разработки для региона публикации. 

Приложения LUIS, созданные в https://www.luis.ai, могут быть опубликованы во всех конечных точках, за исключением [европейских](#publishing-to-europe) и [австралийских](#publishing-to-australia) регионов. 

## <a name="publishing-to-europe"></a>Публикация в Европе

Для публикации в европейские регионы приложения LUIS нужно создавать только по адресу https://eu.luis.ai. При попытке выполнить публикацию в другое место с помощью ключа в европейском регионе LUIS отображает предупреждающее сообщение. Вместо этого используйте https://eu.luis.ai. LUIS apps created at [https://eu.luis.ai][eu.luis.ai] don't automatically migrate to other regions. Чтобы перенести приложение LUIS, экспортируйте, а затем импортируйте его.

## <a name="europe-publishing-regions"></a>Europe publishing regions

 Глобальный регион | Регион разработки API и разработки веб-сайта| Регион публикации и запроса<br>`API region name`   |  Формат URL-адреса конечной точки   |
|-----|------|------|------|
| [Европа](#publishing-to-europe)| `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Центральная Франция<br>`francecentral`     | https://francecentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| [Европа](#publishing-to-europe)| `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Северная Европа<br>`northeurope`     | https://northeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| [Европа](#publishing-to-europe) | `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Западная Европа<br>`westeurope`    |  https://westeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| [Европа](#publishing-to-europe) | `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Южная часть Соединенного Королевства<br>`uksouth`    |  https://uksouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="publishing-to-australia"></a>Публикация в Австралии

Для публикации в австралийские регионы приложения LUIS нужно создавать только по адресу https://au.luis.ai. При попытке выполнить публикацию в другое место с помощью ключа в австралийском регионе LUIS отображает предупреждающее сообщение. Вместо этого используйте https://au.luis.ai. LUIS apps created at [https://au.luis.ai][au.luis.ai] don't automatically migrate to other regions. Чтобы перенести приложение LUIS, экспортируйте, а затем импортируйте его.

## <a name="australia-publishing-regions"></a>Australia publishing regions

 Глобальный регион | Регион разработки API и разработки веб-сайта| Регион публикации и запроса<br>`API region name`   |  Формат URL-адреса конечной точки   |
|-----|------|------|------|
| [Австралия](#publishing-to-australia) | `australiaeast`<br>[au.luis.ai][au.luis.ai]| Восточная Австралия<br>`australiaeast`     |  https://australiaeast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="publishing-to-other-regions"></a>Publishing to other regions

To publish to the other regions, you create LUIS apps at [https://www.luis.ai](https://www.luis.ai) only. 

## <a name="other-publishing-regions"></a>Other publishing regions

 Глобальный регион | Регион разработки API и разработки веб-сайта| Регион публикации и запроса<br>`API region name`   |  Формат URL-адреса конечной точки   |
|-----|------|------|------|
| Африка | `westus`<br>[www.luis.ai][www.luis.ai]| Северная часть ЮАР<br>`southafricanorth` |  https://southafricanorth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Азия | `westus`<br>[www.luis.ai][www.luis.ai]| Центральная Индия<br>`centralindia` |  https://centralindia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Азия | `westus`<br>[www.luis.ai][www.luis.ai]| Восточная Азия<br>`eastasia`     |  https://eastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Азия | `westus`<br>[www.luis.ai][www.luis.ai]| Восточная Япония<br>`japaneast`     |   https://japaneast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Азия | `westus`<br>[www.luis.ai][www.luis.ai]| Западная Япония<br>`japanwest`     |   https://japanwest.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Азия | `westus`<br>[www.luis.ai][www.luis.ai]| Республика Корея, центральный регион<br>`koreacentral`     |   https://koreacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Азия | `westus`<br>[www.luis.ai][www.luis.ai]| Юго-Восточная Азия<br>`southeastasia`     |   https://southeastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Северная Америка |`westus`<br>[www.luis.ai][www.luis.ai] | Центральная Канада<br>`canadacentral`     |   https://canadacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Северная Америка |`westus`<br>[www.luis.ai][www.luis.ai] | Центральная часть США<br>`centralus`     |   https://centralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Северная Америка |`westus`<br>[www.luis.ai][www.luis.ai] | Восточная часть США<br>`eastus`      |  https://eastus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Северная Америка | `westus`<br>[www.luis.ai][www.luis.ai] | Восточная часть США 2<br>`eastus2`     |  https://eastus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Северная Америка | `westus`<br>[www.luis.ai][www.luis.ai] | Центрально-северная часть США<br>`northcentralus`  |  https://northcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| Северная Америка | `westus`<br>[www.luis.ai][www.luis.ai] | Центрально-южная часть США<br>`southcentralus`  |  https://southcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| Северная Америка |`westus`<br>[www.luis.ai][www.luis.ai] | Центрально-западная часть США<br>`westcentralus`    |  https://westcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Северная Америка | `westus`<br>[www.luis.ai][www.luis.ai] | Западная часть США<br>`westus`  |   https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| Северная Америка |`westus`<br>[www.luis.ai][www.luis.ai] | Западная часть США 2<br>`westus2`    |  https://westus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| Южная Америка | `westus`<br>[www.luis.ai][www.luis.ai] | Южная часть Бразилии<br>`brazilsouth`    |  https://brazilsouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="endpoints"></a>Конечные точки

LUIS currently has 2 endpoints: one for authoring and one for query prediction analysis.

|Цель|URL-адрес|
|--|--|
|Разработка|`https://{region}.api.cognitive.microsoft.com/luis/api/v2.0/apps/{appID}/`|
|Анализ текста (прогнозирование запроса)|`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q={q}[&timezoneOffset][&verbose][&spellCheck][&staging][&bing-spell-check-subscription-key][&log]`|

В следующей таблице описаны параметры, обозначенные фигурными скобками `{}` в предыдущей таблице.

|Параметр|Цель|
|--|--|
|region|Регион Azure: для разработки и публикации используются разные регионы|
|appID|Идентификатор приложения LUIS на панели мониторинга, используемый в маршруте URL-адреса|
|q|Текст фразы, отправляемый из клиентского приложения, такого как чат-бот|

## <a name="failover-regions"></a>Failover regions

Each region has a secondary region to fail over to. Europe fails over inside Europe and Australia fails over inside Australia.

Authoring regions have [paired fail-over regions](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Справочник по предварительно созданным сущностям](./luis-reference-prebuilt-entities.md)

 [www.luis.ai]: https://www.luis.ai
 [au.luis.ai]: https://au.luis.ai
 [eu.luis.ai]: https://eu.luis.ai
