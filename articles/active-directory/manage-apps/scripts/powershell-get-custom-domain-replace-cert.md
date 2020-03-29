---
title: Образец PowerShell - Заменить сертификат в приложениях Application Proxy
description: Пример PowerShell, который заменяет сертификат в приложениях Azure Active Directory (Azure AD) приложения proxy.
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
ms.openlocfilehash: 91aafe00856f37e9d7e6b3babafecd2cbdf5f856
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75482001"
---
# <a name="get-all-application-proxy-applications-published-with-the-identical-certificate-and-replace-it"></a>Получите все приложения Прокси приложений опубликованы с идентичным сертификатом и заменить его

Этот пример скрипта PowerShell позволяет заменить сертификат оптом для всех приложений Azure Active Directory (Azure AD), которые публикуются с идентичным сертификатом.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Этот образец требует [AzureAD V2 PowerShell для графического модуля](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) (AzureAD) или [AzureAD V2 PowerShell для предварительной версии модуля Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview).

## <a name="sample-script"></a>Пример скрипта

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-custom-domain-replace-cert.ps1 "Get all Application Proxy applications published with the identical certificate and replace it")]

## <a name="script-explanation"></a>Описание скрипта

| Команда | Примечания |
|---|---|
|[Get-AzureADServicePrincipal](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) | Получает директора службы. |
|[Get-AzureADApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0) | Получает приложение Azure AD. |
|[Get-AzureADApplicationProxyApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplicationproxyapplication?view=azureadps-2.0) | Извлекает приложение, настроенное для прокси-сервера приложений в Azure AD. |
|[Set-AzureADApplicationProxyApplicationCustomDomainCertificate](https://docs.microsoft.com/powershell/module/azuread/set-azureadapplicationproxyapplicationcustomdomaincertificate?view=azureadps-2.0) | Присваивает сертификат приложению, настроенной для прокси-сервера приложения в Azure AD. Эта команда загружает сертификат и позволяет приложению использовать пользовательские домены. |

## <a name="next-steps"></a>Дальнейшие действия

Для получения дополнительной информации о модуле Azure AD PowerShell смотрите [обзор модуля Azure AD PowerShell.](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0)

Для других примеров PowerShell [Azure AD PowerShell examples for Azure AD Application Proxy](../application-proxy-powershell-samples.md)для приложения Proxy см.