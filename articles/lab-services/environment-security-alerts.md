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
ms.openlocfilehash: 4ca17bece33107de756eb221e14eaab851660a99
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/04/2020
ms.locfileid: "76992239"
---
# <a name="security-alerts-for-environments-in-azure-devtest-labs"></a>Оповещения системы безопасности для сред в Azure DevTest Labs
В этой статье показано, как просматривать оповещения системы безопасности для сред в Azure DevTest Labs. 

## <a name="prerequisites"></a>Технические условия
В настоящее время оповещения системы безопасности можно просматривать только для сред, развернутых в лаборатории. Чтобы протестировать или использовать эту функцию, разверните среду в своей лаборатории. 

## <a name="view-security-alerts-for-an-environment"></a>Просмотр оповещений системы безопасности для среды

1. На домашней странице лаборатории в меню слева выберите **оповещения системы безопасности** . Должно отобразиться число оповещений системы безопасности (высокий, средний и низкий).

    ![Обзор оповещений системы безопасности](./media/environment-security-alerts/security-alerts-overview-page.png)
2. Щелкните правой кнопкой мыши три точки (...) в последнем столбце и выберите **Просмотр оповещений системы безопасности**. 

    ![Просмотр оповещений безопасности](./media/environment-security-alerts/view-security-alerts-menu.png)
3. Вы увидите дополнительные сведения о предупреждениях и рекомендациях помощника. 

    ![Просмотр оповещений безопасности](./media/environment-security-alerts/advisor-recommendations.png)


## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о средах см. в следующих статьях:

- [Создание сред с несколькими виртуальными машинами и ресурсов PaaS с помощью шаблонов Azure Resource Manager](devtest-lab-create-environment-from-arm.md)
- [Настройка и использование общедоступных сред](devtest-lab-configure-use-public-environments.md)
