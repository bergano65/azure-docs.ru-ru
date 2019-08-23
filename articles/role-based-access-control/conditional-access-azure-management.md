---
title: Управление доступом к управлению Azure с помощью условного доступа в Azure Active Directory
description: Сведения об использовании условного доступа в Azure AD для управления доступом к управлению Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: skwan
ms.assetid: 0adc8b11-884e-476c-8c43-84f9bf12a34b
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/15/2019
ms.author: rolyon
ms.reviewer: skwan
ms.openlocfilehash: d4036b2aaa59eeb8e38a5881e465031d7a2f15e3
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/22/2019
ms.locfileid: "69899722"
---
# <a name="manage-access-to-azure-management-with-conditional-access"></a>Управление доступом к управлению Azure с помощью условного доступа

Условный доступ в Azure Active Directory (Azure AD) управляет доступом к облачным приложениям на основе указанных вами условий. Чтобы разрешить доступ, создайте политики условного доступа, разрешающие или блокирующие доступ в зависимости от того, соблюдены ли требования в политике. 

Как правило, условный доступ используется для управления доступом к облачным приложениям. Вы также можете настроить политики для управления доступом к функциям управления Azure на основе определенных условий (таких как риск при входе, расположение или устройство) и для обеспечения соблюдения требований, таких как многофакторная проверка подлинности.

Чтобы создать политику для управления Azure, выберите **Microsoft Azure Management** (Управление Microsoft Azure) в области **Облачные приложения** при выборе приложения, к которому применяется политика.

![Условный доступ для управления Azure](./media/conditional-access-azure-management/conditional-access-azure-mgmt.png)

Создаваемая политика применяется ко всем конечным точкам управления Azure, включая следующие:

- портала Azure
- Поставщик Azure Resource Manager
- Классические API управления службами
- Azure PowerShell
- Портал администратора подписок Visual Studio
- Azure DevOps
- Портал фабрики данных Azure

Обратите внимание на то, что политика применяется к среде Azure PowerShell, которая вызывает API Azure Resource Manager. Она не применяется к [Azure AD PowerShell](/powershell/azure/active-directory/install-adv2), которая вызывает Microsoft Graph.

> [!CAUTION]
> Прежде чем настраивать политику для управления доступом к управлению Azure, убедитесь, что вы понимаете, как работает условный доступ. Убедитесь в отсутствии условий, которые могут заблокировать вам доступ к порталу.

Дополнительные сведения о настройке и использовании условного доступа см. [в разделе условный доступ в Azure Active Directory](../active-directory/active-directory-conditional-access-azure-portal.md).
