---
title: Виртуальные машины Azure (классическая модель) будут высняты с 1 марта 2023 г.
description: В этой статье представлен общий обзор выбытия виртуальных машин, созданных с помощью классической модели развертывания.
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: tagore
ms.openlocfilehash: 7f2db507176d65e7794607e83db8605b2f892c1c
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/10/2020
ms.locfileid: "89646625"
---
# <a name="migrate-your-iaas-resources-to-azure-resource-manager-by-march-1-2023"></a>Перенос ресурсов IaaS в Azure Resource Manager с 1 марта 2023 г. 

В 2014 мы запустили инфраструктуру как услугу (IaaS) на [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/). Мы с легкостью улучшаем возможности. Поскольку Azure Resource Manager теперь обладает полными возможностями IaaS и другими улучшениями, мы не рекомендуем управлять виртуальными машинами IaaS с помощью [Azure Service Manager](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-faq#what-is-azure-service-manager-and-what-does-it-mean-by-classic) (ASM) 28 февраля 2020. Эта функция будет полностью прекращена 1 марта 2023 г. 

Сегодня около 90% виртуальных машин IaaS используют Azure Resource Manager. Если вы используете ресурсы IaaS через ASM, начните планировать миграцию прямо сейчас. Заполните ее 1 марта 2023, чтобы воспользоваться преимуществами [Azure Resource Manager](../azure-resource-manager/management/index.yml).

Виртуальные машины, созданные с помощью классической модели развертывания, будут следовать [современным политикам жизненного цикла](https://support.microsoft.com/help/30881/modern-lifecycle-policy) для выхода из эксплуатации.

## <a name="how-does-this-affect-me"></a>Как это повлияет на работу? 

- По состоянию на 28 февраля 2020 клиенты, которые не использовали виртуальные машины IaaS через ASM в феврале 2020 февраля, больше не могут создавать виртуальные машины (классические). 
- С 1 марта 2023 г. клиенты больше не смогут запускать виртуальные машины IaaS с помощью ASM. Все, что все еще выполняется или выделено, будут остановлены и освобождены. 
- В 1 марта 2023 подписки, которые не перенесены в Azure Resource Manager, будут уведомлены о временных шкалах для удаления оставшихся виртуальных машин (классическая модель).  

Эта выбытие *не* влияет на следующие службы и функции Azure: 
- Oблачныe службы Azure 
- Учетные записи хранения, *не* используемые виртуальными машинами (классическая модель) 
- Виртуальные сети, *не* используемые виртуальными машинами (классическая модель) 
- Другие классические ресурсы

## <a name="what-actions-should-i-take"></a>Какие действия следует предпринять? 

Приступите к планированию перехода на Azure Resource Manager сегодня. 

1. Создайте список всех затронутых виртуальных машин. 

   - Виртуальные машины типа " **виртуальные машины" (классические)** на [панели виртуальных машин портал Azure](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ClassicCompute%2FVirtualMachines) — это все затронутые виртуальные машины в рамках подписки. 
   - Вы также можете запросить граф ресурсов Azure с помощью [портала](https://portal.azure.com/#blade/HubsExtension/ArgQueryBlade/query/resources%0A%7C%20where%20type%20%3D%3D%20%22microsoft.classiccompute%2Fvirtualmachines%22) или [PowerShell](https://docs.microsoft.com/azure/governance/resource-graph/concepts/work-with-data) , чтобы просмотреть список всех отмеченных виртуальных машин (классическая модель) и связанные сведения для выбранных подписок. 
   - 8 февраля и 2 сентября 2020 г. Мы отправили сообщения электронной почты владельцам подписки со списком всех подписок, содержащих эти виртуальные машины (классическая модель). Используйте их для создания этого списка. 

1. Дополнительные [сведения](./windows/migration-classic-resource-manager-overview.md) о миграции виртуальных машин [Linux](./linux/migration-classic-resource-manager-plan.md) и [Windows](./windows/migration-classic-resource-manager-plan.md) (классическая модель) в Azure Resource Manager. Дополнительные сведения см. [в разделе часто задаваемые вопросы о классической модели для Azure Resource Manager миграции](./migration-classic-resource-manager-faq.md).

1. Мы рекомендуем начать планирование с помощью [средства миграции платформы поддержка платформ](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview) , чтобы перенести существующие виртуальные машины с помощью трех простых шагов: Проверка, подготовка и фиксация. Это средство предназначено для переноса виртуальных машин в минимальной степени без простоев. 

   1. Первый шаг, проверка, не влияет на существующее развертывание и предоставляет список всех неподдерживаемых сценариев для миграции. 
   1. Просмотрите [список обходных путей](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview#unsupported-features-and-configurations) , чтобы исправить развертывание и подготовить его к миграции. 
   1. В идеале после устранения всех ошибок проверки не следует столкнуться с проблемами во время подготовки и фиксации. После успешной фиксации развертывание динамически переносится в Azure Resource Manager и затем может управляться через новые интерфейсы API, предоставляемые Azure Resource Manager. 

   Если средство миграции не подходит для миграции, можно изучить [другие предложения вычислений](https://docs.microsoft.com/azure/architecture/guide/technology-choices/compute-decision-tree) для миграции. Так как существует много предложений вычислений Azure и они отличаются друг от друга, мы не можем предоставить поддерживаемый платформой путь к миграции.  

1. Технические вопросы, проблемы и помощь по добавлению подписок в список разрешений обратитесь в [службу поддержки](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/{"pesId":"6f16735c-b0ae-b275-ad3a-03479cfa1396","supportTopicId":"8a82f77d-c3ab-7b08-d915-776b4ff64ff4"}).

1. Выполните миграцию как можно скорее, чтобы предотвратить влияние на бизнес и воспользоваться преимуществами улучшенной производительности, безопасности и новых функций Azure Resource Manager. 

## <a name="what-resources-are-available-for-this-migration"></a>Какие ресурсы доступны для этой миграции?

- [Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-virtual-machines-migration.html). Поддержка миграции корпорацией Майкрософт и сообществом.

- [Поддержка миграции Azure](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/{"pesId":"6f16735c-b0ae-b275-ad3a-03479cfa1396","supportTopicId":"1135e3d0-20e2-aec5-4ef0-55fd3dae2d58"}): Специальная группа поддержки для технической помощи во время миграции.

- [Microsoft Fast Track](https://www.microsoft.com/fasttrack): группа, которая может предоставить техническую поддержку во время миграции на соответствующих клиентах. 

Если ваша компания или организация взаимодействуют с корпорацией Майкрософт или работают с представителями корпорации Майкрософт (например, архитекторами облачных решений (КСАС) или диспетчерами технических учетных записей (ТАМС)), обратитесь к ним за дополнительными ресурсами для миграции. 

