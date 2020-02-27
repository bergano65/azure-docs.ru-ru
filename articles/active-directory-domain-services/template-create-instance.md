---
title: Включение доменных служб Azure DS с помощью шаблона | Документация Майкрософт
description: Узнайте, как настроить и включить Azure Active Directory доменных служб с помощью шаблона Azure Resource Manager
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
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/26/2020
ms.locfileid: "77612793"
---
# <a name="create-an-azure-active-directory-domain-services-managed-domain-using-an-azure-resource-manager-template"></a>Создание Azure Active Directory управляемого домена доменных служб с помощью шаблона Azure Resource Manager

Доменные службы Azure Active Directory (Azure AD DS) предоставляют управляемые доменные службы, отвечающие за присоединение к домену, применение групповой политики, использование протокола LDAP, а также выполнение аутентификации Kerberos или NTLM (полностью поддерживается Windows Server Active Directory). Эти доменные службы можно использовать без необходимости развертывать, администрировать и обновлять контроллеры домена. Azure AD DS интегрируется с существующим клиентом Azure AD. Такая интеграция позволяет пользователям входить в систему с корпоративными учетными данными. При этом вы можете использовать существующие группы и учетные записи пользователей для защиты доступа к ресурсам.

В этой статье показано, как включить AD DS Azure с помощью шаблона Azure Resource Manager. Вспомогательные ресурсы создаются с помощью Azure PowerShell.

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

## <a name="dns-naming-requirements"></a>Требования к именованию DNS

При создании экземпляра Azure AD DS указывается имя DNS. При выборе имени DNS следует учитывать ряд рекомендаций.

* **Встроенное доменное имя:** По умолчанию используется встроенное доменное имя каталога (суффикс *onmicrosoft.com* ). Если вы хотите разрешить для управляемого домена доступ по защищенному протоколу LDAP, вы не сможете создать цифровой сертификат для защиты подключения к домену по умолчанию. Домен *onmicrosoft.com* принадлежит корпорации Майкрософт, поэтому центр сертификации не будет выдавать для него сертификаты.
* **Пользовательские доменные имена:** Наиболее распространенный подход заключается в том, чтобы указать имя личного домена, которое, как правило, уже владеет и поддерживает маршрутизацию. При использовании личного домена с поддержкой маршрутизации трафик можно передавать так, нужно приложениям.
* **Суффиксы домена, не поддерживающие маршрутизацию:** Обычно рекомендуется избегать суффикса доменного имени, не поддерживающего маршрутизацию, например *contoso. local*. Суффикс *.local* не маршрутизируется и его использование может привести к ошибкам при разрешении имен DNS.

> [!TIP]
> Если вы создаете личное доменное имя, важно не допускать конфликтов с существующими пространствами имен DNS. Рекомендуется использовать доменное имя отдельно от любого существующего пространства имен DNS или локального домена.
>
> Например, если у вас есть пространство имен DNS *contoso.com*, создайте управляемый домен Azure AD DS с именем пользовательского домена *aaddscontoso.com*. Если необходимо использовать защищенный протокол LDAP, необходимо зарегистрировать и присвоить это имя личного домена, чтобы создать необходимые сертификаты.
>
> Возможно, потребуется создать дополнительные записи DNS для других служб в среде или условные серверы пересылки DNS между существующими пространствами имен DNS в вашей среде. Например, если корневое имя DNS используется в качестве адреса сайта, размещенного на веб-сервере, могут возникнуть конфликты имен, для устранения которых потребуются дополнительные записи DNS.
>
> В этих руководствах и статьях с инструкциями в кратком примере используется личный домен *aaddscontoso.com* . Во всех командах укажите собственное доменное имя.

Также применимы следующие ограничения в отношении DNS:

* **Ограничения префикса домена:** Нельзя создать управляемый домен с префиксом длиннее 15 символов. Префикс указанного доменного имени (например, *ааддсконтосо* в имени домена *aaddscontoso.com* ) должен содержать не менее 15 символов.
* **Конфликты сетевых имен:** Доменное имя DNS для управляемого домена не должно уже существовать в виртуальной сети. В частности, к конфликту имен приведут следующие сценарии:
    * Если в виртуальной сети уже существует домен Active Directory с таким же доменным именем DNS.
    * Если для виртуальной сети, в которой планируется использовать управляемый домен, установлено VPN-подключение к локальной сети. в этом случае необходимо убедиться в том, что в локальной сети нет домена с таким же DNS-именем домена;
    * Если в виртуальной сети Azure существует облачная служба Azure с таким же именем.

## <a name="create-required-azure-ad-resources"></a>Создание необходимых ресурсов Azure AD

Для AD DS Azure требуется субъект-служба и группа Azure AD. Эти ресурсы позволяют управляемому домену Azure AD DS синхронизировать данные и определять, какие пользователи имеют административные разрешения в управляемом домене.

Сначала зарегистрируйте поставщик ресурсов доменных служб Azure AD с помощью командлета [Register-азресаурцепровидер][Register-AzResourceProvider] :

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD
```

Создайте субъект-службу Azure AD с помощью командлета [AzureADServicePrincipal][New-AzureADServicePrincipal] для Azure AD DS для взаимодействия и проверки подлинности. Идентификатор конкретного приложения используется с именем *службы контроллера домена* и идентификатором *2565bd9d-da50-47d4-8b85-4c97f669dc36*. Не изменяйте этот идентификатор приложения.

```powershell
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
```

Теперь создайте группу Azure AD с именем " *Администраторы контроллера домена AAD* " с помощью командлета [New-AzureADGroup][New-AzureADGroup] . Пользователи, добавленные в эту группу, получают разрешения на выполнение задач администрирования в управляемом домене Azure AD DS.

```powershell
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"
```

Создав группу *администраторов контроллера домена AAD* , добавьте пользователя в группу с помощью командлета [Add-AzureADGroupMember][Add-AzureADGroupMember] . Сначала необходимо получить идентификатор объекта группы *администраторов контроллера домена AAD* с помощью командлета [Get-AzureADGroup][Get-AzureADGroup] , а затем идентификатор объекта нужного пользователя с помощью командлета [Get-AzureADUser][Get-AzureADUser] .

В следующем примере это идентификатор объекта пользователя для учетной записи с именем участника `admin@aaddscontoso.onmicrosoft.com`. Замените эту учетную запись пользователя именем участника-пользователя, которого вы хотите добавить в группу *администраторов контроллера домена AAD* :

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

Наконец, создайте группу ресурсов с помощью командлета [New-азресаурцеграуп][New-AzResourceGroup] . В следующем примере группа ресурсов называется *myResourceGroup* и создается в *westus* регионе. Используйте собственное имя и желаемый регион:

```powershell
New-AzResourceGroup `
  -Name "myResourceGroup" `
  -Location "WestUS"
```

Если вы выбрали регион, который поддерживает зоны доступности, ресурсы Azure AD DS распределяются между зонами для дополнительной избыточности. Зоны доступности — уникальные физические расположения в пределах одного региона Azure. Каждая зона состоит из одного или нескольких центров обработки данных, оснащенных независимыми системами электроснабжения, охлаждения и сетевого взаимодействия. Чтобы обеспечить отказоустойчивость, во всех включенных регионах используются минимум три отдельные зоны.

Вы не можете настроить распределение Azure AD DS между зонами. Платформа Azure автоматически обрабатывает распределение ресурсов зоны. Дополнительные сведения о доступности регионов см. в статье [что такое зоны доступности в Azure?][availability-zones].

## <a name="resource-definition-for-azure-ad-ds"></a>Определение ресурса для Azure AD DS

В рамках определения ресурса диспетчер ресурсов требуются следующие параметры конфигурации:

| Параметр               | Значение |
|-------------------------|---------|
| domainName              | Доменное имя DNS для управляемого домена, принимая во внимание предыдущие моменты при именовании префиксов и конфликтов. |
| филтередсинк            | Службы Azure AD DS позволяют синхронизировать *всех пользователей и все группы*, которые доступны в Azure AD, или только *определенные группы*. Если вы решите синхронизировать всех пользователей и группы, позднее вы не сможете выполнить синхронизацию с заданной областью.<br /> См. сведения об [ограниченной синхронизации в области в доменных службах Azure AD][scoped-sync].|
| notificationSettings    | При наличии предупреждений, созданных в управляемом домене Azure AD DS, можно отправлять уведомления по электронной почте. <br />Для этих уведомлений можно *включить* *Глобальные администраторы* клиента Azure и членов группы *администраторов контроллера домена AAD* .<br /> При необходимости можно добавить дополнительных получателей уведомлений при наличии предупреждений, требующих внимания.|
| домаинконфигуратионтипе | По умолчанию управляемый домен AD DS Azure создается как лес *пользователей*. Лес этого типа синхронизирует все объекты из Azure AD, включая учетные записи пользователей, созданные в локальной среде AD DS. Для создания пользовательского леса не нужно указывать значение *domainConfiguration* .<br /> Лес *ресурсов* синхронизирует только пользователей и группы, созданные непосредственно в Azure AD. Леса ресурсов в настоящее время доступны в предварительной версии. Задайте значение *ресаурцетрустинг* , чтобы создать лес ресурсов.<br />Дополнительные сведения о лесах *ресурсов*, в том числе об их предназначении и о создании доверия между лесами и локальными доменами AD DS, см. в [этой статье][resource-forests].|

В приведенном ниже определении уплотненных параметров показано, как объявляются эти значения. Будет создан лес пользователей с именем *aaddscontoso.com* , в котором все пользователи из Azure AD синхронизированы с управляемым доменом Azure AD DS.

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

Затем для определения и создания управляемого домена AD DS Azure используется следующий сжатый диспетчер ресурсов тип ресурса шаблона. Виртуальная сеть и подсеть Azure должны уже существовать или быть созданы как часть шаблона диспетчер ресурсов. Управляемый домен AD DS Azure подключен к этой подсети.

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

Эти параметры и тип ресурса можно использовать в составе более широкого диспетчер ресурсов шаблона для развертывания управляемого домена, как показано в следующем разделе.

## <a name="create-a-managed-domain-using-sample-template"></a>Создание управляемого домена с помощью примера шаблона

Следующий полный пример шаблона диспетчер ресурсов создает управляемый домен AD DS Azure и правила группы безопасности для виртуальной сети, подсети и сети. Правила группы безопасности сети необходимы для защиты управляемого домена и обеспечения правильной передачи трафика. Будет создан лес пользователей с DNS-именем *aaddscontoso.com* , в котором все пользователи синхронизированы из Azure AD:

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

Этот шаблон можно развернуть с помощью предпочтительного метода развертывания, например [портал Azure][portal-deploy], [Azure PowerShell][powershell-deploy]или конвейера CI/CD. В следующем примере используется командлет [New-азресаурцеграупдеплоймент][New-AzResourceGroupDeployment] . Укажите собственное имя группы ресурсов и файл шаблона:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myResourceGroup" -TemplateFile <path-to-template>
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
