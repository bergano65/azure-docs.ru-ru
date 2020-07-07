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
ms.openlocfilehash: 053e86f3493c7a11a3cbbaad0871e45345697878
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "82735340"
---
# <a name="best-practices-for-azure-rbac"></a>Рекомендации по использованию Azure RBAC

В этой статье описываются некоторые рекомендации по использованию управления доступом на основе ролей в Azure (Azure RBAC). Эти рекомендации являются производными от нашего опыта работы с Azure RBAC и опытом работы клиентов.

## <a name="only-grant-the-access-users-need"></a>Предоставляйте доступ только пользователям Access

С помощью Azure RBAC вы можете распределить обязанности внутри команды и предоставить пользователям доступ на уровне, который им необходим для выполнения поставленных задач. Вместо того чтобы предоставить всем неограниченные разрешения для подписки Azure или ресурсов, можно разрешить только определенные действия в конкретной области.

Когда вы разрабатываете стратегию управления доступом, предоставьте пользователям минимальные разрешения, чтобы они могли выполнять свои задачи. На следующей схеме показан предлагаемый шаблон для использования Azure RBAC.

![Azure RBAC и наименьшие права доступа](./media/best-practices/rbac-least-privilege.png)

Дополнительные сведения о добавлении назначений ролей см. [в разделе Добавление или удаление назначений ролей Azure с помощью портал Azure](role-assignments-portal.md).

## <a name="limit-the-number-of-subscription-owners"></a>Ограничение числа владельцев подписки

У вас должно быть не более трех владельцев подписок, чтобы снизить вероятность нарушения защиты от скомпрометированного владельца. Эту рекомендацию можно отслеживать в центре безопасности Azure. Другие рекомендации по удостоверениям и доступу в центре безопасности см. [в разделе рекомендации по безопасности — справочное руководство](../security-center/recommendations-reference.md).

## <a name="use-azure-ad-privileged-identity-management"></a>Использование Azure AD Privileged Identity Management

Для защиты привилегированных учетных записей от вредоносных кибератак можно использовать технологию Azure Active Directory Privileged Identity Management (PIM), чтобы уменьшить продолжительность действия привилегий и повысить вашу осведомленность об их использовании с помощью отчетов и оповещений. PIM помогает защитить привилегированные учетные записи, предоставляя доступ в Azure AD и ресурсам Azure с правами на уровне пользователей. Доступ может быть ограничен временем, после которого права отзываются автоматически. 

Дополнительные сведения см. в статье [Что такое Azure AD Privileged Identity Management?](../active-directory/privileged-identity-management/pim-configure.md)

## <a name="next-steps"></a>Дальнейшие действия

- [Устранение неполадок в Azure RBAC](troubleshooting.md)