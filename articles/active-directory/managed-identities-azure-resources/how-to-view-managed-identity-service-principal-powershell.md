---
title: Просмотр субъекта-службы управляемого удостоверения с помощью PowerShell — Azure AD
description: Пошаговое руководство по просмотру субъекта-службы управляемого удостоверения с помощью PowerShell.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/29/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9ac2d1d4fc5e669f63bff49a7948db74ec1baa62
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184124"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-powershell"></a>Просмотр субъекта-службы управляемого удостоверения с помощью PowerShell

Функция управляемых удостоверений для ресурсов Azure предоставляет службам Azure автоматически управляемые удостоверения в Azure Active Directory. Это удостоверение можно использовать для аутентификации в любой службе, которая поддерживает аутентификацию Azure AD, не храня какие-либо учетные данные в коде. 

В этой статье описано, как просмотреть субъект-службу управляемого удостоверения с помощью PowerShell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>предварительным требованиям

- Если вы не работали с управляемыми удостоверениями для ресурсов Azure, изучите [общие сведения](overview.md).
- Если у вас нет учетной записи Azure, зарегистрируйтесь для получения [бесплатной пробной версии](https://azure.microsoft.com/free/).
- [Включите назначенное системой удостоверение в виртуальной машине](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity) или [приложении](/azure/app-service/overview-managed-identity#adding-a-system-assigned-identity).
- Установка последней версии [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="view-the-service-principal"></a>Просмотр субъекта-службы

Следующая команда позволяет просмотреть субъект-службу виртуальной машины или приложения с включенным управляемым удостоверением, назначаемым системой. Замените `<VM or application name>` собственными значениями.

```powershell
Get-AzADServicePrincipal -DisplayName <VM or application name>
```

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о просмотре субъектов-служб Azure AD с помощью PowerShell см. в разделе [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal).


