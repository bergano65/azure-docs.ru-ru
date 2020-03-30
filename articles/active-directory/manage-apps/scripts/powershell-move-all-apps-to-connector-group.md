---
title: Образец PowerShell - Перемещение приложений для прокси-приложений приложений в другую группу
description: Пример приложения Azure Active Directory (Azure AD) прокси PowerShell используется для перемещения всех приложений, в настоящее время назначенных в группу разъемов, в другую группу разъемов.
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
ms.openlocfilehash: f6dccdaa96dadb061b168bbdf6148ed4d759776c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75481988"
---
# <a name="move-all-apps-assigned-to-a-connector-group-to-another-connector-group"></a>Переместите все приложения, назначенные группе разъемов, в другую группу разъемов

В этом примере скрипта PowerShell все приложения Azure Active Directory (Azure AD) приложения Proxy в настоящее время назначены группе разъемов в другую группу разъемов.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Этот образец требует [AzureAD V2 PowerShell для графического модуля](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) (AzureAD) или [AzureAD V2 PowerShell для предварительной версии модуля Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview).

## <a name="sample-script"></a>Пример скрипта

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/move-all-apps-to-a-connector-group.ps1 "Move all apps assigned to a connector group to another connector group")]

## <a name="script-explanation"></a>Описание скрипта

| Команда | Примечания |
|---|---|
|[Get-AzureADServicePrincipal](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) | Получает директора службы. |
|[Get-AzureADApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0) | Получает приложение Azure AD. |
| [Get-AzureADApplicationProxyConnectorGroup](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplicationproxyconnectorgroup?view=azureadps-2.0) | Извлекает список всех групп разъемов, или если указано, детали указанной группы разъема. |
| [Set-AzureADApplicationProxyConnectorGroup](https://docs.microsoft.com/powershell/module/azuread/set-azureadapplicationproxyapplicationconnectorgroup?view=azureadps-2.0) | Присваивает данную группу разъема указанному приложению.|

## <a name="next-steps"></a>Дальнейшие действия

Для получения дополнительной информации о модуле Azure AD PowerShell смотрите [обзор модуля Azure AD PowerShell.](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0)

Для других примеров PowerShell [Azure AD PowerShell examples for Azure AD Application Proxy](../application-proxy-powershell-samples.md)для приложения Proxy см.