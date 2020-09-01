---
title: Просмотр субъекта-службы управляемого удостоверения с помощью PowerShell — Azure AD
description: Пошаговое руководство по просмотру субъекта-службы управляемого удостоверения с помощью PowerShell.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/29/2018
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a9da4689a1b5579f90a1df0feb487e50d57a9d98
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/01/2020
ms.locfileid: "89269222"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-powershell"></a>Просмотр субъекта-службы управляемого удостоверения с помощью PowerShell

Функция управляемых удостоверений для ресурсов Azure предоставляет службам Azure автоматически управляемые удостоверения в Azure Active Directory. Это удостоверение можно использовать для аутентификации в любой службе, которая поддерживает аутентификацию Azure AD, не храня какие-либо учетные данные в коде. 

В этой статье описано, как просмотреть субъект-службу управляемого удостоверения с помощью PowerShell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Предварительные требования

- Если вы не работали с управляемыми удостоверениями для ресурсов Azure, изучите [общие сведения](overview.md).
- Если у вас нет учетной записи Azure, зарегистрируйтесь для получения [бесплатной пробной версии](https://azure.microsoft.com/free/).
- [Включите назначенное системой удостоверение в виртуальной машине](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity) или [приложении](../../app-service/overview-managed-identity.md#add-a-system-assigned-identity).
- Установите последнюю версию [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="view-the-service-principal"></a>Просмотр субъекта-службы

Следующая команда позволяет просмотреть субъект-службу виртуальной машины или приложения с включенным управляемым удостоверением, назначаемым системой. Замените `<VM or application name>` собственными значениями.

```powershell
Get-AzADServicePrincipal -DisplayName <VM or application name>
```

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о просмотре субъектов-служб Azure AD с помощью PowerShell см. в разделе [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal).