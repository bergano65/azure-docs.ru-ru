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
ms.date: 11/08/2018
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: f5df27df5901d6568b30e1ae4f40cae8b3de4c86
ms.sourcegitcommit: d372d75558fc7be78b1a4b42b4245f40f213018c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/09/2018
ms.locfileid: "51298825"
---
# <a name="connect-to-azure-stack-with-powershell-as-an-operator"></a>Подключитесь к Azure Stack с помощью PowerShell в роли оператора.

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

Azure Stack можно настроить для управления такими ресурсами, как создание предложений, планов, квот и предупреждений, с помощью PowerShell. Этот раздел поможет настроить среду оператора.

## <a name="prerequisites"></a>Предварительные требования

Выполните следующие предварительные требования с помощью [пакета средств разработки](.\asdk\asdk-connect.md#connect-with-rdp) или внешнего клиента на базе Windows (при [подключении к ASDK через VPN](.\asdk\asdk-connect.md#connect-with-vpn)). 

 - Установите [совместимые с Azure Stack модули Azure PowerShell](azure-stack-powershell-install.md).  
 - Скачайте [средства, необходимые для работы с Azure Stack](azure-stack-powershell-download.md).  

## <a name="configure-the-operator-environment-and-sign-in-to-azure-stack"></a>Настройка среды оператора и вход в Azure Stack

Настройте среду оператора Azure Stack с помощью PowerShell. Выполните один из следующих сценариев: замените tenantName Azure AD, конечную точку GraphAudience и значения ArmEndpoint собственной конфигурацией среды.

````PowerShell  
    # For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external.
    # To get this value for Azure Stack integrated systems, contact your service provider.
    $ArmEndpoint = "<Admin Resource Manager endpoint for your environment>"

    # Register an AzureRM environment that targets your Azure Stack instance
    Add-AzureRmEnvironment -Name "AzureStackAdmin" -ARMEndpoint $ArmEndpoint -AzureKeyVaultDnsSuffix adminvault.local.azurestack.external -AzureKeyVaultServiceEndpointResourceId https://adminvault.local.azurestack.external


    # After signing in to your environment, Azure Stack cmdlets
    # can be easily targeted at your Azure Stack instance.
    Add-AzureRmAccount -EnvironmentName "AzureStackAdmin" -TenantId $tenantId
````

## <a name="test-the-connectivity"></a>Проверка подключения

Теперь, когда все настроено, можно создавать ресурсы в Azure Stack с помощью Azure PowerShell. Например, можно создать группу ресурсов для приложения и добавить виртуальную машину. Используйте команду ниже, чтобы создать группу ресурсов с именем **MyResourceGroup**.

```powershell
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>Дополнительная информация

 - [Разработка шаблонов для Azure Stack](user/azure-stack-develop-templates.md)
 - [Развертывание шаблонов с помощью PowerShell](user/azure-stack-deploy-template-powershell.md)