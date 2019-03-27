---
title: Подключение к Azure Stack в роли пользователя с помощью PowerShell | Документация Майкрософт
description: Действия для подключения к пользовательской сущности Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 01/24/2019
ms.openlocfilehash: ab23013d8de61e13013aa4cd735be04e1e3213c3
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58119944"
---
# <a name="connect-to-azure-stack-with-powershell-as-a-user"></a>Подключение к Azure Stack в роли пользователя с помощью PowerShell

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

В этой статье предоставляются инструкции по подключению к экземпляру Azure Stack. Необходимо подключиться к управлению ресурсами Azure Stack с помощью PowerShell. Например, с помощью PowerShell можно будет подписываться на предложения, создавать виртуальные машины и развертывать шаблоны Azure Resource Manager. для выполнения командлетов PowerShell.

Чтобы настроить:
  - У вас есть такие требования:
  - Присоединиться к Azure Active Directory (Azure AD) и службам федерации Active Directory (AD FS). 
  - Зарегистрируйте поставщиков ресурсов.
  - Проверьте подключение.

## <a name="prerequisites"></a>Предварительные требования

Приведенные ниже предварительные требования можно выполнить либо с помощью [пакета средств разработки](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), либо с помощью внешнего клиента на основе Windows, если используется [VPN-подключение](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn).

* Установите [совместимые с Azure Stack модули Azure PowerShell](azure-stack-powershell-install.md).
* Скачайте [средства, необходимые для работы с Azure Stack](azure-stack-powershell-download.md).

Обязательно замените приведенные ниже переменные сценария значениями из конфигурации Azure Stack:

- **Имя клиента Azure AD**  
  Имя вашего клиента Azure AD, используемое для управления Azure Stack, например yourdirectory.onmicrosoft.com.
- **Конечная точка Azure Resource Manager**  
  Для пакета средств разработки Azure Stack используется значение https://management.local.azurestack.external. Чтобы получить это значение для интегрированных систем Azure Stack, обратитесь к поставщику услуг.

## <a name="connect-with-azure-ad"></a>Подключение к Azure AD

```PowerShell  
    Add-AzureRMEnvironment -Name "AzureStackUser" -ArmEndpoint "https://management.local.azurestack.external"
    # Set your tenant name
    $AuthEndpoint = (Get-AzureRmEnvironment -Name "AzureStackUser").ActiveDirectoryAuthority.TrimEnd('/')
    $AADTenantName = "<myDirectoryTenantName>.onmicrosoft.com"
    $TenantId = (invoke-restmethod "$($AuthEndpoint)/$($AADTenantName)/.well-known/openid-configuration").issuer.TrimEnd('/').Split('/')[-1]

    # After signing in to your environment, Azure Stack cmdlets
    # can be easily targeted at your Azure Stack instance.
    Add-AzureRmAccount -EnvironmentName "AzureStackUser" -TenantId $TenantId
```

## <a name="connect-with-ad-fs"></a>Подключение к AD FS

  ```PowerShell  
  # Register an Azure Resource Manager environment that targets your Azure Stack instance
  Add-AzureRMEnvironment -Name "AzureStackUser" -ArmEndpoint "https://management.local.azurestack.external"

  # Sign in to your environment
  Login-AzureRmAccount -EnvironmentName "AzureStackUser"
  ```

## <a name="register-resource-providers"></a>Регистрация поставщиков ресурсов

Поставщики ресурсов не регистрируются автоматически в новых подписках пользователей, для которых на портале не развернуты какие-либо ресурсы. Можно явно зарегистрировать поставщик ресурсов, выполнив следующий сценарий.

```PowerShell  
foreach($s in (Get-AzureRmSubscription)) {
        Select-AzureRmSubscription -SubscriptionId $s.SubscriptionId | Out-Null
        Write-Progress $($s.SubscriptionId + " : " + $s.SubscriptionName)
Get-AzureRmResourceProvider -ListAvailable | Register-AzureRmResourceProvider -Force
    }
```

## <a name="test-the-connectivity"></a>Проверка подключения

Настроив все необходимое, проверьте возможности подключения, воспользовавшись PowerShell для создания ресурсов в Azure Stack. В целях проверки создайте группу ресурсов для приложения и добавьте в нее виртуальную машину. Используйте команду ниже, чтобы создать группу ресурсов MyResourceGroup.

```PowerShell  
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>Дополнительная информация

- [Разработка шаблонов для Azure Stack](azure-stack-develop-templates.md)
- [Развертывание шаблонов с помощью PowerShell](azure-stack-deploy-template-powershell.md)
- [Модуль Azure Stack 1.7.0](https://docs.microsoft.com/en-us/powershell/azure/azure-stack/overview)
- Вы также можете настроить среду PowerShell для облачного оператора. Это описывается в разделе [Настройка среды PowerShell оператора Azure Stack](../azure-stack-powershell-configure-admin.md).
