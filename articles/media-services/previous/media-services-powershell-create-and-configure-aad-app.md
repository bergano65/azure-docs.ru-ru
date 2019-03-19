---
title: Создание приложения Azure AD для доступа к API служб мультимедиа Azure с помощью PowerShell | Документация Майкрософт
description: Узнайте, как использовать PowerShell, чтобы создать приложение Azure Active Directory (Azure AD) и настроить его для доступа к API служб мультимедиа Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: 13e4b99da6de7d9c1fb08edc80605d38ed07c6f5
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/23/2019
ms.locfileid: "56727202"
---
# <a name="use-powershell-to-create-an-azure-ad-app-to-use-with-the-azure-media-services-api"></a>Создание приложения Azure AD для работы с API служб мультимедиа Azure с помощью PowerShell

Узнайте, как с помощью сценария PowerShell создать приложение Azure Active Directory (Azure AD) и участник-службу для доступа к ресурсам служб мультимедиа Azure.  

## <a name="prerequisites"></a>Технические условия

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

- Учетная запись Azure. Если у вас нет учетной записи Azure, начните с получения [бесплатной пробной версии](https://azure.microsoft.com/pricing/free-trial/). 
- Учетная запись служб мультимедиа. Дополнительные сведения см. в статье [Создание учетной записи служб мультимедиа Azure с помощью портала Azure](media-services-portal-create-account.md).

- Установите Azure PowerShell. Дополнительные сведения см. в разделе [об использовании Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).

## <a name="create-an-azure-ad-app-by-using-powershell"></a>Создание приложения Azure AD с помощью PowerShell  

```powershell
Connect-AzAccount
Import-Module Az.Resources
Set-AzContext -SubscriptionId $SubscriptionId
$ServicePrincipal = New-AzADServicePrincipal -DisplayName $ApplicationDisplayName -Password $Password

Get-AzADServicePrincipal -ObjectId $ServicePrincipal.Id 
$NewRole = $null
$Scope = "/subscriptions/your subscription id/resourceGroups/userresourcegroup/providers/microsoft.media/mediaservices/your media account"

$Retries = 0;While ($NewRole -eq $null -and $Retries -le 6)
{
    # Sleep here for a few seconds to allow the service principal application to become active (usually, it will take only a couple of seconds)
    Sleep 15
    New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $ServicePrincipal.ApplicationId -Scope $Scope | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzRoleAssignment -ServicePrincipalName $ServicePrincipal.ApplicationId -ErrorAction SilentlyContinue
    $Retries++;
}
```

Дополнительные сведения см. в следующих статьях:

- [Использование Azure PowerShell для создания субъекта-службы и доступа к ресурсам](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)
- [Управление доступом на основе ролей с помощью Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
- [Authenticating to Azure AD in daemon apps with certificates - manual configuration steps](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential/blob/master/Manual-Configuration-Steps.md#add-the-certificate-as-a-key-for-the-todolistdaemonwithcert-application-in-azure-ad) (Ручная настройка аутентификации приложений управляющей программы в Azure AD с помощью сертификатов)

## <a name="next-steps"></a>Дальнейшие действия

Приступите к [передаче файлов в учетную запись](media-services-portal-upload-files.md).
