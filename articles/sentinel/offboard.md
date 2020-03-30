---
title: Удалить Azure Sentinel Документы Майкрософт
description: Как удалить экземпляр Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/29/2019
ms.author: yelevin
ms.openlocfilehash: 4042f7b9d0f6fdf293f338c89d5783fe266c4edf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77581690"
---
# <a name="remove-azure-sentinel-from-your-workspace"></a>Удалить Azure Sentinel из рабочего пространства

Если вы больше не хотите использовать Azure Sentinel, в этой статье объясняется, как удалить его из рабочего пространства.

## <a name="how-to-remove-azure-sentinel"></a>Как удалить Azure Sentinel

Следите за этим процессом, чтобы удалить Azure Sentinel из рабочей области:

1. Перейдите в **Azure Sentinel**, затем **настройки**и выберите вкладку **Удалить Azure Sentinel**.

1. Перед удалением Azure Sentinel, пожалуйста, используйте флажки, чтобы сообщить нам, почему вы удалите его.

1. Выберите **Удалить Azure Sentinel из рабочего пространства.**
    
    ![Удаление решения SecurityInsights](media/offboard/delete-solution.png)

## <a name="what-happens-behind-the-scenes"></a>Что происходит за кулисами?

При удалении решения Azure Sentinel требуется до 48 часов, чтобы завершить первую фазу процесса удаления.

После идентификации отключения начинается процесс входного сообщения.

**Конфигурация этих разъемов удаляется:**
-   Office 365

-   AWS

-   Оповещения службы безопасности служб майкрософт (Azure ATP, Microsoft Cloud App Security, включая ИТ-отчетность Cloud Discovery Shadow, Azure AD Identity Protection, Microsoft Defender ATP, Центр безопасности Azure)

-   Аналитика угроз

-   Общие журналы безопасности (включая журналы ceF, Barracuda и Syslog) (Если у вас есть Центр безопасности Azure, эти журналы будут по-прежнему собираться).

-   События безопасности Windows (Если у вас есть Центр безопасности Azure, эти журналы будут по-прежнему собираться).

В течение первых 48 часов данные и аналитические правила (включая конфигурацию автоматизации в режиме реального времени) больше не будут доступны или доступны в Azure Sentinel.

**Через 30 дней эти ресурсы удаляются:**

-   Инциденты (включая метаданные расследования)

-   Аналитические правила

-   Закладки

Ваши игровые книжки, сохраненные трудовые книжки, сохраненные охотничьи запросы и тетради не удаляются. **Некоторые из них могут сломаться из-за удаленных данных. Вы можете удалить их вручную.**

После удаления службы существует льготный период в 30 дней, в течение которого можно повторно включить решение, и ваши данные и аналитические правила будут восстановлены, но настроенные разъемы, которые были отключены, должны быть восстановлены.

> [!NOTE]
> Если вы удалите решение, ваша подписка будет по-прежнему зарегистрирована в ресурсе Azure Sentinel. **Вы можете удалить его вручную.**




## <a name="next-steps"></a>Дальнейшие действия
В этом документе вы узнали, как удалить службу Azure Sentinel. Если вы передумаете и хотите установить его снова:
- Начало [работы на борту Azure Sentinel](quickstart-onboard.md).
