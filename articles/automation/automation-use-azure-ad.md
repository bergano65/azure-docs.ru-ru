---
title: Использование Azure AD в службе автоматизации Azure для проверки подлинности в Azure
description: Узнайте, как использовать Azure AD в службе автоматизации Azure в качестве поставщика для проверки подлинности в Azure.
services: automation
ms.date: 03/30/2020
ms.topic: conceptual
ms.openlocfilehash: 90338a1ffa79e6c2347832cb2e74633db02ec72d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80548342"
---
# <a name="use-azure-ad-in-azure-automation-to-authenticate-to-azure"></a>Использование Azure AD в службе автоматизации Azure для проверки подлинности в Azure

Служба [Azure Active Directory (AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) позволяет выполнять ряд административных задач, таких как Управление пользователями, Управление доменами и Настройка единого входа. В этой статье описывается, как использовать Azure AD в службе автоматизации Azure в качестве поставщика для проверки подлинности в Azure. 

>[!NOTE]
>Эта статья была изменена и теперь содержит сведения о новом модуле Az для Azure PowerShell. Вы по-прежнему можете использовать модуль AzureRM, исправления ошибок для которого будут продолжать выпускаться как минимум до декабря 2020 г. Дополнительные сведения о совместимости модуля Az с AzureRM см. в статье [Introducing the new Azure PowerShell Az module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0) (Знакомство с новым модулем Az для Azure PowerShell). Инструкции по установке модуля Az в гибридной рабочей роли Runbook см. в статье об [установке модуля Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Чтобы обновить модули в учетной записи службы автоматизации, см. руководство по [обновлению модулей Azure PowerShell в службе автоматизации Azure](automation-update-azure-modules.md).

## <a name="installing-azure-ad-modules"></a>Установка модулей Azure AD

Вы можете включить Azure AD с помощью следующих модулей PowerShell:

* Azure Active Directory PowerShell для Graph (модули AzureRM и AZ). Служба автоматизации Azure поставляется с модулем AzureRM и последним обновлением, модулем AZ. Функциональность включает неинтерактивную проверку подлинности в Azure с помощью проверки подлинности на основе учетных данных пользователя Azure AD (OrgId). См. раздел [Azure AD 2.0.2.76](https://www.powershellgallery.com/packages/AzureAD/2.0.2.76).

* Microsoft Azure Active Directory для Windows PowerShell (модуль MSOnline). Этот модуль обеспечивает взаимодействие с Microsoft Online, включая Office 365.

>[!NOTE]
>PowerShell Core не поддерживает модуль MSOnline. Чтобы использовать командлеты модуля, необходимо запустить их из Windows PowerShell. Вместо модуля MSOnline рекомендуется использовать более новую версию Azure Active Directory PowerShell для модулей Graph. 

### <a name="preinstallation"></a>Предустановки

Перед установкой модулей Azure AD на компьютере выполните следующие действия.

* Удалите все предыдущие версии модуля AzureRM/AZ и модуля MSOnline. 

* Удалите помощник по входу в Microsoft Online Services, чтобы убедиться в правильности работы новых модулей PowerShell.  

### <a name="install-the-azurerm-and-az-modules"></a>Установка модулей AzureRM и AZ

>[!NOTE]
>Для работы с этими модулями необходимо использовать PowerShell версии 5,1 или более поздней с 64-разрядной версией Windows. 

1. Установите Windows Management Framework (WMF) 5,1. См. статью [Установка и настройка WMF 5,1](https://docs.microsoft.com/powershell/scripting/wmf/setup/install-configure?view=powershell-7).

2. Установите AzureRM и (или) AZ с помощью инструкций в [статье установка Azure PowerShell в Windows с помощью PowerShellGet](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps?view=azurermps-6.13.0).

### <a name="install-the-msonline-module"></a>Установка модуля MSOnline

>[!NOTE]
>Чтобы установить модуль MSOnline, необходимо быть членом роли администратора Office 365. См. раздел [Общие сведения о ролях администратора](https://docs.microsoft.com/microsoft-365/admin/add-users/about-admin-roles?view=o365-worldwide).

1. Убедитесь, что на компьютере включен компонент Microsoft .NET Framework 3.5. x. Скорее всего, на компьютере установлена более новая версия, но обратная совместимость с более старыми версиями .NET Framework может быть включена или отключена. 

2. Установите 64-разрядную версию [помощника по входу в Microsoft Online Services](https://www.microsoft.com/download/details.aspx?id=41950).

3. Запустите Windows PowerShell от имени администратора, чтобы создать командную строку Windows PowerShell с повышенными привилегиями.

4. Развертывание Azure Active Directory из [MSOnline 1,0](http://www.powershellgallery.com/packages/MSOnline/1.0).

5. Если появится запрос на установку поставщика NuGet, введите Y и нажмите клавишу ВВОД.

6. Если появится запрос на установку модуля из [PSGallery](https://www.powershellgallery.com/), введите Y и нажмите клавишу ВВОД.

### <a name="install-support-for-pscredential"></a>Установить поддержку для PSCredential

Служба автоматизации Azure использует класс [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?view=pscore-6.2.0) для представления ресурса учетных данных. Скрипты извлекают `PSCredential` объекты с `Get-AutomationPSCredential` помощью командлета. Дополнительные сведения см. [в статье ресурсы учетных данных в службе автоматизации Azure](shared-resources/credentials.md).

## <a name="assigning-a-subscription-administrator"></a>Назначение администратора подписки

Необходимо назначить администратора для подписки Azure. Этот пользователь имеет роль владельца для области подписки. См. раздел [Управление доступом на основе ролей в службе автоматизации Azure](automation-role-based-access-control.md). 

## <a name="changing-the-azure-ad-users-password"></a>Изменение пароля пользователя Azure AD

Чтобы изменить пароль пользователя Azure AD, сделайте следующее:

1. Выйдите из Azure.

2. Войдите в Azure как только что созданное пользователем Azure AD, используя полное имя пользователя (включая домен) и временный пароль. 

3. Попросите администратора изменить пароль при появлении запроса.

## <a name="configuring-azure-automation-to-use-the-azure-ad-user-to-manage-the-azure-subscription"></a>Настройка службы автоматизации Azure для использования пользователя Azure AD для управления подпиской Azure

Чтобы служба автоматизации Azure могла взаимодействовать с Azure AD, необходимо получить учетные данные, связанные с подключением Azure к Azure AD. Примеры этих учетных данных: идентификатор клиента, идентификатор подписки и т. д. Дополнительные сведения о подключении между Azure и Azure AD см. [в статье подключение Организации к Azure Active Directory](https://docs.microsoft.com/azure/devops/organizations/accounts/connect-organization-to-azure-ad?view=azure-devops).

## <a name="creating-a-credential-asset"></a>Создание ресурса учетных данных

Используя учетные данные Azure для Azure AD, можно создать ресурс-контейнер учетных данных службы автоматизации Azure для безопасного хранения учетных данных Azure AD, чтобы модули Runbook и сценарии настройки Desired State Configuration (DSC) могли получить к ним доступ. Это можно сделать с помощью командлетов портал Azure или PowerShell.

### <a name="create-the-credential-asset-in-azure-portal"></a>Создание ресурса учетных данных в портал Azure

Для создания ресурса учетных данных можно использовать портал Azure. Выполните эту операцию из учетной записи службы автоматизации, используя **учетные данные** в разделе **Общие ресурсы**. См. раздел [ресурсы учетных данных в службе автоматизации Azure](shared-resources/credentials.md).

### <a name="create-the-credential-asset-with-windows-powershell"></a>Создание ресурса учетных данных с помощью Windows PowerShell

Чтобы подготовить новый ресурс учетных данных в Windows PowerShell, сценарий сначала создает `PSCredential` объект, используя назначенное имя пользователя и пароль. Затем скрипт использует этот объект для создания ресурса с помощью вызова командлета [New-азуреаутоматионкредентиал](https://docs.microsoft.com/powershell/module/servicemanagement/azure/new-azureautomationcredential?view=azuresmps-4.0.0) . Кроме того, скрипт может вызвать командлет [Get-Credential](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7) , чтобы предложить пользователю ввести имя и пароль. См. раздел [ресурсы учетных данных в службе автоматизации Azure](shared-resources/credentials.md). 

## <a name="managing-azure-resources-from-an-azure-automation-runbook"></a>Управление ресурсами Azure из модуля Runbook службы автоматизации Azure

Вы можете управлять ресурсами Azure из модулей Runbook службы автоматизации Azure с помощью ресурса учетных данных. Ниже приведен пример модуля Runbook PowerShell, который собирает ресурс учетных данных, используемый для остановки и запуска виртуальных машин в подписке Azure. Сначала этот модуль Runbook `Get-AutomationPSCredential` использует для получения учетных данных, используемых для проверки подлинности в Azure. Затем он вызывает командлет [Connect-азаккаунт](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.6.1) для подключения к Azure с использованием учетных данных. Скрипт использует командлет [SELECT-AzureSubscription](https://docs.microsoft.com/powershell/module/servicemanagement/azure/select-azuresubscription?view=azuresmps-4.0.0) , чтобы выбрать подписку для работы. 

```azurepowershell
Workflow Stop-Start-AzureVM 
{ 
    Param 
    (    
        [Parameter(Mandatory=$true)][ValidateNotNullOrEmpty()] 
        [String] 
        $AzureSubscriptionId, 
        [Parameter(Mandatory=$true)][ValidateNotNullOrEmpty()] 
        [String] 
        $AzureVMList="All", 
        [Parameter(Mandatory=$true)][ValidateSet("Start","Stop")] 
        [String] 
        $Action 
    ) 
     
    $credential = Get-AutomationPSCredential -Name 'AzureCredential' 
    Connect-AzAccount -Credential $credential 
    Select-AzureSubscription -SubscriptionId $AzureSubscriptionId 
 
    if($AzureVMList -ne "All") 
    { 
        $AzureVMs = $AzureVMList.Split(",") 
        [System.Collections.ArrayList]$AzureVMsToHandle = $AzureVMs 
    } 
    else 
    { 
        $AzureVMs = (Get-AzVM).Name 
        [System.Collections.ArrayList]$AzureVMsToHandle = $AzureVMs 
 
    } 
 
    foreach($AzureVM in $AzureVMsToHandle) 
    { 
        if(!(Get-AzVM | ? {$_.Name -eq $AzureVM})) 
        { 
            throw " AzureVM : [$AzureVM] - Does not exist! - Check your inputs " 
        } 
    } 
 
    if($Action -eq "Stop") 
    { 
        Write-Output "Stopping VMs"; 
        foreach -parallel ($AzureVM in $AzureVMsToHandle) 
        { 
            Get-AzVM | ? {$_.Name -eq $AzureVM} | Stop-AzVM -Force 
        } 
    } 
    else 
    { 
        Write-Output "Starting VMs"; 
        foreach -parallel ($AzureVM in $AzureVMsToHandle) 
        { 
            Get-AzVM | ? {$_.Name -eq $AzureVM} | Start-AzVM 
        } 
    } 
}
```  

## <a name="next-steps"></a>Дальнейшие шаги

* Сведения о ресурсах учетных данных службы автоматизации можно найти в [ресурсах учетных данных в службе автоматизации Azure](shared-resources/credentials.md).
* Сведения о работе с модулями автоматизации см. в статье [Управление модулями в службе автоматизации Azure](shared-resources/modules.md) .
* Дополнительные сведения о методах, которые можно использовать для запуска модуля Runbook в службе автоматизации Azure, см. в статье [Запуск модуля Runbook в службе автоматизации Azure](automation-starting-a-runbook.md).
* Дополнительные сведения о PowerShell, включая Справочник по языку и обучающие модули, см. в документации по [PowerShell](https://docs.microsoft.com/powershell/scripting/overview).