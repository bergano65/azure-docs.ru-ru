---
title: Подключение к Azure Stack с помощью PowerShell в роли оператора | Документы Майкрософт
description: Подключитесь к Azure Stack с помощью PowerShell в роли оператора
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 12/07/2018
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: 7a744520fe2a3b53b1306d4c80a5eca7d86258a7
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/08/2019
ms.locfileid: "54104545"
---
# <a name="connect-to-azure-stack-with-powershell-as-an-operator"></a>Подключитесь к Azure Stack с помощью PowerShell в роли оператора.

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

Azure Stack можно настроить для управления такими ресурсами, как создание предложений, планов, квот и предупреждений, с помощью PowerShell. Этот раздел поможет настроить среду оператора.

## <a name="prerequisites"></a>Предварительные требования

Выполните следующие предварительные требования с помощью [пакета средств разработки](./asdk/asdk-connect.md#connect-with-rdp) или внешнего клиента на базе Windows (при [подключении к ASDK через VPN](./asdk/asdk-connect.md#connect-with-vpn)). 

 - Установите [совместимые с Azure Stack модули Azure PowerShell](azure-stack-powershell-install.md).  
 - Скачайте [средства, необходимые для работы с Azure Stack](azure-stack-powershell-download.md).  

## <a name="connect-with-azure-ad"></a>Подключение к Azure AD

Настройте среду оператора Azure Stack с помощью PowerShell. Выполните один из приведенных ниже скриптов. Замените значение tenantName для Azure Active Directory (Azure AD) и значение конечной точки Azure Resource Manager собственной конфигурацией среды. <!-- GraphAudience endpoint -->

```PowerShell  
    # Register an Azure Resource Manager environment that targets your Azure Stack instance. Get your Azure Resource Manager endpoint value from your service provider.
Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint "https://adminmanagement.local.azurestack.external"

    # Set your tenant name
    $AuthEndpoint = (Get-AzureRmEnvironment -Name "AzureStackAdmin").ActiveDirectoryAuthority.TrimEnd('/')
    $AADTenantName = "<myDirectoryTenantName>.onmicrosoft.com"
    $TenantId = (invoke-restmethod "$($AuthEndpoint)/$($AADTenantName)/.well-known/openid-configuration").issuer.TrimEnd('/').Split('/')[-1]

    # After signing in to your environment, Azure Stack cmdlets
    # can be easily targeted at your Azure Stack instance.
    Add-AzureRmAccount -EnvironmentName "AzureStackAdmin" -TenantId $TenantId
```

## <a name="connect-with-ad-fs"></a>Подключение к AD FS

Подключитесь к среде оператора Azure Stack с помощью PowerShell и служб федерации Azure Active Directory (Azure AD FS). Для пакета средств разработки Azure Stack для этой конечной точки Azure Resource Manager устанавливается значение `https://adminmanagement.local.azurestack.external`. Чтобы получить конечную точку Azure Resource Manager для интегрированных систем Azure Stack, обратитесь к поставщику услуг.

<!-- GraphAudience endpoint -->

  ```PowerShell  
  # Register an Azure Resource Manager environment that targets your Azure Stack instance. Get your Azure Resource Manager endpoint value from your service provider.
  Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint "https://adminmanagement.local.azurestack.external"

  $AuthEndpoint = (Get-AzureRmEnvironment -Name "AzureStackAdmin").ActiveDirectoryAuthority.TrimEnd('/')
  $tenantId = (invoke-restmethod "$($AuthEndpoint)/.well-known/openid-configuration").issuer.TrimEnd('/').Split('/')[-1]

  # Sign in to your environment

  $cred = get-credential

  Login-AzureRmAccount `
    -EnvironmentName "AzureStackAdmin" `
    -TenantId $tenantId `
    -Credential $cred
  ```



## <a name="test-the-connectivity"></a>Проверка подключения

Теперь, когда все настроено, можно создавать ресурсы в Azure Stack с помощью Azure PowerShell. Например, можно создать группу ресурсов для приложения и добавить виртуальную машину. Используйте команду ниже, чтобы создать группу ресурсов с именем **MyResourceGroup**.

```PowerShell  
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>Дополнительная информация

 - [Разработка шаблонов для Azure Stack](user/azure-stack-develop-templates.md)
 - [Развертывание шаблонов с помощью PowerShell](user/azure-stack-deploy-template-powershell.md)
