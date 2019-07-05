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
ms.openlocfilehash: 3c7ce8b79644a9ffc9481ba825ec5623a9268983
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476336"
---
# <a name="configure-security-alerts-for-azure-resource-roles-in-pim"></a>Настройка оповещений системы безопасности для ролей ресурсов Azure в PIM
Azure Active Directory (Azure AD) Privileged Identity Management (PIM) создает предупреждения при обнаружении подозрительных или небезопасных действий в вашей среде. Активированные оповещения отображаются на странице "Оповещения". 

![Ресурсов Azure — список оповещений, уровень риска и число странице "оповещения"](media/pim-resource-roles-configure-alerts/rbac-alerts-page.png)

## <a name="review-alerts"></a>Просмотр оповещений
Выберите оповещение, чтобы просмотреть список пользователей или ролей, вызвавших предупреждение, а также рекомендации по исправлению.

![Отчет о предупреждениях, показывающий последнее сканирование времени, описание, действия по устранению рисков, тип, уровень серьезности, влияние на безопасность и предотвращение при очередном](media/pim-resource-roles-configure-alerts/rbac-alert-info.png)

## <a name="alerts"></a>Оповещения
| Предупреждение | severity | Триггер | Рекомендации |
| --- | --- | --- | --- |
| **Ресурсу назначено слишком много владельцев** |Средние |Слишком много пользователей имеют роль владельца. |Изучите список пользователей и назначьте некоторым из них менее привилегированные роли. |
| **Ресурсу назначено слишком много постоянных владельцев** |Средние |Слишком много пользователей имеют постоянно назначенную роль. |Изучите список пользователей и для некоторых из них назначьте обязательную активацию для использования этой роли. |
| **Создана дублирующая роль** |Средние |Несколько ролей имеют одинаковые условия. |Используйте только одну из этих ролей. |


### <a name="severity"></a>severity
* **Высокий**. Требуется немедленное действие из-за нарушения политики. 
* **Средний**. Немедленное действие не требуется, но обнаружено потенциальное нарушение политики.
* **Низкий**. Немедленное действие не требуется, но предлагается рекомендуемое изменение политики.

## <a name="configure-security-alert-settings"></a>Настройка параметров оповещений системы безопасности
Со страницы оповещений перейдите к **параметрам**.

![Странице "оповещения" с выделенным параметрами](media/pim-resource-roles-configure-alerts/rbac-navigate-settings.png)

Настройте для оповещений параметры, которые подходят для вашей среды и целей безопасности.

![Настройка страницы включить и настроить параметры оповещения](media/pim-resource-roles-configure-alerts/rbac-alert-settings.png)

## <a name="next-steps"></a>Дальнейшие действия

- [Настройка параметров роли ресурсов Azure в PIM](pim-resource-roles-configure-role-settings.md)
