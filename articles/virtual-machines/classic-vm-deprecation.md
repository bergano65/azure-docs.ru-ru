---
title: Мы вывыкли классические виртуальные машины Azure 1 марта 2023 г.
description: В статье представлен общий обзор классической выбытия виртуальной машины.
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: tagore
ms.openlocfilehash: d86805975b082136879c0a98ce2817f4f491a9a0
ms.sourcegitcommit: f988fc0f13266cea6e86ce618f2b511ce69bbb96
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/31/2020
ms.locfileid: "87461210"
---
# <a name="migrate-your-iaas-resources-to-azure-resource-manager-by-march-1-2023"></a>Перенос ресурсов IaaS в Azure Resource Manager с 1 марта 2023 г. 

В 2014 мы запустили IaaS на Azure Resource Manager и были усовершенствованы возможности. Поскольку [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) теперь обладает полными возможностями IaaS и другими улучшениями, мы не рекомендуем управлять виртуальными машинами IaaS с помощью Azure Service Manager 28 февраля 2020, и эта функция будет полностью прекращена с 1 марта 2023. 

Сегодня около 90% виртуальных машин IaaS используют Azure Resource Manager. Если вы используете ресурсы IaaS с помощью Azure Service Manager (ASM), начните планировать миграцию сейчас и завершите ее 1 марта 2023, чтобы воспользоваться преимуществами [Azure Resource Manager](../azure-resource-manager/management/index.yml).

Классические виртуальные машины будут следовать [современным политикам жизненного цикла](https://support.microsoft.com/help/30881/modern-lifecycle-policy) для выхода из эксплуатации.

## <a name="how-does-this-affect-me"></a>Как это повлияет на работу? 

1) Начиная с 28 февраля 2020, клиенты, которые не использовали виртуальные машины IaaS в Azure Service Manager (ASM) в течение февраля 2020, больше не смогут создавать классические виртуальные машины. 
2) С 1 марта 2023 г. клиенты больше не смогут запускать виртуальные машины IaaS с помощью Azure Service Manager. все, что еще выполняется или выделено, будут остановлены и освобождены. 
2) В 1 марта 2023 подписки, которые не были перенесены в Azure Resource Manager, будут уведомлены о временных шкалах для удаления оставшихся классических виртуальных машин.  

Следующие службы и функции Azure **не** будут затронуты этим прекращением работы: 
- Облачные службы 
- Учетные записи хранения, **не** используемые классическими виртуальными машинами 
- Виртуальные сети (виртуальных сетей) **не** используются классическими виртуальными машинами. 
- Другие классические ресурсы

## <a name="what-actions-should-i-take"></a>Какие действия следует предпринять? 

- Приступите к планированию перехода на Azure Resource Manager сегодня. 

- Дополнительные [сведения](./windows/migration-classic-resource-manager-overview.md) о переносе классических виртуальных машин [Linux](./linux/migration-classic-resource-manager-plan.md) и [Windows](./windows/migration-classic-resource-manager-plan.md) в Azure Resource Manager.

- Дополнительные сведения см. в разделе [часто задаваемые вопросы о классической модели для Azure Resource Manager миграции](./windows/migration-classic-resource-manager-faq.md) .

- Для получения технических вопросов, проблем и добавления подписок в список разрешений [обратитесь в службу поддержки](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/{"pesId":"6f16735c-b0ae-b275-ad3a-03479cfa1396","supportTopicId":"8a82f77d-c3ab-7b08-d915-776b4ff64ff4"}).

- За помощью в процессе миграции [обратитесь в службу поддержки миграции](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/{"pesId":"6f16735c-b0ae-b275-ad3a-03479cfa1396","supportTopicId":"1135e3d0-20e2-aec5-4ef0-55fd3dae2d58"}) .

- Другие вопросы не входят в часто задаваемые вопросы и комментарии, приведенные ниже.
