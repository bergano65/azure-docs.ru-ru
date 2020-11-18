---
title: Пример PowerShell. Назначение пользователя приложению Application Proxy
description: Пример PowerShell, который назначает пользователя приложению Azure Active Directory (Azure AD) Application Proxy.
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 12/05/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: eb8375c4e6ef3af90d5f533bc608046802eec736
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658865"
---
# <a name="assign-a-user-to-a-specific-azure-ad-application-proxy-application"></a>Назначение пользователя конкретному приложению Azure AD Application Proxy

Этот пример скрипта PowerShell позволяет назначить пользователя конкретному приложению Azure Active Directory (Azure AD) Application Proxy.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Для работы с этим примером требуется [модуль AzureAD PowerShell (версии 2) для Graph](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) (AzureAD) или [предварительная версия модуля AzureAD PowerShell (версии 2) для Graph](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview).

## <a name="sample-script"></a>Пример скрипта

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/assign-user-to-app.ps1 "Assign a user to an application")]

## <a name="script-explanation"></a>Описание скрипта

| Get-Help | Примечания |
|---|---|
| [New-AzureADUserAppRoleAssignment](/powershell/module/AzureAD/new-azureaduserapproleassignment?view=azureadps-2.0) | Назначает пользователя роли приложения. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о модуле Azure AD PowerShell см. в этом [обзоре](/powershell/azure/active-directory/overview?view=azureadps-2.0).

См. другие [примеры Azure AD PowerShell для Azure AD Application Proxy](../application-proxy-powershell-samples.md).