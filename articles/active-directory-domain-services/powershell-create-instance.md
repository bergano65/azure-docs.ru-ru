---
title: Включение доменных служб Azure DS с помощью PowerShell | Документация Майкрософт
description: Узнайте, как настраивать и включать Azure Active Directory доменные службы с помощью Azure AD PowerShell и Azure PowerShell.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: d4bc5583-6537-4cd9-bc4b-7712fdd9272a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: iainfou
ms.openlocfilehash: 163259af3797b652c9605c171447f4a7d2576c87
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/10/2019
ms.locfileid: "70842709"
---
# <a name="enable-azure-active-directory-domain-services-using-powershell"></a>Включение доменных служб Azure Active Directory с помощью PowerShell

Доменные службы Azure Active Directory (Azure AD DS) предоставляют управляемые доменные службы, отвечающие за присоединение к домену, применение групповой политики, использование протокола LDAP, а также выполнение аутентификации Kerberos или NTLM (полностью поддерживается Windows Server Active Directory). Эти доменные службы можно использовать без необходимости развертывать, администрировать и обновлять контроллеры домена. Azure AD DS интегрируется с существующим клиентом Azure AD. Такая интеграция позволяет пользователям входить в систему с корпоративными учетными данными. При этом вы можете использовать существующие группы и учетные записи пользователей для защиты доступа к ресурсам.

В этой статье показано, как включить Azure AD DS с помощью PowerShell.

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Предварительные требования

Для работы с этой статьей вам потребуются следующие ресурсы:

* Установите и настройте Azure PowerShell.
    * При необходимости выполните инструкции по [установке модуля Azure PowerShell и подключитесь к подписке Azure](/powershell/azure/install-az-ps).
    * Убедитесь, что вы входите в подписку Azure с помощью командлета [Connect-азаккаунт][Connect-AzAccount] .
* Установите и настройте Azure AD PowerShell.
    * При необходимости следуйте инструкциям по [установке модуля Azure AD PowerShell и подключению к Azure AD](/powershell/azure/active-directory/install-adv2).
    * Убедитесь, что вы входите в клиент Azure AD с помощью командлета [Connect-AzureAD][Connect-AzureAD] .
* Привилегии *глобального администратора* для клиента Azure AD, чтобы включить доменные службы Azure AD.
* Для создания нужных ресурсов Azure AD DS требуются привилегии *участника* в подписке Azure.

## <a name="create-required-azure-ad-resources"></a>Создание необходимых ресурсов Azure AD

Для AD DS Azure требуется субъект-служба и группа Azure AD. Эти ресурсы позволяют управляемому домену Azure AD DS синхронизировать данные и определять, какие пользователи имеют административные разрешения в управляемом домене.

Сначала создайте субъект-службу Azure AD для AD DS Azure для взаимодействия и проверки подлинности. Идентификатор конкретного приложения используется с именем *службы контроллера домена* и идентификатором *2565bd9d-da50-47d4-8b85-4c97f669dc36*. Не изменяйте этот идентификатор приложения.

Создайте субъект-службу Azure AD с помощью командлета [New AzureADServicePrincipal][New-AzureADServicePrincipal] :

```powershell
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
```

Теперь создайте группу Azure AD с именем *Администраторы контроллера домена AAD*. Пользователи, добавленные в эту группу, получают разрешения на выполнение задач администрирования в управляемом домене Azure AD DS.

Создайте группу *администраторов контроллера домена AAD* с помощью командлета [New-AzureADGroup][New-AzureADGroup] :

```powershell
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"
```

Создав группу *администраторов контроллера домена AAD* , добавьте пользователя в группу с помощью командлета [Add-AzureADGroupMember][Add-AzureADGroupMember] . Сначала необходимо получить идентификатор объекта группы *администраторов контроллера домена AAD* с помощью командлета [Get-AzureADGroup][Get-AzureADGroup] , а затем идентификатор объекта нужного пользователя с помощью командлета [Get-AzureADUser][Get-AzureADUser] .

В следующем примере это идентификатор объекта пользователя для учетной записи с именем UPN `admin@contoso.onmicrosoft.com`. Замените эту учетную запись пользователя именем участника-пользователя, которого вы хотите добавить в группу *администраторов контроллера домена AAD* :

```powershell
# First, retrieve the object ID of the newly created 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# Now, retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq 'admin@contoso.onmicrosoft.com'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId
```

## <a name="create-supporting-azure-resources"></a>Создание вспомогательных ресурсов Azure

Сначала зарегистрируйте поставщик ресурсов доменных служб Azure AD с помощью командлета [Register-азресаурцепровидер][Register-AzResourceProvider] :

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD
```

Затем создайте группу ресурсов с помощью командлета [New-азресаурцеграуп][New-AzResourceGroup] . В следующем примере группа ресурсов называется *myResourceGroup* и создается в *westus* регионе. Используйте собственное имя и желаемый регион:

```powershell
$ResourceGroupName = "myResourceGroup"
$AzureLocation = "westus"

# Create the resource group.
New-AzResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation
```

Создайте виртуальную сеть и подсети для доменных служб Azure AD. Создаются две подсети — одна для *DomainServices*, а другая для *рабочих нагрузок*. AD DS Azure развертывается в выделенной подсети *DomainServices* . Не развертывайте другие приложения или рабочие нагрузки в этой подсети. Используйте отдельные *рабочие нагрузки* или другие подсети для остальных виртуальных машин.

Создайте подсети с помощью командлета [New-азвиртуалнетворксубнетконфиг][New-AzVirtualNetworkSubnetConfig] , а затем создайте виртуальную сеть с помощью командлета [New-азвиртуалнетворк][New-AzVirtualNetwork] .

```powershell
$VnetName = "myVnet"

# Create the dedicated subnet for AAD Domain Services.
$AaddsSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name DomainServices `
  -AddressPrefix 10.0.0.0/24

$WorkloadSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name Workloads `
  -AddressPrefix 10.0.1.0/24

# Create the virtual network in which you will enable Azure AD Domain Services.
$Vnet= New-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location westus `
  -Name $VnetName `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $AaddsSubnet,$WorkloadSubnet
```

## <a name="create-an-azure-ad-ds-managed-domain"></a>Создание управляемого домена AD DS Azure

Теперь создадим управляемый домен Azure AD DS. Задайте идентификатор подписки Azure, а затем укажите имя управляемого домена, например *contoso.com*. Идентификатор подписки можно получить с помощью командлета [Get-азсубскриптион][Get-AzSubscription] .

```powershell
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "contoso.com"

# Enable Azure AD Domain Services for the directory.
New-AzResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
  -Location $AzureLocation `
  -Properties @{"DomainName"=$ManagedDomainName; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
  -Force -Verbose
```

Создание ресурса и возврат управления в командную строку PowerShell занимает несколько минут. Управляемый домен AD DS Azure по всей важности подготавливается в фоновом режиме и может занять до часа для завершения развертывания. В портал Azure на странице **Обзор** управляемого домена AD DS Azure отображается текущее состояние на протяжении этого этапа развертывания.

Когда портал Azure показывает, что управляемый домен AD DS Azure завершил подготовку, необходимо выполнить следующие задачи:

* Обновите параметры DNS для виртуальной сети, чтобы виртуальные машины могли найти управляемый домен для присоединения к нему или для аутентификации.
    * Чтобы настроить DNS, выберите управляемый домен Azure AD DS на портале. В окне **Обзор** появится запрос на автоматическую настройку этих параметров DNS.
* [Включите синхронизацию паролей с доменными службами Azure AD](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) , чтобы конечные пользователи могли входить в управляемый домен с использованием своих корпоративных учетных данных.

## <a name="complete-powershell-script"></a>Завершение выполнения скрипта PowerShell

Следующий полный сценарий PowerShell объединяет все задачи, описанные в этой статье. Скопируйте скрипт и сохраните его в файл с `.ps1` расширением. Запустите скрипт в локальной консоли PowerShell или [Azure Cloud Shell][cloud-shell].

> [!NOTE]
> Чтобы включить AD DS Azure, необходимо быть глобальным администратором для клиента Azure AD. Вам также требуются права *участника* в подписке Azure.

```powershell
# Change the following values to match your deployment.
$AaddsAdminUserUpn = "admin@contoso.onmicrosoft.com"
$ResourceGroupName = "myResourceGroup"
$VnetName = "myVnet"
$AzureLocation = "westus"
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "contoso.com"

# Connect to your Azure AD directory.
Connect-AzureAD

# Login to your Azure subscription.
Connect-AzAccount

# Create the service principal for Azure AD Domain Services.
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"

# Create the delegated administration group for AAD Domain Services.
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"

# First, retrieve the object ID of the newly created 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# Now, retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq '$AaddsAdminUserUpn'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId

# Register the resource provider for Azure AD Domain Services with Resource Manager.
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD

# Create the resource group.
New-AzResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation

# Create the dedicated subnet for AAD Domain Services.
$AaddsSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name DomainServices `
  -AddressPrefix 10.0.0.0/24

$WorkloadSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name Workloads `
  -AddressPrefix 10.0.1.0/24

# Create the virtual network in which you will enable Azure AD Domain Services.
$Vnet=New-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location $AzureLocation `
  -Name $VnetName `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $AaddsSubnet,$WorkloadSubnet

# Enable Azure AD Domain Services for the directory.
New-AzResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
  -Location $AzureLocation `
  -Properties @{"DomainName"=$ManagedDomainName; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
  -Force -Verbose
```

Создание ресурса и возврат управления в командную строку PowerShell занимает несколько минут. Управляемый домен AD DS Azure по всей важности подготавливается в фоновом режиме и может занять до часа для завершения развертывания. В портал Azure на странице **Обзор** управляемого домена AD DS Azure отображается текущее состояние на протяжении этого этапа развертывания.

Когда портал Azure показывает, что управляемый домен AD DS Azure завершил подготовку, необходимо выполнить следующие задачи:

* Обновите параметры DNS для виртуальной сети, чтобы виртуальные машины могли найти управляемый домен для присоединения к нему или для аутентификации.
    * Чтобы настроить DNS, выберите управляемый домен Azure AD DS на портале. В окне **Обзор** появится запрос на автоматическую настройку этих параметров DNS.
* [Включите синхронизацию паролей с доменными службами Azure AD](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) , чтобы конечные пользователи могли входить в управляемый домен с использованием своих корпоративных учетных данных.

## <a name="next-steps"></a>Следующие шаги

Чтобы просмотреть управляемый домен Azure AD DS в действии, можно [присоединить к домену виртуальную машину Windows][windows-join], [настроить защищенный протокол LDAP][tutorial-ldaps]и [настроить синхронизацию хэша паролей][tutorial-phs].

<!-- INTERNAL LINKS -->
[windows-join]: join-windows-vm.md
[tutorial-ldaps]: tutorial-configure-ldaps.md
[tutorial-phs]: tutorial-configure-password-hash-sync.md

<!-- EXTERNAL LINKS -->
[Connect-AzAccount]: /powershell/module/Az.Accounts/Connect-AzAccount
[Connect-AzureAD]: /powershell/module/AzureAD/Connect-AzureAD
[New-AzureADServicePrincipal]: /powershell/module/AzureAD/New-AzureADServicePrincipal
[New-AzureADGroup]: /powershell/module/AzureAD/New-AzureADGroup
[Add-AzureADGroupMember]: /powershell/module/AzureAD/Add-AzureADGroupMember
[Get-AzureADGroup]: /powershell/module/AzureAD/Get-AzureADGroup
[Get-AzureADUser]: /powershell/module/AzureAD/Get-AzureADUser
[Register-AzResourceProvider]: /powershell/module/Az.Resources/Register-AzResourceProvider
[New-AzResourceGroup]: /powershell/module/Az.Resources/New-AzResourceGroup
[New-AzVirtualNetworkSubnetConfig]: /powershell/module/Az.Network/New-AzVirtualNetworkSubnetConfig
[New-AzVirtualNetwork]: /powershell/module/Az.Network/New-AzVirtualNetwork
[Get-AzSubscription]: /powershell/module/Az.Accounts/Get-AzSubscription
[cloud-shell]: /azure/cloud-shell/cloud-shell-windows-users
