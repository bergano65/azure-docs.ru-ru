---
title: Настройка оповещений безопасности для ролей ресурсов Azure в PIM — Azure Active Directory | Документация Майкрософт
description: Узнайте, как настроить оповещения системы безопасности для ролей ресурсов Azure AD в Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 827c42763eee39c717cedc90469ae765cc331272
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/28/2019
ms.locfileid: "66253841"
---
# <a name="configure-security-alerts-for-azure-resource-roles-in-pim"></a>Настройка оповещений системы безопасности для ролей ресурсов Azure в PIM
Azure Active Directory (Azure AD) Privileged Identity Management (PIM) создает предупреждения при обнаружении подозрительных или небезопасных действий в вашей среде. Активированные оповещения отображаются на странице "Оповещения". 

![Страница оповещений](media/pim-resource-roles-configure-alerts/rbac-alerts-page.png)

## <a name="review-alerts"></a>Просмотр оповещений
Выберите оповещение, чтобы просмотреть список пользователей или ролей, вызвавших предупреждение, а также рекомендации по исправлению.

![Отчет об оповещении](media/pim-resource-roles-configure-alerts/rbac-alert-info.png)

## <a name="alerts"></a>Оповещения
| Предупреждение | Severity | Триггер | Рекомендации |
| --- | --- | --- | --- |
| **Ресурсу назначено слишком много владельцев** |Средние |Слишком много пользователей имеют роль владельца. |Изучите список пользователей и назначьте некоторым из них менее привилегированные роли. |
| **Ресурсу назначено слишком много постоянных владельцев** |Средние |Слишком много пользователей имеют постоянно назначенную роль. |Изучите список пользователей и для некоторых из них назначьте обязательную активацию для использования этой роли. |
| **Создана дублирующая роль** |Средние |Несколько ролей имеют одинаковые условия. |Используйте только одну из этих ролей. |


### <a name="severity"></a>Severity
* **Высокий**. Требуется немедленное действие из-за нарушения политики. 
* **Средний**. Немедленное действие не требуется, но обнаружено потенциальное нарушение политики.
* **Низкий**. Немедленное действие не требуется, но предлагается рекомендуемое изменение политики.

## <a name="configure-security-alert-settings"></a>Настройка параметров оповещений системы безопасности
Со страницы оповещений перейдите к **параметрам**.
![Параметры](media/pim-resource-roles-configure-alerts/rbac-navigate-settings.png)

Настройте для оповещений параметры, которые подходят для вашей среды и целей безопасности.
![Настройка параметров](media/pim-resource-roles-configure-alerts/rbac-alert-settings.png)

## <a name="next-steps"></a>Дальнейшие действия

- [Настройка параметров роли ресурсов Azure в PIM](pim-resource-roles-configure-role-settings.md)
