---
title: Образец PowerShell - Приложения Прокси-приложения с идентичными сертификатами
description: Пример PowerShell, в который перечислены все прокси-приложения приложения Azure Active Directory (Azure AD), которые публикуются с идентичным сертификатом.
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
ms.openlocfilehash: ef654226d041a7602c03e1ce4670d2f8eea0c2e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75482014"
---
# <a name="get-all-azure-ad-proxy-application-apps-that-are-published-with-the-identical-certificate"></a>Получите все приложения Azure AD Proxy, которые публикуются с идентичным сертификатом

В этом примере скрипта PowerShell перечислены все прокси-приложения Azure Active Directory (Azure AD), которые публикуются с идентичным сертификатом.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Этот образец требует [AzureAD V2 PowerShell для графического модуля](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) (AzureAD) или [AzureAD V2 PowerShell для предварительной версии модуля Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview).

## <a name="sample-script"></a>Пример скрипта

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-custom-domain-identical-cert.ps1 "Get all Azure AD Proxy application apps published with the identical certificate")]

## <a name="script-explanation"></a>Описание скрипта

| Команда | Примечания |
|---|---|
|[Get-AzureADServicePrincipal](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) | Получает директора службы. |
|[Get-AzureADApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0) | Получает приложение Azure AD. |
|[Get-AzureADApplicationProxyApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplicationproxyapplication?view=azureadps-2.0) | Извлекает приложение, настроенное для прокси-сервера приложений в Azure AD. |

## <a name="next-steps"></a>Дальнейшие действия

Для получения дополнительной информации о модуле Azure AD PowerShell смотрите [обзор модуля Azure AD PowerShell.](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0)

Для других примеров PowerShell [Azure AD PowerShell examples for Azure AD Application Proxy](../application-proxy-powershell-samples.md)для приложения Proxy см.