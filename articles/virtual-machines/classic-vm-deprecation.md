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
ms.openlocfilehash: 0b2b995a6fe4cedd14b2e4ceeddc5747ec2423cf
ms.sourcegitcommit: afa1411c3fb2084cccc4262860aab4f0b5c994ef
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/23/2020
ms.locfileid: "88754809"
---
# <a name="migrate-your-iaas-resources-to-azure-resource-manager-by-march-1-2023"></a>Перенос ресурсов IaaS в Azure Resource Manager с 1 марта 2023 г. 

В 2014 мы запустили IaaS на Azure Resource Manager и были усовершенствованы возможности. Поскольку [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) теперь обладает полными возможностями IaaS и другими улучшениями, мы не рекомендуем управлять виртуальными машинами IaaS с помощью [Azure Service Manager](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-faq#what-is-azure-service-manager-and-what-does-it-mean-by-classic) 28 февраля 2020, и эта функция будет полностью прекращена с 1 марта 2023. 

Сегодня около 90% виртуальных машин IaaS используют Azure Resource Manager. Если вы используете ресурсы IaaS с помощью Azure Service Manager (ASM), начните планировать миграцию сейчас и завершите ее 1 марта 2023, чтобы воспользоваться преимуществами [Azure Resource Manager](../azure-resource-manager/management/index.yml).

Классические виртуальные машины будут следовать [современным политикам жизненного цикла](https://support.microsoft.com/help/30881/modern-lifecycle-policy) для выхода из эксплуатации.

## <a name="how-does-this-affect-me"></a>Как это повлияет на работу? 

- Начиная с 28 февраля 2020, клиенты, которые не использовали виртуальные машины IaaS в Azure Service Manager (ASM) в течение февраля 2020, больше не смогут создавать классические виртуальные машины. 
- С 1 марта 2023 г. клиенты больше не смогут запускать виртуальные машины IaaS с помощью Azure Service Manager. все, что еще выполняется или выделено, будут остановлены и освобождены. 
- В 1 марта 2023 подписки, которые не перенесены в Azure Resource Manager, будут уведомлены о временных шкалах для удаления оставшихся классических виртуальных машин.  

Следующие службы и функции Azure **не** будут затронуты этим прекращением работы: 
- Облачные службы 
- Учетные записи хранения, **не** используемые классическими виртуальными машинами 
- Виртуальные сети (виртуальных сетей) **не** используются классическими виртуальными машинами. 
- Другие классические ресурсы

## <a name="what-actions-should-i-take"></a>Какие действия следует предпринять? 

- Приступите к планированию перехода на Azure Resource Manager сегодня. 

- Создайте список всех затронутых виртуальных машин. Виртуальная машина типа "виртуальные машины (классические)" в [колонке виртуальной машины портала Azure](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ClassicCompute%2FVirtualMachines) — это все затронутые виртуальные машины в подписке. 

- Дополнительные [сведения](./windows/migration-classic-resource-manager-overview.md) о переносе классических виртуальных машин [Linux](./linux/migration-classic-resource-manager-plan.md) и [Windows](./windows/migration-classic-resource-manager-plan.md) в Azure Resource Manager.

- Дополнительные сведения см. в разделе [часто задаваемые вопросы о классической модели для Azure Resource Manager миграции](./migration-classic-resource-manager-faq.md) .

- Для получения технических вопросов, проблем и добавления подписок в список разрешений [обратитесь в службу поддержки](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/{"pesId":"6f16735c-b0ae-b275-ad3a-03479cfa1396","supportTopicId":"8a82f77d-c3ab-7b08-d915-776b4ff64ff4"}).

- Другие вопросы не входят в часто задаваемые вопросы и комментарии, приведенные ниже.

- Выполните миграцию как можно скорее, чтобы предотвратить влияние на бизнес и повысить производительность, безопасность & новых функций, предоставляемых Azure Resource Manager. 

## <a name="what-resources-are-provided-to-me-for-this-migration"></a>Какие ресурсы предоставляются мне для этой миграции?

- [Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-virtual-machines-migration.html): Поддержка миграции в сообщество Microsoft &

- [Поддержка миграции Azure](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/{"pesId":"6f16735c-b0ae-b275-ad3a-03479cfa1396","supportTopicId":"1135e3d0-20e2-aec5-4ef0-55fd3dae2d58"}): Специальная группа поддержки для технической помощи во время миграции

- [Microsoft Fast Track](https://www.microsoft.com/fasttrack): группа Microsoft Fast Track может предоставить техническую поддержку во время миграции на соответствующих клиентах. 

- Если ваша компания или организация взаимодействуют с представителем корпорации Майкрософт и (или) с такими представителями Майкрософт, как (архитектор облачных решений (CSA), диспетчеры технических учетных записей (ТАМС)), обратитесь к ним за дополнительными ресурсами для миграции. 

