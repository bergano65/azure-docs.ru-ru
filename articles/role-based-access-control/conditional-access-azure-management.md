---
title: Управление доступом к функциям управления Azure с помощью условного доступа в Azure Active Directory
description: Сведения об использовании условного доступа в Azure AD для управления доступом к функциям управления Azure.
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
ms.openlocfilehash: b824d122a5d26c17c41a0e2ea1c595c9e2dd7206
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/16/2019
ms.locfileid: "54354354"
---
# <a name="manage-access-to-azure-management-with-conditional-access"></a>Управление доступом к функциям управления Azure с помощью условного доступа

Компонент условного доступа в Azure Active Directory (Azure AD) управляет доступом к облачным приложениям на основе заданных вами условий. Чтобы разрешить доступ, создайте политики условного доступа, разрешающие или запрещающие доступ в зависимости от того, соблюдены ли требования политики. 

Обычно условный доступ используется для управления доступом к облачным приложениям. Вы также можете настроить политики для управления доступом к функциям управления Azure на основе определенных условий (таких как риск при входе, расположение или устройство) и для обеспечения соблюдения требований, таких как многофакторная проверка подлинности.

Чтобы создать политику для управления Azure, выберите **Microsoft Azure Management** (Управление Microsoft Azure) в области **Облачные приложения** при выборе приложения, к которому применяется политика.

![Условный доступ для управления Azure](./media/conditional-access-azure-management/conditional-access-azure-mgmt.png)

Созданная вами политика применяется ко всем конечным точкам управления Azure, включая портал Azure, поставщик Azure Resource Manager, классические API управления службами, Azure PowerShell и портал администратора подписок Visual Studio. Обратите внимание на то, что политика применяется к среде Azure PowerShell, которая вызывает API Azure Resource Manager. Она не применяется к [Azure AD PowerShell](/powershell/azure/active-directory/install-adv2), которая вызывает Microsoft Graph.

> [!CAUTION]
> Перед настройкой политики для управления доступом к функциям управления Azure тщательно изучите принципы работы условного доступа. Убедитесь в отсутствии условий, которые могут заблокировать вам доступ к порталу.

Дополнительные сведения о настройке и использовании условного доступа см. в статье [Условный доступ в Azure Active Directory](../active-directory/active-directory-conditional-access-azure-portal.md).