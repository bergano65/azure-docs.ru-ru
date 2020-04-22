---
title: Лучшие практики для Azure RBAC
description: Рекомендации по использованию элемента управления доступом на основе ролей Azure (Azure RBAC).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/17/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: dc86dd488ff9e8649ae80f4768941791dd37fce6
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726778"
---
# <a name="best-practices-for-azure-rbac"></a>Лучшие практики для Azure RBAC

В этой статье описаны некоторые рекомендации по использованию элемента управления доступом на основе ролей Azure (Azure RBAC). Эти рекомендации основаны на нашем опыте работы с Azure RBAC и опыте таких клиентов, как вы.

## <a name="only-grant-the-access-users-need"></a>Только предоставить пользователям доступ

Используя Azure RBAC, вы можете сегрегировать обязанности внутри вашей команды и предоставлять только тот объем доступа к пользователям, который им необходим для выполнения их работы. Вместо того чтобы предоставить всем неограниченные разрешения для подписки Azure или ресурсов, можно разрешить только определенные действия в конкретной области.

Когда вы разрабатываете стратегию управления доступом, предоставьте пользователям минимальные разрешения, чтобы они могли выполнять свои задачи. На следующей схеме представлен рекомендуемый шаблон для использования ролей RBAC.

![RBAC и минимальные привилегии](./media/best-practices/rbac-least-privilege.png)

Для получения информации о том, как добавить назначения ролей, [см.](role-assignments-portal.md)

## <a name="limit-the-number-of-subscription-owners"></a>Ограничьте число владельцев подписок

Вы должны иметь максимум 3 владельцев подписки, чтобы уменьшить вероятность нарушения со стороны скомпрометированного владельца. Эта рекомендация может быть проверена в Центре безопасности Azure. Для других рекомендаций по идентификации и доступу в Центре [безопасности](../security-center/recommendations-reference.md)см.

## <a name="use-azure-ad-privileged-identity-management"></a>Используйте управление привилегированными удостоверениями ИТ-данных Azure AD

Для защиты привилегированных учетных записей от вредоносных кибератак можно использовать технологию Azure Active Directory Privileged Identity Management (PIM), чтобы уменьшить продолжительность действия привилегий и повысить вашу осведомленность об их использовании с помощью отчетов и оповещений. PIM помогает защитить привилегированные учетные записи, предоставляя только в свое время привилегированный доступ к ресурсам Azure AD и Azure. Доступ может быть ограничен по времени, после которого привилегии автоматически отменяются. 

Дополнительные сведения см. в статье [Что такое Azure AD Privileged Identity Management?](../active-directory/privileged-identity-management/pim-configure.md)

## <a name="next-steps"></a>Следующие шаги

- [Устранение неполадок Azure RBAC](troubleshooting.md)