---
title: Управление доступом к управлению Azure с помощью условного доступа в Azure AD
description: Узнайте об использовании условия доступа в Azure AD для управления доступом к управлению Azure.
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
ms.openlocfilehash: f3341f1c30a1581b8507652c322c00581e3972aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77137415"
---
# <a name="manage-access-to-azure-management-with-conditional-access"></a>Управление доступом к управлению Azure с помощью условного доступа

> [!CAUTION]
> Убедитесь, что вы понимаете, как работает условный доступ, прежде чем настроить политику для управления доступом к управлению Azure. Убедитесь в отсутствии условий, которые могут заблокировать вам доступ к порталу.

Условный доступ в Active Directory Azure (Azure AD) управляет доступом к облачным приложениям на основе определенных условий, которые вы указываете. Чтобы разрешить доступ, необходимо создать политики условного доступа, которые позволяют или блокируют доступ в зависимости от того, удовлетворяются ли требования в политике. 

Как правило, для управления доступом к облачным приложениям используется «Условный доступ». Вы также можете настроить политики для управления доступом к функциям управления Azure на основе определенных условий (таких как риск при входе, расположение или устройство) и для обеспечения соблюдения требований, таких как многофакторная проверка подлинности.

Чтобы создать политику для управления Azure, выберите **Microsoft Azure Management** (Управление Microsoft Azure) в области **Облачные приложения** при выборе приложения, к которому применяется политика.

![Условный доступ для управления Azure](./media/conditional-access-azure-management/conditional-access-azure-mgmt.png)

Создаваемые политики применяются ко всем конечным точкам управления Azure, включая следующие:

- Портал Azure
- Поставщик ресурсов Azure
- Классические AIS по управлению обслуживанием
- Azure PowerShell
- Администратор ампонок Visual Studio
- Azure DevOps
- Портал Azure Data Factory

Обратите внимание на то, что политика применяется к среде Azure PowerShell, которая вызывает API Azure Resource Manager. Она не применяется к [Azure AD PowerShell](/powershell/azure/active-directory/install-adv2), которая вызывает Microsoft Graph.


Для получения дополнительной информации о том, как настроить и использовать условный доступ, смотрите [В каталоге Active Azure.](../active-directory/active-directory-conditional-access-azure-portal.md)
