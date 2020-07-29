---
title: Пример PowerShell. список основных сведений о приложениях прокси приложения
description: Пример PowerShell, в котором перечислены приложения прокси приложения Azure Active Directory (Azure AD), а также идентификатор приложения (AppId), имя (DisplayName) и идентификатор объекта (ObjId).
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
ms.openlocfilehash: 009aabb8b742a752a29d775efbd11b153588c09b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "75482222"
---
# <a name="get-all-application-proxy-apps-and-list-basic-information"></a>Получение всех приложений прокси приложения и список основных сведений

В этом примере сценария PowerShell выводятся сведения обо всех приложениях прокси приложения Azure Active Directory (Azure AD), включая идентификатор приложения (AppId), имя (DisplayName) и идентификатор объекта (ObjId).

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Для работы с этим образцом требуется [AzureAD v2 PowerShell для модуля Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) (AzureAD) или [AzureAD v2 PowerShell для предварительной версии модуля Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview).
## <a name="sample-script"></a>Пример скрипта

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-appproxy-apps-basic.ps1 "Get all Application Proxy apps")]

## <a name="script-explanation"></a>Описание скрипта

| Get-Help | Примечания |
|---|---|
|[Get-AzureADServicePrincipal](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) | Возвращает субъект-службу. |

## <a name="next-steps"></a>Дальнейшие шаги

Дополнительные сведения о модуле Azure AD PowerShell см. в статье [Общие сведения о модуле Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0).

Другие примеры PowerShell для прокси приложения см. в статье [примеры Azure AD PowerShell для AD application proxy Azure](../application-proxy-powershell-samples.md).