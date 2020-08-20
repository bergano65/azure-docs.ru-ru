---
title: Пример PowerShell. Вывод основных сведений о приложениях Application Proxy
description: Пример PowerShell, который выводит сведения о приложениях Azure Active Directory (Azure AD) Application Proxy, а также данные об идентификаторах приложений (AppId), именах (DisplayName) и идентификаторах объектов (ObjId).
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
ms.openlocfilehash: 80509969a30ea3e708d2e176a87277d51fb6da3f
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/18/2020
ms.locfileid: "88506939"
---
# <a name="get-all-application-proxy-apps-and-list-basic-information"></a>Получение данных обо всех приложениях Application Proxy и вывод основных сведений

Этот пример скрипта PowerShell выводит сведения обо всех приложениях Azure Active Directory (Azure AD) Application Proxy, в том числе идентификаторы приложений (AppId), имена (DisplayName) и идентификаторы объектов (ObjId).

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Для работы с этим примером требуется [модуль Azure AD PowerShell (версии 2) для Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) (AzureAD) или [предварительная версия модуля Azure AD PowerShell (версии 2) для Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview).
## <a name="sample-script"></a>Пример скрипта

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-appproxy-apps-basic.ps1 "Get all Application Proxy apps")]

## <a name="script-explanation"></a>Описание скрипта

| Get-Help | Примечания |
|---|---|
|[Get-AzureADServicePrincipal](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) | Получает субъект-службу. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о модуле Azure AD PowerShell см. в этом [обзоре](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0).

См. другие [примеры Azure AD PowerShell для Azure AD Application Proxy](../application-proxy-powershell-samples.md).