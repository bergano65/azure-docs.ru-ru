---
title: Оповещения системы безопасности для сред в Azure DevTest Labs
description: В этой статье показано, как просмотреть оповещения системы безопасности для среды в DevTest Labs и предпринять соответствующие действия.
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
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588711"
---
# <a name="security-alerts-for-environments-in-azure-devtest-labs"></a>Оповещения системы безопасности для сред в Azure DevTest Labs
Теперь, как пользователь лаборатории, вы можете просматривать оповещения центра безопасности Azure для лабораторных сред. Центр безопасности автоматически собирает, анализирует и объединяет данные журналов, поступающие от ресурсов Azure, сети и подключенных решений партнеров, таких как брандмауэры и решения для защиты конечных точек, для выявления реальных угроз и сокращения ложных срабатываний. Список приоритетных оповещений системы безопасности отображается в центре безопасности вместе с информацией, необходимой для быстрого анализа проблемы, и рекомендациями по устранению атаки. [Дополнительные сведения об оповещениях системы безопасности в центре безопасности Azure](../security-center//security-center-alerts-overview.md).  


## <a name="prerequisites"></a>Предварительные требования
В настоящее время оповещения системы безопасности можно просматривать только для сред "платформа как услуга" (PaaS), развернутых в лаборатории. Чтобы протестировать или использовать эту функцию, [разверните среду в своей лаборатории](devtest-lab-create-environment-from-arm.md). 

## <a name="view-security-alerts-for-an-environment"></a>Просмотр оповещений системы безопасности для среды

1. На домашней странице лаборатории в меню слева выберите **оповещения системы безопасности** . Должно отобразиться число оповещений системы безопасности (высокий, средний и низкий). Узнайте больше о [том, как классифицируются оповещения](../security-center/security-center-alerts-overview.md#how-are-alerts-classified).

    ![Обзор оповещений системы безопасности](./media/environment-security-alerts/security-alerts-overview-page.png)
2. Щелкните правой кнопкой мыши три точки (...) в последнем столбце и выберите **Просмотр оповещений системы безопасности**. 

    ![Просмотр оповещений безопасности](./media/environment-security-alerts/view-security-alerts-menu.png)
    
3. Вы увидите дополнительные сведения о предупреждениях и рекомендациях помощника. Дополнительные сведения об [управлении оповещениями безопасности в центре безопасности Azure и реагировании на них](../security-center/security-center-managing-and-responding-alerts.md).

    ![Просмотр оповещений безопасности](./media/environment-security-alerts/advisor-recommendations.png)


## <a name="next-steps"></a>Следующие шаги
Дополнительные сведения о средах см. в следующих статьях:

- [Создание сред с несколькими виртуальными машинами и ресурсов PaaS с помощью шаблонов Azure Resource Manager](devtest-lab-create-environment-from-arm.md)
- [Настройка и использование общедоступных сред](devtest-lab-configure-use-public-environments.md)
