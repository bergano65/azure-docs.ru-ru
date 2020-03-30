---
title: Включить службы домена Azure DS с помощью PowerShell (ru) Документы Майкрософт
description: Узнайте, как настроить и включить службы домена Активных каталогов Azure с помощью Azure AD PowerShell и Azure PowerShell.
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
ms.openlocfilehash: ee85002aea962dfa675ac6c09a6bfbaeba8e9e79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77613235"
---
# <a name="enable-azure-active-directory-domain-services-using-powershell"></a>Включение доменных служб Azure Active Directory с помощью PowerShell

Доменные службы Azure Active Directory (Azure AD DS) предоставляют управляемые доменные службы, отвечающие за присоединение к домену, применение групповой политики, использование протокола LDAP, а также выполнение аутентификации Kerberos или NTLM (полностью поддерживается Windows Server Active Directory). Эти доменные службы можно использовать без необходимости развертывать, администрировать и обновлять контроллеры домена. Azure AD DS интегрируется с существующим клиентом Azure AD. Такая интеграция позволяет пользователям входить в систему с корпоративными учетными данными. При этом вы можете использовать существующие группы и учетные записи пользователей для защиты доступа к ресурсам.

В этой статье показано, как включить Azure AD DS с помощью PowerShell.

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Предварительные требования

Для завершения этой статьи необходимы следующие ресурсы:

* Установите и настройте Azure PowerShell.
    * При необходимости следуйте [инструкциям по установке модуля Azure PowerShell и подключитесь к подписке Azure.](/powershell/azure/install-az-ps)
    * Убедитесь, что вы впишетесь в подписку Azure с помощью cmdlet [Connect-AzAccount.][Connect-AzAccount]
* Установка и настройка Azure AD PowerShell.
    * При необходимости следуйте инструкциям [по установке модуля Azure AD PowerShell и подключитесь к Azure AD.](/powershell/azure/active-directory/install-adv2)
    * Убедитесь, что вы впишетесь в ваш aure AD-арендатора с помощью [cmdlet Connect-AzureAD.][Connect-AzureAD]
* Привилегии *глобального администратора* для клиента Azure AD, чтобы включить доменные службы Azure AD.
* Для создания нужных ресурсов Azure AD DS требуются привилегии *участника* в подписке Azure.

## <a name="create-required-azure-ad-resources"></a>Создание необходимых ресурсов Azure AD

Azure AD DS требует основного обслуживания и группы Azure AD. Эти ресурсы позволяют управляемому домену Azure AD синхронизировать данные и определять, какие пользователи имеют административные разрешения в управляемом домене.

Во-первых, создайте принцип службы Azure AD для Azure AD DS для связи и аутентификации. Используется специальный идентификатор приложения под названием *Domain Controller Services* с идентификатором *2565bd9d-da50-47d4-8b85-4c97f669dc36*. Не изменяйте идентификатор этого приложения.

Создайте принцип службы Azure AD с помощью cmdlet [New-AzureADServicePrincipal:][New-AzureADServicePrincipal]

```powershell
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
```

Теперь создайте группу Azure AD под названием *AAD DC Administrators.* Пользователям, добавленным в эту группу, выдаются разрешения на выполнение задач администрирования в домене Azure AD DS.

Создайте группу *администраторов AAD DC* с помощью cmdlet [New-AzureADGroup:][New-AzureADGroup]

```powershell
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"
```

С помощью *группы администраторов AAD DC* добавьте пользователя в группу с помощью cmdlet [Add-AzureADGroupMember.][Add-AzureADGroupMember] Сначала вы получаете идентификатор группового объекта *AAD DC* с помощью cmdlet [Get-AzureADGroup,][Get-AzureADGroup] а затем идентификатора объекта пользователя с помощью cmdlet [Get-AzureADUser.][Get-AzureADUser]

В следующем примере идентификатор объекта пользователя `admin@aaddscontoso.onmicrosoft.com`для учетной записи с UPN. Замените эту учетную запись пользователя UPN пользователя, который вы хотите добавить в группу *администраторов AAD DC:*

```powershell
# First, retrieve the object ID of the newly created 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# Now, retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq 'admin@aaddscontoso.onmicrosoft.com'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId
```

## <a name="create-supporting-azure-resources"></a>Создание вспомогательных ресурсов Azure

Во-первых, зарегистрируйте поставщика ресурсов для доменов Azure AD, используя cmdlet [Register-AzResourceProvider:][Register-AzResourceProvider]

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD
```

Затем создайте группу ресурсов с помощью cmdlet [New-AzResourceGroup.][New-AzResourceGroup] В следующем примере ресурсная группа называется *myResourceGroup* и создается в регионе *Westus.* Используйте свое имя и желаемую область:

```powershell
$ResourceGroupName = "myResourceGroup"
$AzureLocation = "westus"

# Create the resource group.
New-AzResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation
```

Создайте виртуальную сеть и подсети для служб доменов Azure AD. Созданы две подсети - одна для *DomainServices*и одна для *рабочих нагрузок.* Azure AD DS развертывается в выделенной подсети *DomainServices.* Не развертывайте другие приложения или рабочие нагрузки в эту подсеть. Используйте отдельные *рабочие нагрузки* или другие подсети для остальных вс-мидов.

Создайте подсети с помощью смдлета [New-AzVirtualNetworkSubnetConfig,][New-AzVirtualNetworkSubnetConfig] а затем создайте виртуальную сеть с помощью смдлета [New-AzVirtualNetwork.][New-AzVirtualNetwork]

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

## <a name="create-an-azure-ad-ds-managed-domain"></a>Создание домена Azure AD DS, управляемого DS

Теперь создадим домен СУ Azure AD DS. Установите идентификатор подписки Azure, а затем укажите имя для управляемого домена, например *aaddscontoso.com.* Вы можете получить идентификатор подписки с помощью cmdlet [Get-AzSubscription.][Get-AzSubscription]

Если вы выбрали регион, который поддерживает зоны доступности, ресурсы Azure AD DS распределяются между зонами для дополнительной избыточности.

Зоны доступности — уникальные физические расположения в пределах одного региона Azure. Каждая зона состоит из одного или нескольких центров обработки данных, оснащенных независимыми системами электроснабжения, охлаждения и сетевого взаимодействия. Чтобы обеспечить отказоустойчивость, во всех включенных регионах используются минимум три отдельные зоны.

Вы не можете настроить распределение Azure AD DS между зонами. Платформа Azure автоматически обрабатывает распределение ресурсов зоны. Для получения дополнительной информации и просмотра доступности региона [см.][availability-zones]

```powershell
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "aaddscontoso.com"

# Enable Azure AD Domain Services for the directory.
New-AzResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
  -Location $AzureLocation `
  -Properties @{"DomainName"=$ManagedDomainName; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
  -Force -Verbose
```

Создание ресурса и возврат управления в запрос PowerShell занимает несколько минут. Управляемый домен Azure AD DS продолжает быть подготовлен в фоновом режиме и может занять до часа для завершения развертывания. На портале Azure страница **«Обзор»** для домена Azure AD DS показывает текущее состояние на протяжении всего этапа развертывания.

Когда портал Azure показывает, что управляемый домен Azure AD DS закончил подготовку, необходимо выполнить следующие задачи:

* Обновите параметры DNS для виртуальной сети, чтобы виртуальные машины могли найти управляемый домен для присоединения к нему или для аутентификации.
    * Чтобы настроить DNS, выберите на портале управляемый DS AD Azure AD. В окне **Обзора** вам предлагается автоматически настроить настройки DNS.
* Если в регионе, поддерживающем зоны доступности, создан домен Azure AD DS, создайте группу сетевой безопасности для ограничения трафика в виртуальной сети для домена Azure AD DS. Создается стандартный балансер нагрузки Azure, который требует размещения этих правил. Эта группа сетевой безопасности обеспечивает безопасность DS Azure AD и необходима для правильной работы управляемого домена.
    * Чтобы создать группу сетевой безопасности и требуемые правила, выберите на портале управляемый DS AD Azure. В окне **Обзора** предлагается автоматически создавать и настраивать группу сетевой безопасности.
* [Позволяет синхронизировать пароли в службы домена Azure AD,](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) чтобы конечные пользователи могли войти в управляемый домен, используя свои корпоративные учетные данные.

## <a name="complete-powershell-script"></a>Полный сценарий PowerShell

Следующий полный сценарий PowerShell сочетает в себе все задачи, указанные в этой статье. Копируйте скрипт и сохраните `.ps1` его в файле с расширением. Запустите скрипт в локальной консоли PowerShell или [в облачной оболочке Azure.][cloud-shell]

> [!NOTE]
> Чтобы включить AD-развращец Azure AD, необходимо стать глобальным администратором для арендатора Azure AD. Вам также нужны по крайней мере привилегии *вкладчика* в подписке Azure.

```powershell
# Change the following values to match your deployment.
$AaddsAdminUserUpn = "admin@aaddscontoso.onmicrosoft.com"
$ResourceGroupName = "myResourceGroup"
$VnetName = "myVnet"
$AzureLocation = "westus"
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "aaddscontoso.com"

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

Создание ресурса и возврат управления в запрос PowerShell занимает несколько минут. Управляемый домен Azure AD DS продолжает быть подготовлен в фоновом режиме и может занять до часа для завершения развертывания. На портале Azure страница **«Обзор»** для домена Azure AD DS показывает текущее состояние на протяжении всего этапа развертывания.

Когда портал Azure показывает, что управляемый домен Azure AD DS закончил подготовку, необходимо выполнить следующие задачи:

* Обновите параметры DNS для виртуальной сети, чтобы виртуальные машины могли найти управляемый домен для присоединения к нему или для аутентификации.
    * Чтобы настроить DNS, выберите на портале управляемый DS AD Azure AD. В окне **Обзора** вам предлагается автоматически настроить настройки DNS.
* Если в регионе, поддерживающем зоны доступности, создан домен Azure AD DS, создайте группу сетевой безопасности для ограничения трафика в виртуальной сети для домена Azure AD DS. Создается стандартный балансер нагрузки Azure, который требует размещения этих правил. Эта группа сетевой безопасности обеспечивает безопасность DS Azure AD и необходима для правильной работы управляемого домена.
    * Чтобы создать группу сетевой безопасности и требуемые правила, выберите на портале управляемый DS AD Azure. В окне **Обзора** предлагается автоматически создавать и настраивать группу сетевой безопасности.
* [Позволяет синхронизировать пароли в службы домена Azure AD,](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) чтобы конечные пользователи могли войти в управляемый домен, используя свои корпоративные учетные данные.

## <a name="next-steps"></a>Дальнейшие действия

Чтобы увидеть в действии правительственный домен Azure AD DS, можно [объединить windows VM,][windows-join] [настроить безопасную синхронизацию LDAP][tutorial-ldaps]и [настроить синхронизацию хэш-пароля.][tutorial-phs]

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
[availability-zones]: ../availability-zones/az-overview.md
