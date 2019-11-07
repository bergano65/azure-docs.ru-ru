---
title: Пользовательские поддомены
titleSuffix: Azure Cognitive Services
description: Имена пользовательских поддоменов для каждого ресурса службы "Поиск и Настройка" создаются с помощью портал Azure, Azure Cloud Shell или Azure CLI.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 07/24/2019
ms.author: erhopf
ms.openlocfilehash: 108e5ebfa34db711af4ff33cbf4fbba897b8947d
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73647695"
---
# <a name="custom-subdomain-names-for-cognitive-services"></a>Имена пользовательских поддоменов для Cognitive Services

Azure Cognitive Services использовать пользовательские имена поддоменов для каждого ресурса, созданного с помощью [портал Azure](https://portal.azure.com), [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/)или [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). В отличие от региональных конечных точек, которые были распространены для всех клиентов в определенном регионе Azure, имена пользовательских поддоменов уникальны для ресурса. Имена пользовательских поддоменов необходимы, чтобы включить такие функции, как Azure Active Directory (Azure AD) для проверки подлинности.

## <a name="how-does-this-impact-existing-resources"></a>Как это влияет на существующие ресурсы?

Cognitive Services ресурсов, созданных до 1 июля, 2019 будут использовать региональные конечные точки для связанной службы. Эти конечные точки будут работать с существующими и новыми ресурсами.

Если вы хотите перенести существующий ресурс для использования пользовательских имен поддоменов, чтобы вы могли включить такие функции, как Azure AD, выполните следующие действия:

1. Войдите в портал Azure и нахождение ресурса Cognitive Services, в который вы хотите добавить пользовательское имя поддомена.
2. В колонке **обзора** выберите и щелкните **создать имя пользовательского домена**.
3. Откроется панель с инструкциями по созданию уникального пользовательского поддомена для ресурса.
   > [!WARNING]
   > После создания имени пользовательского поддомена его **нельзя** изменить.

## <a name="do-i-need-to-update-my-existing-resources"></a>Нужно ли обновлять существующие ресурсы?

Нет. Региональная конечная точка будет продолжать работать для новых и существующих Cognitive Services, а имя пользовательского поддомена — необязательно. Даже если добавить имя пользовательского поддомена, региональная конечная точка будет продолжать работать с ресурсом.

## <a name="what-if-an-sdk-asks-me-for-the-region-for-a-resource"></a>Что делать, если пакет SDK запрашивает регион для ресурса?

> [!WARNING]
> В настоящее время службы речи **не** поддерживают пользовательские поддомены. Используйте региональные конечные точки при использовании речевых служб и соответствующих пакетов SDK.

Региональные конечные точки и имена пользовательских поддоменов поддерживаются и могут использоваться взаимозаменяемыми. Однако требуется полная конечная точка.

Сведения о регионе доступны в колонке " **Обзор** " для ресурса в [портал Azure](https://portal.azure.com). Полный список региональных конечных точек см [. в разделе есть ли список региональных конечных точек?](#is-there-a-list-of-regional-endpoints)

## <a name="are-custom-subdomain-names-regional"></a>Являются ли имена пользовательских поддоменов региональными?

Да. При использовании имени пользовательского поддомена не изменяется ни одно из региональных аспектов ресурса Cognitive Services.

## <a name="what-are-the-requirements-for-a-custom-subdomain-name"></a>Каковы требования к имени пользовательского поддомена?

Имя пользовательского поддомена является уникальным для ресурса. Имя может содержать только буквенно-цифровые символы и `-` символ. Длина должна составлять от 2 до 64 символов и заканчиваться `-`.

## <a name="can-i-change-a-custom-domain-name"></a>Можно ли изменить имя пользовательского домена?

Нет. После создания имени пользовательского поддомена и его связывания с ресурсом его нельзя изменить.

## <a name="can-i-reuse-a-custom-domain-name"></a>Можно ли повторно использовать имя пользовательского домена?

Каждое имя пользовательского поддомена уникально, поэтому для повторного использования имени пользовательского поддомена, назначенного ресурсу Cognitive Services, необходимо удалить существующий ресурс. После удаления ресурса можно повторно использовать имя пользовательского поддомена.

## <a name="is-there-a-list-of-regional-endpoints"></a>Есть ли список региональных конечных точек?

Да. Это список региональных конечных точек, которые можно использовать с ресурсами Cognitive Services Azure.

> [!NOTE]
> API перевода текстов и API-интерфейсы поиска Bing используют глобальные конечные точки.

| Тип конечной точки | Регион | Конечная точка |
|---------------|--------|----------|
| Общедоступные | Глобальный (Перевод текстов & Bing) | `https://api.cognitive.microsoft.com` |
| | Восточная часть Австралии | `https://australiaeast.api.cognitive.microsoft.com` |
| | Южная Бразилия | `https://brazilsouth.api.cognitive.microsoft.com` |
| | Центральная Канада | `https://canadacentral.api.cognitive.microsoft.com` |
| | Центральный регион США | `https://centralus.api.cognitive.microsoft.com` |
| | Восточная Азия | `https://eastasia.api.cognitive.microsoft.com` |
| | Восток США | `https://eastus.api.cognitive.microsoft.com` |
| | Восток США 2 | `https://eastus2.api.cognitive.microsoft.com` |
| | Центральная Франция | `https://francecentral.api.cognitive.microsoft.com` |
| | Центральная Индия | `https://centralindia.api.cognitive.microsoft.com` |
| | Восточная часть Японии | `https://japaneast.api.cognitive.microsoft.com` |
| | Центральная Корея | `https://koreacentral.api.cognitive.microsoft.com` |
| | Центрально-северная часть США | `https://northcentralus.api.cognitive.microsoft.com` |
| | Северная Европа | `https://northeurope.api.cognitive.microsoft.com` |
| | Северная часть ЮАР | `https://southafricanorth.api.cognitive.microsoft.com` |
| | Центрально-южная часть США | `https://southcentralus.api.cognitive.microsoft.com` |
| | Юго-Восточная Азия | `https://southeastasia.api.cognitive.microsoft.com` |
| | Южная часть Великобритании | `https://uksouth.api.cognitive.microsoft.com` |
| | Западно-центральная часть США | `https://westcentralus.api.cognitive.microsoft.com` |
| | Западная Европа | `https://westeurope.api.cognitive.microsoft.com` |
| | Запад США | `https://westus.api.cognitive.microsoft.com` |
| | Западный регион США 2 | `https://westus2.api.cognitive.microsoft.com` |
| Штат в США (для обслуживания государственных организаций США) | Правительство штата Вирджиния | `https://virginia.api.cognitive.microsoft.us` |
| Китай | Восточный Китай 2 | `https://chinaeast2.api.cognitive.azure.cn` |
| | Север Китая | `https://chinanorth.api.cognitive.azure.cn` |

## <a name="see-also"></a>Дополнительные материалы

* [Что такое Cognitive Services?](Welcome.md)
* [Проверка подлинности](authentication.md)
