---
title: Мы увозим в отставку СМ Azure Classic с 1 марта 2023 года
description: Статья обеспечивает высокий уровень обзор Классический VM выхода на пенсию
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
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668808"
---
# <a name="migrate-your-iaas-resources-to-azure-resource-manager-by-march-1-2023"></a>Переимите ресурсы IaaS в отдел ресурсов Azure до 1 марта 2023 года 

В 2014 году мы запустили IaaS на ресурсном менеджере Azure и с тех пор расширяем возможности. Поскольку [менеджер ресурсов Azure](https://azure.microsoft.com/features/resource-manager/) теперь имеет полные возможности IaaS и другие достижения, мы устранили управление IaaS VMs через менеджер службы Azure 28 февраля 2020 года, и эта функциональность будет полностью удалена 1 марта 2023 года. 

Сегодня около 90% IaaS VMs используют менеджер ресурсов Azure. Если вы используете ресурсы IaaS через менеджер службы Azure (ASM), начните планировать миграцию уже сейчас и завершите ее до 1 марта 2023 года, чтобы воспользоваться преимуществами [менеджера ресурсов Azure.](https://docs.microsoft.com/azure/azure-resource-manager/management/)

Классические ВМ будут следовать [современной политики жизненного цикла](https://support.microsoft.com/help/30881/modern-lifecycle-policy) для выхода на пенсию.

## <a name="how-does-this-affect-me"></a>Как это повлияет на работу? 

1) Начиная с 28 февраля 2020 года, клиенты, которые не использовали IaaS VMs через Azure Service Manager (ASM) в феврале 2020 года, больше не смогут создавать классические ВМ. 
2) С 1 марта 2023 года клиенты больше не смогут запускать IAaS-вМ с помощью Azure Service Manager, и все, что все еще работает или выделяется, будут остановлены и локализованы. 
2) 1 марта 2023 года подписки, не перебравшиеся в Azure Resource Manager, будут проинформированы о сроках для удаляния оставшихся классических ВМ.  

Следующие службы и функции Azure **НЕ** будут затронуты этим выходом на пенсию: 
- Облачные службы 
- Учетные записи **хранения, не** используемые классическими VMs 
- Виртуальные сети (VNets) **не** используются классическими виртуальными системами. 
- Другие классические ресурсы

## <a name="what-actions-should-i-take"></a>Какие действия я должен предпринять? 

- Начните планировать миграцию на менеджер ресурсов Azure уже сегодня. 

- [Узнайте больше](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview) о переходе классических [Linux](./linux/migration-classic-resource-manager-plan.md) и [Windows](./windows/migration-classic-resource-manager-plan.md) VMs в Azure Resource Manager.

- Для получения дополнительной информации обратитесь к [часто задаваемым вопросам о классической технологии для миграции диспетчера ресурсов Azure](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-faq)

- По техническим вопросам, вопросам и подписке белый список [контактной поддержки.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

- Для других вопросов, не являются частью часто задаваемых вопросов и обратной связи, комментарий ниже.
