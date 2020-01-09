---
title: Пример PowerShell. вывод расширенных сведений о приложениях прокси приложения
description: Пример PowerShell, в котором перечислены все приложения прокси приложения Azure Active Directory (Azure AD), а также идентификатор приложения (AppId), имя (DisplayName), внешний URL-адрес (Екстерналурл), внутренний URL-адрес (InternalUrl) и тип проверки подлинности ( Екстерналаусентикатионтипе).
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
ms.openlocfilehash: f58de8c081dbb0323a2d9638777e29d879172072
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75482144"
---
# <a name="get-all-application-proxy-apps-and-list-extended-information"></a>Получение всех приложений прокси приложения и перечисление расширенных сведений

В этом примере сценария PowerShell выводятся сведения обо всех приложениях прокси приложения Azure Active Directory (Azure AD), включая идентификатор приложения (AppId), имя (DisplayName), внешний URL-адрес (Екстерналурл), внутренний URL-адрес (InternalUrl) и тип проверки подлинности (Екстерналаусентикатионтипе).

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Для работы с этим образцом требуется [AzureAD v2 PowerShell для модуля Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) (AzureAD) или [AzureAD v2 PowerShell для предварительной версии модуля Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview).

## <a name="sample-script"></a>Пример скрипта

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-appproxy-apps-extended.ps1 "Get all Application Proxy apps")]

## <a name="script-explanation"></a>Описание скрипта

| Get-Help | Примечания |
|---|---|
|[Get-AzureADServicePrincipal](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) | Возвращает субъект-службу. |
|[Get-AzureADApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0) | Возвращает приложение Azure AD. |
|[Get-Азуреадаппликатионпроксяппликатион](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplicationproxyapplication?view=azureadps-2.0) | Извлекает приложение, настроенное для прокси приложения в Azure AD. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о модуле Azure AD PowerShell см. в статье [Общие сведения о модуле Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0).

Другие примеры PowerShell для прокси приложения см. в статье [примеры Azure AD PowerShell для AD application proxy Azure](../application-proxy-powershell-samples.md).