---
title: Мы вывыкли классические виртуальные машины Azure 1 марта 2023 г.
description: В статье представлен общий обзор классической выбытия виртуальной машины.
services: virtual-machines
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 02/10/2020
ms.author: tagore
ms.openlocfilehash: e56aa5ec073aadc2a16d53c266d33255a34077cb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80668808"
---
# <a name="migrate-your-iaas-resources-to-azure-resource-manager-by-march-1-2023"></a>Перенос ресурсов IaaS в Azure Resource Manager с 1 марта 2023 г. 

В 2014 мы запустили IaaS на Azure Resource Manager и были усовершенствованы возможности. Поскольку [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) теперь обладает полными возможностями IaaS и другими улучшениями, мы не рекомендуем управлять виртуальными машинами IaaS с помощью Azure Service Manager 28 февраля 2020, и эта функция будет полностью прекращена с 1 марта 2023. 

Сегодня около 90% виртуальных машин IaaS используют Azure Resource Manager. Если вы используете ресурсы IaaS с помощью Azure Service Manager (ASM), начните планировать миграцию сейчас и завершите ее 1 марта 2023, чтобы воспользоваться преимуществами [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/management/).

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

- Дополнительные [сведения](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview) о переносе классических виртуальных машин [Linux](./linux/migration-classic-resource-manager-plan.md) и [Windows](./windows/migration-classic-resource-manager-plan.md) в Azure Resource Manager.

- Дополнительные сведения см. в разделе [часто задаваемые вопросы о классической модели для Azure Resource Manager миграции](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-faq) .

- Для получения технических вопросов, проблем и подписки список разрешений [обратитесь в службу поддержки](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

- Другие вопросы не входят в часто задаваемые вопросы и комментарии, приведенные ниже.
