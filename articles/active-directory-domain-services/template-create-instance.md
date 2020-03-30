---
title: Включить службы домена Azure DS с помощью шаблона Документы Майкрософт
description: Узнайте, как настроить и включить службы домена активных каталогов Azure с помощью шаблона управления ресурсами Azure
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: iainfou
ms.openlocfilehash: 2daadb539bc08df37f15c187866b735e45309288
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77612793"
---
# <a name="create-an-azure-active-directory-domain-services-managed-domain-using-an-azure-resource-manager-template"></a>Создание домена с помощью шаблона Управления ресурсами Azure Active Directory Domain Services, управляемого

Доменные службы Azure Active Directory (Azure AD DS) предоставляют управляемые доменные службы, отвечающие за присоединение к домену, применение групповой политики, использование протокола LDAP, а также выполнение аутентификации Kerberos или NTLM (полностью поддерживается Windows Server Active Directory). Эти доменные службы можно использовать без необходимости развертывать, администрировать и обновлять контроллеры домена. Azure AD DS интегрируется с существующим клиентом Azure AD. Такая интеграция позволяет пользователям входить в систему с корпоративными учетными данными. При этом вы можете использовать существующие группы и учетные записи пользователей для защиты доступа к ресурсам.

В этой статье показано, как включить DD Azure AD с помощью шаблона управления ресурсами Azure. Вспомогательные ресурсы создаются с помощью Azure PowerShell.

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

## <a name="dns-naming-requirements"></a>Требования к именованию DNS

При создании экземпляра Azure AD DS указывается имя DNS. При выборе имени DNS следует учитывать ряд рекомендаций.

* **Встроенное доменное имя:** По умолчанию используется встроенное доменное имя каталога (суффикс *.onmicrosoft.com).* Если вы хотите разрешить для управляемого домена доступ по защищенному протоколу LDAP, вы не сможете создать цифровой сертификат для защиты подключения к домену по умолчанию. Домен *onmicrosoft.com* принадлежит корпорации Майкрософт, поэтому центр сертификации не будет выдавать для него сертификаты.
* **Пользовательские доменные имена:** Наиболее распространенным подходом является указание пользовательского доменного имени, обычно которое у вас уже есть и является приемлемым. При использовании личного домена с поддержкой маршрутизации трафик можно передавать так, нужно приложениям.
* **Недостоверные суффиксы домена:** Обычно мы рекомендуем вам избегать суффикса доменных имен, например *contoso.local*. Суффикс *.local* не маршрутизируется и его использование может привести к ошибкам при разрешении имен DNS.

> [!TIP]
> Если вы создаете личное доменное имя, важно не допускать конфликтов с существующими пространствами имен DNS. Рекомендуется использовать доменное имя отдельно от любого существующего пространства dNS или находного DNS-имен.
>
> Например, если у вас есть существующее пространство dNS-имен *contoso.com,* создайте управляемый домен Azure AD DS с пользовательским доменным именем *aaddscontoso.com.* Если вам нужно использовать безопасный LDAP, вы должны зарегистрироваться и владеть этим пользовательским доменным именем для создания необходимых сертификатов.
>
> Возможно, потребуется создать дополнительные dNS-записи для других служб в среде или условные дистрикторы DNS между существующими пространствами dNS-имен в вашей среде. Например, если корневое имя DNS используется в качестве адреса сайта, размещенного на веб-сервере, могут возникнуть конфликты имен, для устранения которых потребуются дополнительные записи DNS.
>
> В этих учебниках и статьях пользовательский домен *aaddscontoso.com* используется в качестве короткого примера. Во всех командах укажите собственное доменное имя.

Также применимы следующие ограничения в отношении DNS:

* **Ограничения префикса домена:** Нельзя создать управляемый домен с приставкой длиной более 15 символов. Приставка указанного доменного имени *(например, aaddscontoso* в *доменном aaddscontoso.com)* должна содержать 15 или меньше символов.
* **Конфликты названий сети:** Доменное имя DNS для управляемого домена уже не должно существовать в виртуальной сети. В частности, к конфликту имен приведут следующие сценарии:
    * Если в виртуальной сети уже существует домен Active Directory с таким же доменным именем DNS.
    * Если для виртуальной сети, в которой планируется использовать управляемый домен, установлено VPN-подключение к локальной сети. в этом случае необходимо убедиться в том, что в локальной сети нет домена с таким же DNS-именем домена;
    * Если в виртуальной сети Azure существует облачная служба Azure с таким же именем.

## <a name="create-required-azure-ad-resources"></a>Создание необходимых ресурсов Azure AD

Azure AD DS требует основного обслуживания и группы Azure AD. Эти ресурсы позволяют управляемому домену Azure AD синхронизировать данные и определять, какие пользователи имеют административные разрешения в управляемом домене.

Во-первых, зарегистрируйте поставщика ресурсов для доменов Azure AD, используя cmdlet [Register-AzResourceProvider:][Register-AzResourceProvider]

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD
```

Создайте принцип службы Azure AD, используя [cmdlet New-AzureADServicePrincipal][New-AzureADServicePrincipal] для Azure AD DS для связи и аутентификации. Используется специальный идентификатор приложения под названием *Domain Controller Services* с идентификатором *2565bd9d-da50-47d4-8b85-4c97f669dc36*. Не изменяйте идентификатор этого приложения.

```powershell
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
```

Теперь создайте группу Azure AD под названием *AAD DC Administrators* с помощью cmdlet [New-AzureADGroup.][New-AzureADGroup] Пользователям, добавленным в эту группу, выдаются разрешения на выполнение задач администрирования в домене Azure AD DS.

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

Наконец, создайте группу ресурсов с помощью cmdlet [New-AzResourceGroup.][New-AzResourceGroup] В следующем примере ресурсная группа называется *myResourceGroup* и создается в регионе *Westus.* Используйте свое имя и желаемую область:

```powershell
New-AzResourceGroup `
  -Name "myResourceGroup" `
  -Location "WestUS"
```

Если вы выбрали регион, который поддерживает зоны доступности, ресурсы Azure AD DS распределяются между зонами для дополнительной избыточности. Зоны доступности — уникальные физические расположения в пределах одного региона Azure. Каждая зона состоит из одного или нескольких центров обработки данных, оснащенных независимыми системами электроснабжения, охлаждения и сетевого взаимодействия. Чтобы обеспечить отказоустойчивость, во всех включенных регионах используются минимум три отдельные зоны.

Вы не можете настроить распределение Azure AD DS между зонами. Платформа Azure автоматически обрабатывает распределение ресурсов зоны. Для получения дополнительной информации и просмотра доступности региона [см.][availability-zones]

## <a name="resource-definition-for-azure-ad-ds"></a>Определение ресурсов для Azure AD DS

В рамках определения ресурса Resource Manager требуются следующие параметры конфигурации:

| Параметр               | Значение |
|-------------------------|---------|
| domainName              | Доменное имя DNS для управляемого домена с учетом предыдущих точек именования префиксов и конфликтов. |
| фильтрованныйСинхрониза            | Службы Azure AD DS позволяют синхронизировать *всех пользователей и все группы*, которые доступны в Azure AD, или только *определенные группы*. Если вы решите синхронизировать всех пользователей и все группы, вы не сможете позже перейти на ограниченную синхронизацию.<br /> См. сведения об [ограниченной синхронизации в области в доменных службах Azure AD][scoped-sync].|
| notificationSettings    | При наличии каких-либо оповещений, генерируемых в домене Azure AD DS, уведомления по электронной почте могут быть отправлены. <br />*Глобальные администраторы* арендатора Azure и члены *группы администраторов AAD DC* могут быть *включены* для этих уведомлений.<br /> При желании можно добавить дополнительные получатели для уведомлений, когда есть оповещения, требующие внимания.|
| domainConfigurationType | По умолчанию управляемый домен AD DS Azure создается как лес *пользователей*. Лес этого типа синхронизирует все объекты из Azure AD, включая учетные записи пользователей, созданные в локальной среде AD DS. Для создания леса пользователя не требуется указывать значение *domainConfiguration.*<br /> Лес *ресурсов* синхронизирует только пользователей и группы, созданные непосредственно в Azure AD. Леса ресурсов в настоящее время доступны в предварительной версии. Установите значение *РесурсДоверия* для создания ресурсного леса.<br />Дополнительные сведения о лесах *ресурсов*, в том числе об их предназначении и о создании доверия между лесами и локальными доменами AD DS, см. в [этой статье][resource-forests].|

Следующее определение сжатых параметров показывает, как декларируются эти значения. Лес пользователя под названием *aaddscontoso.com* создается со всеми пользователями из Azure AD, синхронизированными с управляемым dS-доменом Azure AD:

```json
"parameters": {
    "domainName": {
        "value": "aaddscontoso.com"
    },
    "filteredSync": {
        "value": "Disabled"
    },
    "notificationSettings": {
        "value": {
            "notifyGlobalAdmins": "Enabled",
            "notifyDcAdmins": "Enabled",
            "additionalRecipients": []
        }
    },
    [...]
}
```

Следующий тип шаблона шаблона сгущенного ресурса «Менеджер ресурсов» затем используется для определения и создания домена Azure AD DS, управляемого DS. Виртуальная сеть и подсеть Azure уже должны существовать или создаваться как часть шаблона «Менеджер ресурсов». Управляемый домен Azure AD DS подключен к этой подсети.

```json
"resources": [
    {
        "apiVersion": "2017-06-01",
        "type": "Microsoft.AAD/DomainServices",
        "name": "[parameters('domainName')]",
        "location": "[parameters('location')]",
        "dependsOn": [
            "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]"
        ],
        "properties": {
            "domainName": "[parameters('domainName')]",
            "subnetId": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', parameters('vnetName'), '/subnets/', parameters('subnetName'))]",
            "filteredSync": "[parameters('filteredSync')]",
            "notificationSettings": "[parameters('notificationSettings')]"
        }
    },
    [...]
]
```

Эти параметры и тип ресурса могут быть использованы как часть более широкого шаблона Resource Manager для развертывания управляемого домена, как показано в следующем разделе.

## <a name="create-a-managed-domain-using-sample-template"></a>Создание управляемого домена с помощью шаблона образца

Следующий полный шаблон образца ресурсного менеджера создает управляемый домен Azure AD DS и поддерживающие правила виртуальной сети, подсети и группы сетевой безопасности. Правила группы сетевой безопасности необходимы для обеспечения безопасности управляемого домена и обеспечения правильного движения трафика. Создается лес пользователя с именем DNS *aaddscontoso.com,* при этом все пользователи синхронизированы с Azure AD:

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "apiVersion": {
            "value": "2017-06-01"
        },
        "domainConfigurationType": {
            "value": "FullySynced"
        },
        "domainName": {
            "value": "aaddscontoso.com"
        },
        "filteredSync": {
            "value": "Disabled"
        },
        "location": {
            "value": "westus"
        },
        "notificationSettings": {
            "value": {
                "notifyGlobalAdmins": "Enabled",
                "notifyDcAdmins": "Enabled",
                "additionalRecipients": []
            }
        },
        "subnetName": {
            "value": "aadds-subnet"
        },
        "vnetName": {
            "value": "aadds-vnet"
        },
        "vnetAddressPrefixes": {
            "value": [
                "10.1.0.0/24"
            ]
        },
        "subnetAddressPrefix": {
            "value": "10.1.0.0/24"
        },
        "nsgName": {
            "value": "aadds-nsg"
        }
    },
    "resources": [
        {
            "apiVersion": "2017-06-01",
            "type": "Microsoft.AAD/DomainServices",
            "name": "[parameters('domainName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]"
            ],
            "properties": {
                "domainName": "[parameters('domainName')]",
                "subnetId": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', parameters('vnetName'), '/subnets/', parameters('subnetName'))]",
                "filteredSync": "[parameters('filteredSync')]",
                "domainConfigurationType": "[parameters('domainConfigurationType')]",
                "notificationSettings": "[parameters('notificationSettings')]"
            }
        },
        {
            "type": "Microsoft.Network/NetworkSecurityGroups",
            "name": "[parameters('nsgName')]",
            "location": "[parameters('location')]",
            "properties": {
                "securityRules": [
                    {
                        "name": "AllowSyncWithAzureAD",
                        "properties": {
                            "access": "Allow",
                            "priority": 101,
                            "direction": "Inbound",
                            "protocol": "Tcp",
                            "sourceAddressPrefix": "AzureActiveDirectoryDomainServices",
                            "sourcePortRange": "*",
                            "destinationAddressPrefix": "*",
                            "destinationPortRange": "443"
                        }
                    },
                    {
                        "name": "AllowPSRemoting",
                        "properties": {
                            "access": "Allow",
                            "priority": 301,
                            "direction": "Inbound",
                            "protocol": "Tcp",
                            "sourceAddressPrefix": "AzureActiveDirectoryDomainServices",
                            "sourcePortRange": "*",
                            "destinationAddressPrefix": "*",
                            "destinationPortRange": "5986"
                        }
                    },
                    {
                        "name": "AllowRD",
                        "properties": {
                            "access": "Allow",
                            "priority": 201,
                            "direction": "Inbound",
                            "protocol": "Tcp",
                            "sourceAddressPrefix": "CorpNetSaw",
                            "sourcePortRange": "*",
                            "destinationAddressPrefix": "*",
                            "destinationPortRange": "3389"
                        }
                    }
                ]
            },
            "apiVersion": "2018-04-01"
        },
        {
            "type": "Microsoft.Network/virtualNetworks",
            "name": "[parameters('vnetName')]",
            "location": "[parameters('location')]",
            "apiVersion": "2018-04-01",
            "dependsOn": [
                "[concat('Microsoft.Network/NetworkSecurityGroups/', parameters('nsgName'))]"
            ],
            "properties": {
                "addressSpace": {
                    "addressPrefixes": "[parameters('vnetAddressPrefixes')]"
                },
                "subnets": [
                    {
                        "name": "[parameters('subnetName')]",
                        "properties": {
                            "addressPrefix": "[parameters('subnetAddressPrefix')]",
                            "networkSecurityGroup": {
                                "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/NetworkSecurityGroups/', parameters('nsgName'))]"
                            }
                        }
                    }
                ]
            }
        }
    ],
    "outputs": {}
}
```

Этот шаблон можно использовать с помощью предпочтительного метода развертывания, например [портала Azure,][portal-deploy] [Azure PowerShell][powershell-deploy]или конвейера CI/CD. В следующем примере используется смдлет [New-AzResourceGroupDeployment.][New-AzResourceGroupDeployment] Укажите имя группы ресурсов и имя файла шаблона:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myResourceGroup" -TemplateFile <path-to-template>
```

Создание ресурса и возврат управления в запрос PowerShell занимает несколько минут. Управляемый домен Azure AD DS продолжает быть подготовлен в фоновом режиме и может занять до часа для завершения развертывания. На портале Azure страница **«Обзор»** для домена Azure AD DS показывает текущее состояние на протяжении всего этапа развертывания.

Когда портал Azure показывает, что управляемый домен Azure AD DS закончил подготовку, необходимо выполнить следующие задачи:

* Обновите параметры DNS для виртуальной сети, чтобы виртуальные машины могли найти управляемый домен для присоединения к нему или для аутентификации.
    * Чтобы настроить DNS, выберите на портале управляемый DS AD Azure AD. В окне **Обзора** вам предлагается автоматически настроить настройки DNS.
* [Позволяет синхронизировать пароли в службы домена Azure AD,](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) чтобы конечные пользователи могли войти в управляемый домен, используя свои корпоративные учетные данные.

## <a name="next-steps"></a>Дальнейшие действия

Чтобы увидеть в действии правительственный домен Azure AD DS, можно [объединить windows VM,][windows-join] [настроить безопасную синхронизацию LDAP][tutorial-ldaps]и [настроить синхронизацию хэш-пароля.][tutorial-phs]

<!-- INTERNAL LINKS -->
[windows-join]: join-windows-vm.md
[tutorial-ldaps]: tutorial-configure-ldaps.md
[tutorial-phs]: tutorial-configure-password-hash-sync.md
[availability-zones]: ../availability-zones/az-overview.md
[portal-deploy]: ../azure-resource-manager/templates/deploy-portal.md
[powershell-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[scoped-sync]: scoped-synchronization.md
[resource-forests]: concepts-resource-forest.md

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
[Get-AzSubscription]: /powershell/module/Az.Accounts/Get-AzSubscription
[cloud-shell]: /azure/cloud-shell/cloud-shell-windows-users
[naming-prefix]: /windows-server/identity/ad-ds/plan/selecting-the-forest-root-domain
[New-AzResourceGroupDeployment]: /powershell/module/Az.Resources/New-AzResourceGroupDeployment
