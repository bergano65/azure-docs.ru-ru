---
title: Предупреждения о безопасности для сред в Лабораториях Azure DevTest
description: В этой статье показано, как просматривать оповещения безопасности для среды в DevTest Labs и принять соответствующие меры.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2020
ms.author: spelluru
ms.openlocfilehash: fbac5a2fab91cdac8ebf626e324f12f209cfade5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588711"
---
# <a name="security-alerts-for-environments-in-azure-devtest-labs"></a>Предупреждения о безопасности для сред в Лабораториях Azure DevTest
Теперь в качестве пользователя лаборатории вы можете просматривать оповещения Центра безопасности Azure для лабораторных сред. Центр безопасности автоматически собирает, анализирует и объединяет данные журналов, поступающие от ресурсов Azure, сети и подключенных решений партнеров, таких как брандмауэры и решения для защиты конечных точек, для выявления реальных угроз и сокращения ложных срабатываний. Список приоритетных оповещений системы безопасности отображается в центре безопасности вместе с информацией, необходимой для быстрого анализа проблемы, и рекомендациями по устранению атаки. [Подробнее о предупреждениях безопасности в Центре безопасности Azure узнайте](../security-center//security-center-alerts-overview.md)больше.  


## <a name="prerequisites"></a>Предварительные требования
В настоящее время можно просматривать оповещения безопасности только для платформы как среды службы (PaaS), развернутые в вашей лаборатории. Чтобы протестировать или использовать эту функцию, [разместите среду в вашей лаборатории.](devtest-lab-create-environment-from-arm.md) 

## <a name="view-security-alerts-for-an-environment"></a>Просмотр предупреждений безопасности для среды

1. На главной странице лаборатории выберите **оповещения безопасности** в левом меню. Вы должны увидеть количество предупреждений безопасности (высокое, среднее и низкое). Узнайте больше о [том, как классифицируются оповещения.](../security-center/security-center-alerts-overview.md#how-are-alerts-classified)

    ![Предупреждения о безопасности - обзор](./media/environment-security-alerts/security-alerts-overview-page.png)
2. Нажмите на три точки (...) в последнем столбце и выберите **оповещения о безопасности View.** 

    ![Просмотр оповещений безопасности](./media/environment-security-alerts/view-security-alerts-menu.png)
    
3. Вы увидите более подробную информацию о предупреждениях и рекомендациях консультантов. Подробнее об [управлении и реагировании на оповещения безопасности в Центре безопасности Azure.](../security-center/security-center-managing-and-responding-alerts.md)

    ![Просмотр оповещений безопасности](./media/environment-security-alerts/advisor-recommendations.png)


## <a name="next-steps"></a>Дальнейшие действия
Чтобы узнать больше о средах, смотрите следующие статьи:

- [Создание мульти-Vm-сред и ресурсов PaaS с помощью шаблонов управления ресурсами Azure](devtest-lab-create-environment-from-arm.md)
- [Настройка и использование общих сред](devtest-lab-configure-use-public-environments.md)
