---
title: Пример PowerShell. Назначение пользователя для приложения прокси приложения
description: Пример PowerShell, который назначает пользователя для приложения прокси приложения Azure Active Directory (Azure AD).
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 42667ebdfc37b679d56421e3a82d4a6aaaed8fdf
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "75483340"
---
# <a name="assign-a-user-to-a-specific-azure-ad-application-proxy-application"></a>Назначение пользователя конкретному приложению Azure AD Application Proxy

Этот пример сценария PowerShell позволяет назначить пользователя конкретному приложению AD Application Proxy Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Для работы с этим образцом требуется [AzureAD v2 PowerShell для модуля Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) (AzureAD) или [AzureAD v2 PowerShell для предварительной версии модуля Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview).

## <a name="sample-script"></a>Пример скрипта

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/assign-user-to-app.ps1 "Assign a user to an application")]

## <a name="script-explanation"></a>Описание скрипта

| Get-Help | Примечания |
|---|---|
| [New-AzureADUserAppRoleAssignment](https://docs.microsoft.com/powershell/module/AzureAD/new-azureaduserapproleassignment?view=azureadps-2.0) | Назначает пользователя роли приложения. |

## <a name="next-steps"></a>Дальнейшие шаги

Дополнительные сведения о модуле Azure AD PowerShell см. в статье [Общие сведения о модуле Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0).

Другие примеры PowerShell для прокси приложения см. в статье [примеры Azure AD PowerShell для AD application proxy Azure](../application-proxy-powershell-samples.md).