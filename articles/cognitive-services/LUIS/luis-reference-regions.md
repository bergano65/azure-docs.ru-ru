---
title: Публикации регионов & конечных точек - LUIS
description: Область, указанная на портале Azure, является тем же, где вы будете публиковать приложение LUIS и генерируется URL-адрес endpoint для этого же региона.
ms.topic: reference
ms.date: 11/19/2019
ms.openlocfilehash: a7709d15a51637772d7a4775cd71c307dc21c52b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80292091"
---
# <a name="authoring-and-publishing-regions-and-the-associated-keys"></a>Регионы создания и публикации и связанные ключи

Три авторских региона поддерживаются соответствующими порталами LUIS. Чтобы опубликовать приложение LUIS в несколько регионов, требуется по меньшей мере один ключ для каждого региона.

<a name="luis-website"></a>

## <a name="luis-authoring-regions"></a>LUIS Авторрегионы
Есть три авторских портала LUIS, основанных на регионе. Разработку и публикацию следует выполнять в одном регионе.

|LUIS|Регион разработки|Название региона Лазурного региона|
|--|--|--|
|[www.luis.ai][www.luis.ai] <br>[preview.luis.ai](https://preview.luis.ai)|США<br>не Европа<br>не Австралия| `westus`|
|[au.luis.ai][au.luis.ai] <br>[preview.au.luis.ai](https://preview.au.luis.ai)|Австралия| `australiaeast`|
|[eu.luis.ai][eu.luis.ai] <br>[preview.eu.luis.ai](https://preview.eu.luis.ai)|Европа|`westeurope`|

Авторинговые регионы [имеют парные регионы, не связанные с отказом.](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)

<a name="regions-and-azure-resources"></a>

## <a name="publishing-regions-and-azure-resources"></a>Публикация регионов и ресурсов Azure
Приложения можно публиковать во всех регионах, которые связаны с ресурсами LUIS, добавленными на портале LUIS. Например, для приложения, созданного [на www.luis.ai,][www.luis.ai]если вы создаете ресурс LUIS или Cognitive Service в **westus** и добавляете его в [приложение в качестве ресурса,](luis-how-to-azure-subscription.md)приложение публикуется в этом регионе.

## <a name="public-apps"></a>Общедоступные приложения
Общедоступное приложение можно публиковать во всех регионах, чтобы пользователь с ключом ресурса LUIS для определенного региона смог получить доступ к приложению в том регионе, который связан с ключом ресурса.

<a name="publishing-regions"></a>

## <a name="publishing-regions-are-tied-to-authoring-regions"></a>Издательские регионы привязаны к авторским регионам

Приложение региона разработки можно публиковать только в соответствующий регион публикации. Если приложение находится в неправильном регионе разработки, экспортируйте его, а затем импортируйте в правильный регион разработки для региона публикации.

Приложения LUIS, созданные в https://www.luis.ai, могут быть опубликованы во всех конечных точках, за исключением [европейских](#publishing-to-europe) и [австралийских](#publishing-to-australia) регионов.

## <a name="publishing-to-europe"></a>Публикация в Европе

Для публикации в европейские регионы приложения LUIS нужно создавать только по адресу https://eu.luis.ai. При попытке выполнить публикацию в другое место с помощью ключа в европейском регионе LUIS отображает предупреждающее сообщение. Вместо этого используйте https://eu.luis.ai. Приложения LUIS, [https://eu.luis.ai][eu.luis.ai] созданные в ней, не перемещаются автоматически в другие регионы. Чтобы перенести приложение LUIS, экспортируйте, а затем импортируйте его.

## <a name="europe-publishing-regions"></a>Регионы публикации Европы

 Глобальный регион | Регион разработки API и разработки веб-сайта| Регион публикации и запроса<br>`API region name`   |  Формат URL-адреса конечной точки   |
|-----|------|------|------|
| [Европа](#publishing-to-europe)| `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Центральная Франция<br>`francecentral`     | `https://francecentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| [Европа](#publishing-to-europe)| `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Северная Европа<br>`northeurope`     | `https://northeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| [Европа](#publishing-to-europe) | `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Западная Европа<br>`westeurope`    |  `https://westeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| [Европа](#publishing-to-europe) | `westeurope`<br>[eu.luis.ai][eu.luis.ai]| южная часть Соединенного Королевства<br>`uksouth`    |  `https://uksouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |

## <a name="publishing-to-australia"></a>Публикация в Австралии

Для публикации в австралийские регионы приложения LUIS нужно создавать только по адресу https://au.luis.ai. При попытке выполнить публикацию в другое место с помощью ключа в австралийском регионе LUIS отображает предупреждающее сообщение. Вместо этого используйте https://au.luis.ai. Приложения LUIS, [https://au.luis.ai][au.luis.ai] созданные в ней, не перемещаются автоматически в другие регионы. Чтобы перенести приложение LUIS, экспортируйте, а затем импортируйте его.

## <a name="australia-publishing-regions"></a>Австралия издательских регионов

 Глобальный регион | Регион разработки API и разработки веб-сайта| Регион публикации и запроса<br>`API region name`   |  Формат URL-адреса конечной точки   |
|-----|------|------|------|
| [Австралия](#publishing-to-australia) | `australiaeast`<br>[au.luis.ai][au.luis.ai]| Восточная Австралия<br>`australiaeast`     |  `https://australiaeast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |

## <a name="publishing-to-other-regions"></a>Публикация в другие регионы

Чтобы опубликовать в других регионах, [https://www.luis.ai](https://www.luis.ai) вы создаете только приложения LUIS.

## <a name="other-publishing-regions"></a>Другие издательские регионы

 Глобальный регион | Регион разработки API и разработки веб-сайта| Регион публикации и запроса<br>`API region name`   |  Формат URL-адреса конечной точки   |
|-----|------|------|------|
| Африка | `westus`<br>[www.luis.ai][www.luis.ai]| Северная часть ЮАР;<br>`southafricanorth` |  `https://southafricanorth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Азия | `westus`<br>[www.luis.ai][www.luis.ai]| Центральная Индия<br>`centralindia` |  `https://centralindia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Азия | `westus`<br>[www.luis.ai][www.luis.ai]| Восточная Азия<br>`eastasia`     |  `https://eastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Азия | `westus`<br>[www.luis.ai][www.luis.ai]| Восточная Япония<br>`japaneast`     |   `https://japaneast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Азия | `westus`<br>[www.luis.ai][www.luis.ai]| Западная Япония<br>`japanwest`     |   `https://japanwest.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Азия | `westus`<br>[www.luis.ai][www.luis.ai]| Республика Корея, центральный регион<br>`koreacentral`     |   `https://koreacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Азия | `westus`<br>[www.luis.ai][www.luis.ai]| Юго-Восточная Азия<br>`southeastasia`     |   `https://southeastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Северная Америка |`westus`<br>[www.luis.ai][www.luis.ai] | Центральная Канада<br>`canadacentral`     |   `https://canadacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Северная Америка |`westus`<br>[www.luis.ai][www.luis.ai] | Центральная часть США<br>`centralus`     |   `https://centralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Северная Америка |`westus`<br>[www.luis.ai][www.luis.ai] | Восточная часть США<br>`eastus`      |  `https://eastus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Северная Америка | `westus`<br>[www.luis.ai][www.luis.ai] | восточная часть США 2<br>`eastus2`     |  `https://eastus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Северная Америка | `westus`<br>[www.luis.ai][www.luis.ai] | Центрально-северная часть США<br>`northcentralus`  |  `https://northcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Северная Америка | `westus`<br>[www.luis.ai][www.luis.ai] | Центрально-южная часть США<br>`southcentralus`  |  `https://southcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Северная Америка |`westus`<br>[www.luis.ai][www.luis.ai] | центрально-западная часть США<br>`westcentralus`    |  `https://westcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Северная Америка | `westus`<br>[www.luis.ai][www.luis.ai] | западная часть США<br>`westus`  |   `https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`  |
| Северная Америка |`westus`<br>[www.luis.ai][www.luis.ai] | западная часть США 2<br>`westus2`    |  `https://westus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`  |
| Южная Америка | `westus`<br>[www.luis.ai][www.luis.ai] | Южная Бразилия<br>`brazilsouth`    |  `https://brazilsouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |

## <a name="endpoints"></a>Конечные точки

Узнайте больше об [авторах и конечных точках прогнозирования.](developer-reference-resource.md)

## <a name="failover-regions"></a>Регионы отработки отказа

Каждый регион имеет вторичный регион, чтобы потерпеть неудачу. Европа терпит неудачу над внутри Europe и Австралия терпит неудачу над внутри Австралия.

Авторинговые регионы [имеют парные регионы, не связанные с отказом.](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Справочник по предварительно созданным сущностям](./luis-reference-prebuilt-entities.md)

 [www.luis.ai]: https://www.luis.ai
 [au.luis.ai]: https://au.luis.ai
 [eu.luis.ai]: https://eu.luis.ai
