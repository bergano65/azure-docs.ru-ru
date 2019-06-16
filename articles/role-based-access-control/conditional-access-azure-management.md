---
title: Управление доступом для управления Azure с помощью условного доступа в Azure Active Directory
description: Дополнительные сведения об использовании условного доступа в Azure AD для управления доступом к функциям управления Azure.
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
ms.date: 01/15/2019
ms.author: rolyon
ms.reviewer: skwan
ms.openlocfilehash: 4ee58835a5055f5382cefa3a049424ffe97a01c4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67122948"
---
# <a name="manage-access-to-azure-management-with-conditional-access"></a>Управление доступом для управления Azure с помощью условного доступа

Условный доступ в Azure Active Directory (Azure AD) управляет доступом к облачным приложениям на основе определенных условий, заданных. Чтобы разрешить доступ, создание политики условного доступа, разрешить или запретить доступ, в зависимости от того, выполняются ли требования, указанные в политике. 

Как правило вы используете условный доступ для управления доступом к облачным приложениям. Вы также можете настроить политики для управления доступом к функциям управления Azure на основе определенных условий (таких как риск при входе, расположение или устройство) и для обеспечения соблюдения требований, таких как многофакторная проверка подлинности.

Чтобы создать политику для управления Azure, выберите **Microsoft Azure Management** (Управление Microsoft Azure) в области **Облачные приложения** при выборе приложения, к которому применяется политика.

![Условный доступ для управления Azure](./media/conditional-access-azure-management/conditional-access-azure-mgmt.png)

Созданная вами политика применяется ко всем конечным точкам управления Azure, включая портал Azure, поставщик Azure Resource Manager, классические API управления службами, Azure PowerShell и портал администратора подписок Visual Studio. Обратите внимание на то, что политика применяется к среде Azure PowerShell, которая вызывает API Azure Resource Manager. Она не применяется к [Azure AD PowerShell](/powershell/azure/active-directory/install-adv2), которая вызывает Microsoft Graph.

> [!CAUTION]
> Убедитесь, что вы понимаете, как работает условный доступ, прежде чем настраивать политику для управления доступом к функциям управления Azure. Убедитесь в отсутствии условий, которые могут заблокировать вам доступ к порталу.

Дополнительные сведения о том, как настроить и использовать условного доступа см. в разделе [условного доступа в Azure Active Directory](../active-directory/active-directory-conditional-access-azure-portal.md).