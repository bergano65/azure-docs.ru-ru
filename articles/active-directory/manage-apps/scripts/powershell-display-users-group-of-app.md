---
title: Пример PowerShell. Вывод данных о пользователях и группах для приложения Application Proxy
description: Пример PowerShell, который выводит данные обо всех пользователях и группах, назначенных конкретному приложению Azure Active Directory (Azure AD) Application Proxy.
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
ms.openlocfilehash: 184b8f4c7d1765e9a221b87f06e47d8e0d4b8057
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659172"
---
# <a name="display-users-and-groups-assigned-to-an-application-proxy-application"></a>Отображение пользователей и групп, назначенных приложению прокси приложения

Этот пример скрипта PowerShell выводит данные о пользователях и группах, назначенных конкретному приложению Azure Active Directory (Azure AD) Application Proxy.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Для работы с этим примером требуется [модуль Azure AD PowerShell (версии 2) для Graph](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) (AzureAD) или [предварительная версия модуля Azure AD PowerShell (версии 2) для Graph](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview).

## <a name="sample-script"></a>Пример скрипта

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/display-users-group-of-an-app.ps1 "Display users and groups assigned to an Application Proxy application")]

## <a name="script-explanation"></a>Описание скрипта

| Get-Help | Примечания |
|---|---|
| [Get-AzureADUser](/powershell/module/AzureAD/get-azureaduser?view=azureadps-2.0)| Возвращает пользователя. |
| [Get-AzureADGroup](/powershell/module/AzureAD/get-azureadgroup?view=azureadps-2.0)| Получает группу. |
| [Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) | Получает субъект-службу. |
| [Get-AzureADUserAppRoleAssignment](/powershell/module/AzureAD/get-azureaduserapproleassignment?view=azureadps-2.0) | Получает назначение роли приложения для пользователя. |
| [Get-AzureADGroupAppRoleAssignment](/powershell/module/AzureAD/get-azureadgroupapproleassignment?view=azureadps-2.0) | Получает назначение роли приложения для группы. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о модуле Azure AD PowerShell см. в этом [обзоре](/powershell/azure/active-directory/overview?view=azureadps-2.0).

См. другие [примеры Azure AD PowerShell для Azure AD Application Proxy](../application-proxy-powershell-samples.md).