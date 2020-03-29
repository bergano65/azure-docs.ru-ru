---
title: Пользовательские поддомены
titleSuffix: Azure Cognitive Services
description: Пользовательские имена поддоменов для каждого ресурса Когнитивной Службы создаются через портал Azure, Azure Cloud Shell или Azure CLI.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 07/24/2019
ms.author: erhopf
ms.openlocfilehash: 108e5ebfa34db711af4ff33cbf4fbba897b8947d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "73647695"
---
# <a name="custom-subdomain-names-for-cognitive-services"></a>Пользовательские имена поддоменов для когнитивных служб

Когнитивные службы Azure используют пользовательские имена поддоменов для каждого ресурса, созданного на [портале Azure,](https://portal.azure.com) [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/)или [Azure CLI.](https://docs.microsoft.com/cli/azure/install-azure-cli) В отличие от региональных конечных точек, которые были общими для всех клиентов в определенном регионе Azure, пользовательские имена поддоменов являются уникальными для ресурса. Пользовательские имена поддоменов необходимы для включения таких функций, как Azure Active Directory (Azure AD) для проверки подлинности.

## <a name="how-does-this-impact-existing-resources"></a>Как это влияет на существующие ресурсы?

Ресурсы Cognitive Services, созданные до 1 июля 2019 года, будут использовать региональные конечные точки для сопутствуаного сервиса. Эти конечные точки будут работать с существующими и новыми ресурсами.

Если вы хотите перенести существующий ресурс, чтобы использовать пользовательские имена поддоменов, чтобы можно включить такие функции, как Azure AD, следуйте следующим инструкциям:

1. Войдите на портал Azure и найдите ресурс Cognitive Services, к который вы хотите добавить специальное имя поддомейна.
2. В лезвии **Обзора** найдите и выберите **пользовательские доменные имена.**
3. Это открывает панель с инструкциями для создания уникального пользовательского поддомена для вашего ресурса.
   > [!WARNING]
   > После того как вы создали пользовательское имя поддомено, оно **не может** быть изменено.

## <a name="do-i-need-to-update-my-existing-resources"></a>Нужно ли обновлять существующие ресурсы?

Нет. Региональная конечная точка будет продолжать работать для новых и существующих когнитивных услуг и пользовательские имя поддоменных является необязательным. Даже если будет добавлено пользовательское имя поддомена, региональная конечная точка продолжит работать с ресурсом.

## <a name="what-if-an-sdk-asks-me-for-the-region-for-a-resource"></a>Что делать, если SDK просит меня для региона для ресурса?

> [!WARNING]
> В настоящее время службы речевого **обеспечения не** поддерживают пользовательские поддомены. Пожалуйста, используйте региональные конечные точки при использовании речевых служб и связанных с ними SDK.

Региональные конечные точки и пользовательские имена поддоменов поддерживаются и могут использоваться взаимозаменяемы. Тем не менее, требуется полная конечная точка.

Информация о регионе доступна в лезвии **«Обзор»** для вашего ресурса на [портале Azure](https://portal.azure.com). Полный список региональных конечных точек можно узнать [о том, есть ли список региональных конечных точек?](#is-there-a-list-of-regional-endpoints)

## <a name="are-custom-subdomain-names-regional"></a>Являются ли пользовательские имена поддоменов региональными?

Да. Использование пользовательского названия поддоменов не меняет ни одного из региональных аспектов вашего ресурса Cognitive Services.

## <a name="what-are-the-requirements-for-a-custom-subdomain-name"></a>Каковы требования к пользовательскому имени поддомена?

Пользовательское имя поддомена является уникальным для вашего ресурса. Название может включать только алфавитные символы и `-` символ; она должна быть между 2 и 64 символов `-`в длину и не может закончиться с .

## <a name="can-i-change-a-custom-domain-name"></a>Могу ли я изменить пользовательское доменное имя?

Нет. После создания пользовательского поддоменного имени и ассоциированного ресурса его нельзя изменить.

## <a name="can-i-reuse-a-custom-domain-name"></a>Могу ли я повторно использовать пользовательское доменное имя?

Каждое пользовательское имя поддомена уникально, поэтому для повторного использования пользовательского поддоменного имени, назначенного ресурсу Cognitive Services, необходимо удалить существующий ресурс. После удаления ресурса можно повторно использовать пользовательское имя поддомена.

## <a name="is-there-a-list-of-regional-endpoints"></a>Есть ли список региональных конечных точек?

Да. Это список региональных конечных точек, которые можно использовать с помощью ресурсов Azure Cognitive Services.

> [!NOTE]
> API-аИС Translator Text и API Bing Search используют глобальные конечные точки.

| Тип конечной точки | Регион | Конечная точка |
|---------------|--------|----------|
| Public | Глобальный (Переводчик Текст & Bing) | `https://api.cognitive.microsoft.com` |
| | Восточная Австралия | `https://australiaeast.api.cognitive.microsoft.com` |
| | Южная Бразилия | `https://brazilsouth.api.cognitive.microsoft.com` |
| | Центральная Канада | `https://canadacentral.api.cognitive.microsoft.com` |
| | Центральная часть США | `https://centralus.api.cognitive.microsoft.com` |
| | Восточная Азия | `https://eastasia.api.cognitive.microsoft.com` |
| | Восточная часть США | `https://eastus.api.cognitive.microsoft.com` |
| | восточная часть США 2 | `https://eastus2.api.cognitive.microsoft.com` |
| | Центральная Франция | `https://francecentral.api.cognitive.microsoft.com` |
| | Центральная Индия | `https://centralindia.api.cognitive.microsoft.com` |
| | Восточная Япония | `https://japaneast.api.cognitive.microsoft.com` |
| | Республика Корея, центральный регион | `https://koreacentral.api.cognitive.microsoft.com` |
| | Центрально-северная часть США | `https://northcentralus.api.cognitive.microsoft.com` |
| | Северная Европа | `https://northeurope.api.cognitive.microsoft.com` |
| | Северная часть ЮАР; | `https://southafricanorth.api.cognitive.microsoft.com` |
| | Центрально-южная часть США | `https://southcentralus.api.cognitive.microsoft.com` |
| | Юго-Восточная Азия | `https://southeastasia.api.cognitive.microsoft.com` |
| | южная часть Соединенного Королевства | `https://uksouth.api.cognitive.microsoft.com` |
| | центрально-западная часть США | `https://westcentralus.api.cognitive.microsoft.com` |
| | Западная Европа | `https://westeurope.api.cognitive.microsoft.com` |
| | западная часть США | `https://westus.api.cognitive.microsoft.com` |
| | западная часть США 2 | `https://westus2.api.cognitive.microsoft.com` |
| US Gov | US Gov (Вирджиния) | `https://virginia.api.cognitive.microsoft.us` |
| Китай | Восточный Китай 2 | `https://chinaeast2.api.cognitive.azure.cn` |
| | Северный Китай | `https://chinanorth.api.cognitive.azure.cn` |

## <a name="see-also"></a>См. также

* [Что такое когнитивные услуги?](Welcome.md)
* [Проверка подлинности](authentication.md)
