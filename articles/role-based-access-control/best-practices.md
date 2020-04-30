---
title: Рекомендации по использованию Azure RBAC
description: Рекомендации по использованию управления доступом на основе ролей Azure (Azure RBAC).
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81726778"
---
# <a name="best-practices-for-azure-rbac"></a>Рекомендации по использованию Azure RBAC

В этой статье описываются некоторые рекомендации по использованию управления доступом на основе ролей в Azure (Azure RBAC). Эти рекомендации являются производными от нашего опыта работы с Azure RBAC и опытом работы клиентов.

## <a name="only-grant-the-access-users-need"></a>Предоставляйте доступ только пользователям Access

С помощью Azure RBAC вы можете разделить обязанности внутри группы и предоставить пользователям доступ только для тех пользователей, которые им необходимы для выполнения своих задач. Вместо того чтобы предоставить всем неограниченные разрешения для подписки Azure или ресурсов, можно разрешить только определенные действия в конкретной области.

Когда вы разрабатываете стратегию управления доступом, предоставьте пользователям минимальные разрешения, чтобы они могли выполнять свои задачи. На следующей схеме представлен рекомендуемый шаблон для использования ролей RBAC.

![RBAC и минимальные привилегии](./media/best-practices/rbac-least-privilege.png)

Дополнительные сведения о добавлении назначений ролей см. в разделе [Добавление и удаление назначений ролей](role-assignments-portal.md).

## <a name="limit-the-number-of-subscription-owners"></a>Ограничение числа владельцев подписки

У вас должно быть не более трех владельцев подписок, чтобы снизить вероятность нарушения защиты от скомпрометированного владельца. Эту рекомендацию можно отслеживать в центре безопасности Azure. Другие рекомендации по удостоверениям и доступу в центре безопасности см. [в разделе рекомендации по безопасности — справочное руководство](../security-center/recommendations-reference.md).

## <a name="use-azure-ad-privileged-identity-management"></a>Использование Azure AD Privileged Identity Management

Для защиты привилегированных учетных записей от вредоносных кибератак можно использовать технологию Azure Active Directory Privileged Identity Management (PIM), чтобы уменьшить продолжительность действия привилегий и повысить вашу осведомленность об их использовании с помощью отчетов и оповещений. PIM помогает защитить привилегированные учетные записи, предоставляя доступ в Azure AD и ресурсам Azure с правами на уровне пользователей. Доступ может быть ограничен временем, после которого права отзываются автоматически. 

Дополнительные сведения см. в статье [Что такое Azure AD Privileged Identity Management?](../active-directory/privileged-identity-management/pim-configure.md)

## <a name="next-steps"></a>Дальнейшие шаги

- [Устранение неполадок в Azure RBAC](troubleshooting.md)