---
title: Просмотр субъекта-службы управляемого удостоверения с помощью PowerShell
description: Пошаговое руководство по просмотру субъекта-службы управляемого удостоверения с помощью PowerShell.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/29/2018
ms.author: priyamo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5bf6687adc367ef32550c4cb70a667050e54298c
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56200757"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-powershell"></a>Просмотр субъекта-службы управляемого удостоверения с помощью PowerShell

Функция управляемых удостоверений для ресурсов Azure предоставляет службам Azure автоматически управляемые удостоверения в Azure Active Directory. Это удостоверение можно использовать для аутентификации в любой службе, которая поддерживает аутентификацию Azure AD, не храня какие-либо учетные данные в коде. 

В этой статье описано, как просмотреть субъект-службу управляемого удостоверения с помощью PowerShell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Предварительные требования

- Если вы не работали с управляемыми удостоверениями для ресурсов Azure, изучите [общие сведения](overview.md).
- Если у вас нет учетной записи Azure, зарегистрируйтесь для получения [бесплатной пробной версии](https://azure.microsoft.com/free/).
- [Включите назначенное системой удостоверение в виртуальной машине](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity) или [приложении](/azure/app-service/overview-managed-identity#adding-a-system-assigned-identity).
- Установка последней версии [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="view-the-service-principal"></a>Просмотр субъекта-службы

Следующая команда позволяет просмотреть субъект-службу виртуальной машины или приложения с включенным управляемым удостоверением, назначаемым системой. Замените `<VM or application name>` собственными значениями.

```PowerShell
Get-AzADServicePrincipal -DisplayName <VM or application name>
```

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о просмотре субъектов-служб Azure AD с помощью PowerShell см. в разделе [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal).


