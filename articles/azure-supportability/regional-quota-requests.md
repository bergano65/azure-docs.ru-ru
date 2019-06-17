---
title: Запросы на увеличение квоты Azure региональных | Документация Майкрософт
description: Запросы на увеличение региональной квоты
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 4c5524cf450959db7055ca5d032c81f79ebac077
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67083084"
---
# <a name="total-regional-vcpu-limit-increase"></a>Увеличение лимита общие региональные виртуальные ЦП 

Теперь вы можете запросить увеличение через **Справка и поддержка** колонке или **использование + Квота** колонки на портале. 

## <a name="request-total-regional-vcpus-quota-increase-at-subscription-level-using-the-help--support-blade"></a>Запрос общие региональные увеличение квоты виртуальных ЦП на уровне с помощью подписки **Справка и поддержка** колонке

Следуйте приведенным ниже инструкциям для создания запроса на обслуживание, доступных на портале Azure по колонке «Справка и поддержка» в Azure. 

1. Из https://portal.azure.com выберите **Справка и поддержка**.

![Справка и поддержка](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  Выберите **Новый запрос в службу поддержки**. 

![Новый запрос на техническую поддержку](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. В раскрывающемся списке Тип проблемы, выберите **ограничения службы и подписки (квоты)** .

![Раскрывающийся список типов проблема](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. Выберите подписку, которая требует увеличенную квоту.

![Выберите newSR подписки](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. Выберите **Other Requests** в **тип квоты** раскрывающегося списка.

![QuotaType](./media/resource-manager-core-quotas-request/regional-quotatype.png)

6. В **сведения** области дополнительной информацией для примера ниже, для упрощения обработки запроса и продолжить создание вариантов. 
    1.  **Модель развертывания** — указать "Resource Manager"
    2.  **Запрошенный регион** — указать на нужный регион, например восточная часть США 2
    3.  **Новое ограничение значение** — ограничить новый регион. Значение не должно превышать сумму утвержденных квоту для отдельного семейства SKU для этой подписки

![QuotaDetails](./media/resource-manager-core-quotas-request/regional-details.png)

## <a name="request-total-regional-vcpus-quota-increase-at-subscription-level-using-the-usages--quota-blade"></a>Запрос общие региональные увеличение квоты виртуальных ЦП на уровне с помощью подписки **использование + Квота** колонке

Следуйте инструкциям ниже, используя для создания запроса на обслуживание с помощью Azure «использование + Квота» доступных на портале Azure. 

1. В https://portal.azure.com выберите **Подписки**.

![Подписки](./media/resource-manager-core-quotas-request/subscriptions.png)

2. Выберите подписку, которая требует увеличенную квоту.

![Выберите подписку.](./media/resource-manager-core-quotas-request/select-subscription.png)

3. Выберите **Использование и квоты**.

![Использование и квоты](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. В правом верхнем углу выберите **Запросить увеличение**.

![Запросить увеличение](./media/resource-manager-core-quotas-request/request-increase.png)

5. Выберите **Other Requests** в **тип квоты** раскрывающегося списка.

![QuotaType](./media/resource-manager-core-quotas-request/regional-quotatype.png)

6. В **сведения** области дополнительной информацией для примера ниже, для упрощения обработки запроса и продолжить создание вариантов. 
    1.  **Модель развертывания** — указать "Resource Manager"
    2.  **Запрошенный регион** — указать на нужный регион, например восточная часть США 2
    3.  **Новое ограничение значение** — ограничить новый регион. Значение не должно превышать сумму утвержденных квоту для отдельного семейства SKU для этой подписки

![QuotaDetails](./media/resource-manager-core-quotas-request/regional-details.png)



