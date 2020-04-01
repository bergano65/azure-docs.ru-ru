---
title: Используйте Azure AD в автоматизации Azure для аутентификации в Azure
description: Узнайте, как использовать Azure AD в Azure Automation в качестве поставщика аутентификации в Azure.
services: automation
ms.date: 03/30/2020
ms.topic: conceptual
ms.openlocfilehash: 77476c67761a950430b39d5baddf2c6efd77f1a2
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80479452"
---
# <a name="use-azure-ad-in-azure-automation-to-authenticate-to-azure"></a>Используйте Azure AD в автоматизации Azure для аутентификации в Azure

Служба [Active Directory (AD) Azure](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) позволяет выполнять ряд административных задач, таких как управление пользователями, управление доменами и единая конфигурация. В этой статье описывается, как использовать Azure AD в Azure Automation в качестве поставщика аутентификации для Azure. 

>[!NOTE]
>Эта статья была изменена и теперь содержит сведения о новом модуле Az для Azure PowerShell. Вы по-прежнему можете использовать модуль AzureRM, исправления ошибок для которого будут продолжать выпускаться как минимум до декабря 2020 г. Дополнительные сведения о совместимости модуля Az с AzureRM см. в статье [Introducing the new Azure PowerShell Az module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0) (Знакомство с новым модулем Az для Azure PowerShell). Для инструкций по установке модуля Az на гибридном Runbook Worker [см.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) Для учетной записи Автоматизация вы можете обновить свои модули до последней версии, используя [как обновить модули Azure PowerShell в Azure Automation.](automation-update-azure-modules.md)

## <a name="installing-azure-ad-modules"></a>Установка модулей Azure AD

Включить Azure AD можно через следующие модули PowerShell:

* Активный каталог Azure PowerShell для Graph (модули AzureRM и Az). Azure Automation поставляется с модулем AzureRM и его недавней модернизацией, модулем Az. Функциональность включает неинтерактивную аутентификацию в Azure с помощью проверки подлинности пользователя Azure AD (OrgId). Смотрите [Azure AD 2.0.2.76](https://www.powershellgallery.com/packages/AzureAD/2.0.2.76).

* Microsoft Azure Active Directory для Windows PowerShell (модуль MSOnline). Этот модуль позволяет взаимодействовать с Microsoft Online, включая Office 365.

>[!NOTE]
>PowerShell Core не поддерживает модуль MSOnline. Для использования модуля cmdlets, вы должны запустить их из Windows PowerShell. Вместо модуля MSOnline рекомендуется использовать новый Активный каталог Azure PowerShell для graph модулей. 

### <a name="preinstallation"></a>Предварительной установки

Перед установкой модулей Azure AD на компьютере:

* Удалите предыдущие версии модуля AzureRM/Az и модуля MSOnline. 

* Удалите помощник Microsoft Online Services Sign-In для обеспечения правильной работы новых модулей PowerShell.  

### <a name="install-the-azurerm-and-az-modules"></a>Установка модулей AzureRM и Az

>[!NOTE]
>Для работы с этими модулями необходимо использовать версию PowerShell 5.1 или позже с 64-битной версией Windows. 

1. Установка платформы управления Windows (WMF) 5.1. Смотрите [Установить и настроить WMF 5.1](https://docs.microsoft.com/powershell/scripting/wmf/setup/install-configure?view=powershell-7).

2. Установите AzureRM and/or Az с помощью инструкций в [установке Azure PowerShell на Windows с помощью PowerShellGet.](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps?view=azurermps-6.13.0)

### <a name="install-the-msonline-module"></a>Установка модуля MSOnline

>[!NOTE]
>Чтобы установить модуль MSOnline, вы должны быть членом роли администратора Office 365. Смотрите [о роли админа](https://docs.microsoft.com/microsoft-365/admin/add-users/about-admin-roles?view=o365-worldwide).

1. Убедитесь, что функция Microsoft .NET Framework 3.5.x включена на вашем компьютере. Вполне вероятно, что ваш компьютер имеет более новую версию установлена, но обратная совместимость со старыми версиями рамочной системы .NET может быть включена или отключена. 

2. Установите 64-битную версию [помощника Microsoft Online Services.](https://www.microsoft.com/download/details.aspx?id=41950)

3. Запустите Windows PowerShell в качестве администратора для создания повышенной команды Windows PowerShell.

4. Развертывание активного каталога Azure от [MSOnline 1.0](http://www.powershellgallery.com/packages/MSOnline/1.0).

5. Если вам предложено установить поставщика NuGet, введите Y и нажмите ENTER.

6. Если вам предложено установить модуль из [PSGallery,](https://www.powershellgallery.com/)введите Y и нажмите ENTER.

### <a name="install-support-for-pscredential"></a>Установка поддержки ДЛЯ PSCredential

Azure Automation использует класс [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?view=pscore-6.2.0) для представления ресурса учетных данных. Ваши скрипты `PSCredential` извлекают `Get-AutomationPSCredential` объекты с помощью cmdlet. Для получения дополнительной [Credential assets in Azure Automation](shared-resources/credentials.md)информации см.

## <a name="assigning-a-subscription-administrator"></a>Назначение администратора подписки

Необходимо назначить администратора для подписки Azure. Этот человек имеет роль Владельца для сферы подписки. Смотрите [элемент управления доступом на основе ролей в Azure Automation.](automation-role-based-access-control.md) 

## <a name="changing-the-azure-ad-users-password"></a>Изменение пароля пользователя Azure AD

Чтобы изменить пароль пользователя Azure AD:

1. Вход из Azure.

2. Войдите в Систему администратора в Azure по мере создания только что созданного пользователя Azure AD, используя полное имя пользователя (включая домен) и временный пароль. 

3. Попросите администратора изменить пароль по запросу.

## <a name="configuring-azure-automation-to-use-the-azure-ad-user-to-manage-the-azure-subscription"></a>Настройка автоматизации Azure для использования пользователя Azure AD для управления подпиской Azure

Для связи Azure Automation с Azure AD необходимо получить учетные данные, связанные с подключением Azure к Azure AD. Примерами этих учетных данных являются идентификатор клиента, идентификатор подписки и тому подобное. Подробнее о связи между Azure и Azure AD можно узнать об этом в [присваивайте организацию к активному каталогу Azure.](https://docs.microsoft.com/azure/devops/organizations/accounts/connect-organization-to-azure-ad?view=azure-devops)

## <a name="creating-a-credential-asset"></a>Создание актива учетных данных

Благодаря доступу учетных данных Azure для Azure AD пришло время создать учетный элемент Azure Automation для надежного хранения учетных данных Azure AD, чтобы к ним могли получить доступ к скриптам Runbooks и Desire State Configuration (DSC). Это можно сделать с помощью портала Azure или смдлетов PowerShell.

### <a name="create-the-credential-asset-in-azure-portal"></a>Создание ресурса учетных данных на портале Azure

Для создания ресурса учетных данных можно использовать портал Azure. Сделайте эту операцию из учетной записи автоматизации с помощью **учетных данных** под **общими ресурсами.** Смотрите [учетные данные в Azure Automation](shared-resources/credentials.md).

### <a name="create-the-credential-asset-with-windows-powershell"></a>Создание актива учетных данных с помощью Windows PowerShell

Чтобы подготовить новый актив учетных данных в `PSCredential` Windows PowerShell, скрипт сначала создает объект, используя назначенное имя пользователя и пароль. Затем скрипт использует этот объект для создания актива через вызов в смдлет [New-AzureAutomationCredential.](https://docs.microsoft.com/powershell/module/servicemanagement/azure/new-azureautomationcredential?view=azuresmps-4.0.0) Кроме того, скрипт может вызвать cmdlet [Get-Credential,](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7) чтобы побудить пользователя ввести имя и пароль. Смотрите [учетные данные в Azure Automation](shared-resources/credentials.md). 

## <a name="managing-azure-resources-from-an-azure-automation-runbook"></a>Управление ресурсами Azure из рунисток Автоматизации Azure

Вы можете управлять ресурсами Azure из runbooks Azure Automation с помощью ресурса учетных данных. Ниже приведен пример runbook PowerShell, который собирает ресурс учетных данных для использования для остановки и запуска виртуальных машин в подписке Azure. Этот runbook `Get-AutomationPSCredential` сначала используется для получения учетных данных для проверки подлинности Azure. Затем он вызывает cmdlet [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.6.1) для подключения к Azure с помощью учетных данных. Скрипт использует cmdlet [Select-AzureSubscription](https://docs.microsoft.com/powershell/module/servicemanagement/azure/select-azuresubscription?view=azuresmps-4.0.0) для выбора подписки для работы. 

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

## <a name="next-steps"></a>Следующие шаги

* Вы можете найти информацию об учетных ресурсах Automation в [учетных данных в Azure Automation.](shared-resources/credentials.md)
* Ознакомиться с [модулями Управления в Azure Automation](shared-resources/modules.md) можно узнать, как работать с модулями автоматизации.
* Чтобы узнать больше о методах, которые можно использовать для запуска [Starting a runbook in Azure Automation](automation-starting-a-runbook.md)запуска сборника в Azure Automation, см.
* Для получения дополнительной информации о PowerShell, включая [PowerShell Docs](https://docs.microsoft.com/powershell/scripting/overview)языковые справочные и учебные модули, см.