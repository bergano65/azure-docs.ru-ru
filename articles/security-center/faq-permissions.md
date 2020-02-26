---
title: Вопросы и ответы по центру безопасности Azure — вопросы о разрешениях
description: В этой статье содержатся ответы на вопросы о разрешениях в центре безопасности Azure — продукте, который помогает предотвращать, обнаруживать угрозы и реагировать на них.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: 66a260fbb03f770ee98ec29a5f5e15e3d7dd1310
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77599396"
---
# <a name="permissions"></a>Разрешения

## <a name="how-do-permissions-work-in-azure-security-center"></a>Как работают разрешения в центре безопасности Azure?

Центр безопасности Azure использует [управление доступом на основе ролей (RBAC)](../role-based-access-control/role-assignments-portal.md), в котором предусмотрены [встроенные роли](../role-based-access-control/built-in-roles.md). Эти роли можно назначать пользователям, группам и службам.

Центр безопасности оценивает конфигурацию ресурсов, чтобы выявить проблемы безопасности и уязвимости. В центре безопасности отображается только информация, связанная с ресурсами, для которых вам назначена роль владельца, участника или читателя в рамках подписки или группы ресурсов, к которым принадлежит ресурс.

Подробные сведения о ролях и разрешенных действиях в центре безопасности см. в статье [Permissions in Azure Security Center](security-center-permissions.md) (Разрешения в центре безопасности Azure).



## <a name="who-can-modify-a-security-policy"></a>Кто может изменять политику безопасности?

Изменить политику безопасности может пользователь с правами администратора безопасности, владельца или участника этой подписки.

Дополнительные сведения о настройке политики безопасности см. в статье [Настройка политик безопасности в Центре безопасности Azure](tutorial-security-policy.md).