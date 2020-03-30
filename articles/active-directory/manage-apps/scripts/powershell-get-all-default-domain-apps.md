---
title: Образец PowerShell - Приложения Прокси-приложений с использованием домена по умолчанию
description: Пример PowerShell, в которых перечислены все прокси-приложения приложения Azure Active Directory (Azure AD), использующие домены по умолчанию (.msappproxy.net).
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
ms.openlocfilehash: 40a9f7ce86981d6f03b25237a7a73869fd5fd4ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75483379"
---
# <a name="get-all-application-proxy-apps-using-default-domains-msappproxynet"></a>Получите все приложения Application Proxy с помощью доменов по умолчанию (.msappproxy.net)

В этом примере скрипта PowerShell перечислены все прокси-приложения приложения Azure Active Directory (Azure AD), использующие домены по умолчанию (msappproxy.net).

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Этот образец требует [AzureAD V2 PowerShell для графического модуля](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) (AzureAD) или [AzureAD V2 PowerShell для предварительной версии модуля Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview).

## <a name="sample-script"></a>Пример скрипта

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-default-domain-apps.ps1 "Get all Application Proxy apps using default domains (.msappproxy.net)")]

## <a name="script-explanation"></a>Описание скрипта

| Команда | Примечания |
|---|---|
|[Get-AzureADServicePrincipal](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) | Получает директора службы. |
|[Get-AzureADApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0) | Получает приложение Azure AD. |
|[Get-AzureADApplicationProxyApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplicationproxyapplication?view=azureadps-2.0) | Извлекает приложение, настроенное для прокси-сервера приложений в Azure AD. |

## <a name="next-steps"></a>Дальнейшие действия

Для получения дополнительной информации о модуле Azure AD PowerShell смотрите [обзор модуля Azure AD PowerShell.](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0)

Для других примеров PowerShell [Azure AD PowerShell examples for Azure AD Application Proxy](../application-proxy-powershell-samples.md)для приложения Proxy см.