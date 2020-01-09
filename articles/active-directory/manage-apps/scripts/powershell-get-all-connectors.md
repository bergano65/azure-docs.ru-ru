---
title: Пример PowerShell. Вывод списка всех групп соединителей прокси приложения
description: Пример PowerShell, в котором перечислены все группы соединителей и соединители прокси приложения Azure Active Directory (Azure AD) в каталоге.
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
ms.openlocfilehash: 53af7f3f9049edc23ed4226f43e7fe67398c29f5
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75482118"
---
# <a name="get-all-connector-groups-and-connectors-in-the-directory"></a>Получение всех групп соединителей и соединителей в каталоге

Этот пример сценария PowerShell перечисляет все группы соединителей и соединители прокси приложения Azure Active Directory (Azure AD) в каталоге.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Для работы с этим образцом требуется [AzureAD v2 PowerShell для модуля Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) (AzureAD) или [AzureAD v2 PowerShell для предварительной версии модуля Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview).

## <a name="sample-script"></a>Пример скрипта

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-connectors.ps1 "Get all connector groups and connectors in the directory")]

## <a name="script-explanation"></a>Описание скрипта

| Get-Help | Примечания |
|---|---|
| [Get-Азуреадаппликатионпроксиконнекторграуп](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplicationproxyconnectorgroup?view=azureadps-2.0) | Извлекает список всех групп соединителей или сведения о указанной группе соединителей. |
| [Get-Азуреадаппликатионпроксиконнекторграупмемберс](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplicationproxyconnectorgroupmembers?view=azureadps-2.0) | Получает все соединители прокси приложения, связанные с каждой группой соединителей.|

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о модуле Azure AD PowerShell см. в статье [Общие сведения о модуле Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0).

Другие примеры PowerShell для прокси приложения см. в статье [примеры Azure AD PowerShell для AD application proxy Azure](../application-proxy-powershell-samples.md).