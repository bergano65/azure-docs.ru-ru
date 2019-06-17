---
title: Управляемого приложения Azure с помощью управляемого удостоверения
description: Сведения о настройке управляемого приложения с управляемым удостоверением. Управляемое удостоверение может использоваться для развертывания управляемых приложений, связанные с существующими ресурсами, предоставить управляемые приложения для управления ресурсами Azure за пределами управляемой группе ресурсов и удостоверения оперативной управляемых приложений для журнала действий и другие службы в Azure.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.reviewer: ''
ms.author: jobreen
author: jjbfour
ms.date: 05/13/2019
ms.openlocfilehash: 5ef653e825a5f1eb0f5df52f9c2544a5224b34cf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66003451"
---
# <a name="azure-managed-application-with-managed-identity"></a>Управляемого приложения Azure с помощью управляемого удостоверения

> [!NOTE]
> Поддержка управляемого удостоверения для управляемых приложений в настоящее время доступна Предварительная версия. Используйте api версии 2018-09-01-preview использовать управляемое удостоверение.

Сведения о настройке управляемого приложения должен содержать управляемое удостоверение. Управляемое удостоверение может использоваться для позволяют клиенту для предоставления доступа на дополнительные ресурсы для существующего управляемого приложения. Удостоверения управляются платформой Azure, и для них не нужно подготавливать или изменять секреты. Дополнительные сведения об управляемых удостоверениях в Azure Active Directory (AAD), см. в разделе [управляемые удостоверения для ресурсов Azure](../active-directory/managed-identities-azure-resources/overview.md).

Приложению можно предоставить два типа удостоверений:

- **Назначаемое системой удостоверение** привязывается к приложению и удаляется при удалении приложения. Приложение может иметь только одно назначаемое системой удостоверение.
- Объект **назначаемого пользователем удостоверения** — это отдельный ресурс Azure, могут быть назначены для приложения. Приложение может иметь несколько назначаемых пользователем удостоверений.

## <a name="how-to-use-managed-identity"></a>Как использовать управляемое удостоверение

Удостоверение управляемый поддерживает многие сценарии для управляемых приложений. Ниже приведены некоторые распространенные сценарии, которые могут быть устранены.

- Развертывание управляемого приложения связать существующие ресурсы Azure. Пример сценария — развертывание виртуальной машины Azure (ВМ) в пределах управляемого приложения, к которой подключен [существующий сетевой интерфейс](../virtual-network/virtual-network-network-interface-vm.md).
- Предоставив доступ к ресурсам Azure за пределами управляемого приложения и издатель **управляемой группы ресурсов**.
- Предоставление оперативной удостоверения управляемых приложений для журнала действий и других служб в Azure.

## <a name="adding-managed-identity"></a>Добавление управляемого удостоверения

Для создания управляемого приложения с управляемым удостоверением требуется дополнительное свойство для настройки ресурсов Azure. В следующем примере показан образец **удостоверений** свойство:

```json
{
"identity": {
    "type": "SystemAssigned, UserAssigned",
    "userAssignedIdentities": {
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.ManagedIdentity/userassignedidentites/myuserassignedidentity": {}
    }
}
```

Существует два распространенных способа для создания управляемого приложения с **удостоверений**: [CreateUIDefinition.json](./create-uidefinition-overview.md) и [шаблонов Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md). Простой единый создавать сценарии, CreateUIDefinition должна использоваться Включить управляемое удостоверение, так как он предоставляет более широкие возможности. Тем не менее при работе с расширенной или сложных систем, требующих автоматической или нескольких развертываний управляемого приложения, шаблоны можно использовать.

### <a name="using-createuidefinition"></a>С помощью CreateUIDefinition

Управляемое приложение можно настроить с помощью управляемого удостоверения через [CreateUIDefinition.json](./create-uidefinition-overview.md). В [выводит раздел](./create-uidefinition-overview.md#outputs), ключ `managedIdentity` может использоваться для переопределения свойство идентификатора шаблона управляемого приложения. Пример ниже позволит **назначенный системой** удостоверения на управляемого приложения. Более сложные объекты удостоверений может быть создана с помощью элементов CreateUIDefinition попросить потребитель для входных данных. Эти входные данные могут использоваться для создания управляемых приложений с помощью **назначаемого пользователем удостоверения**.

```json
"outputs": {
    "managedIdentity": "[parse('{\"Type\":\"SystemAssigned\"}')]"
}
```

#### <a name="when-to-use-createuidefinition-for-managed-identity"></a>Когда следует использовать CreateUIDefinition для управляемого удостоверения

Ниже приведены некоторые рекомендации по использованию CreateUIDefinition для включения управляемого удостоверения на управляемые приложения.

- Создание управляемого приложения проходит через портал Azure или marketplace.
- Управляемое удостоверение требующее сложных потребителей.
- При создании управляемого приложения требуется управляемое удостоверение.

#### <a name="systemassigned-createuidefinition"></a>CreateUIDefinition для него значение SystemAssigned

Основные определения CreateUIDefinition, позволяющий него значение SystemAssigned удостоверение для управляемого приложения.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {}
        ],
        "steps": [
        ],
        "outputs": {
            "managedIdentity": "[parse('{\"Type\":\"SystemAssigned\"}')]"
        }
    }
}
```

#### <a name="userassigned-createuidefinition"></a>UserAssigned CreateUIDefinition

Основные определения CreateUIDefinition, принимающий **назначаемого пользователем удостоверения** ресурсов на вход, а также включает идентификатор UserAssigned для управляемого приложения.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {}
        ],
        "steps": [
            {
                "name": "manageIdentity",
                "label": "Identity",
                "subLabel": {
                    "preValidation": "Manage Identities",
                    "postValidation": "Done"
                },
                "bladeTitle": "Identity",
                "elements": [
                    {
                        "name": "userAssignedText",
                        "type": "Microsoft.Common.TextBox",
                        "label": "User assigned managed identity",
                        "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.ManagedIdentity/userassignedidentites/myuserassignedidentity",
                        "visible": true
                    }
                ]
            }
        ],
        "outputs": {
            "managedIdentity": "[parse(concat('{\"Type\":\"UserAssigned\",\"UserAssignedIdentities\":{',string(steps('manageIdentity').userAssignedText),':{}}}'))]"
        }
    }
}
```

CreateUIDefinition.json выше создает создать взаимодействие с пользователем, имеется текстовое поле для объекта-получателя для ввода **назначаемого пользователем удостоверения** идентификатор ресурса Azure. Созданный интерфейс выглядит следующим образом:

![Пример назначаемого пользователем удостоверения CreateUIDefinition](./media/publish-managed-identity/user-assigned-identity.png)

### <a name="using-azure-resource-manager-templates"></a>Использование шаблонов диспетчера ресурсов Azure

> [!NOTE]
> Шаблоны управляемого приложения Marketplace создаются автоматически для клиентов, перейдя на портале Azure создайте качества.
> В таких случаях `managedIdentity` ключ выходные данные на CreateUIDefinition должен использоваться Включить удостоверение.

Управляемое удостоверение можно также включить с помощью шаблонов Azure Resource Manager. Пример ниже позволит **назначенный системой** удостоверения на управляемого приложения. Более сложные объекты удостоверений может быть создана с помощью параметров шаблона Azure Resource Manager для входных данных. Эти входные данные могут использоваться для создания управляемых приложений с помощью **назначаемого пользователем удостоверения**.

#### <a name="when-to-use-azure-resource-manager-templates-for-managed-identity"></a>Когда следует использовать шаблоны Azure Resource Manager для управляемого удостоверения

Ниже приведены некоторые рекомендации по использованию шаблонов Azure Resource Manager для включения управляемого удостоверения на управляемые приложения.

- Управляемые приложения могут развертываться программным способом на основе шаблона.
- Пользовательские назначения ролей для управляемого удостоверения необходимые для подготовки управляемого приложения.
- Управляемые приложения не обязательно Azure процесса создания портала и marketplace.

#### <a name="systemassigned-template"></a>Шаблон для него значение SystemAssigned

Базовый шаблон Azure Resource Manager, который развертывает приложения на управляемых с помощью **назначенный системой** удостоверений.

```json
"resources": [
    {
        "type": "Microsoft.Solutions/applications",
        "name": "[parameters('applicationName')]",
        "apiVersion": "2018-09-01-preview",
        "location": "[parameters('location')]",
        "identity": {
            "type": "SystemAssigned"
        },
        "properties": {
            "ManagedResourceGroupId": "[parameters('managedByResourceGroupId')]",
            "parameters": { }
        }
    }
]
```

### <a name="userassigned-template"></a>Шаблон UserAssigned

Базовый шаблон Azure Resource Manager, который развертывает приложения на управляемых с помощью **назначаемого пользователем удостоверения**.

```json
"resources": [
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "name": "[parameters('managedIdentityName')]",
      "apiVersion": "2018-11-30",
      "location": "[parameters('location')]"
    },
    {
        "type": "Microsoft.Solutions/applications",
        "name": "[parameters('applicationName')]",
        "apiVersion": "2018-09-01-preview",
        "location": "[parameters('location')]",
        "identity": {
            "type": "UserAssigned",
            "userAssignedIdentities": {
                "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',parameters('managedIdentityName'))]": {}
            }
        },
        "properties": {
            "ManagedResourceGroupId": "[parameters('managedByResourceGroupId')]",
            "parameters": { }
        }
    }
]
```

## <a name="granting-access-to-azure-resources"></a>Предоставление доступа к ресурсам Azure

После предоставления удостоверения управляемого приложения его можно предоставить доступ к существующим ресурсам azure. Этот процесс может осуществляться через интерфейс управления (IAM) доступом на портале Azure. Имя управляемого приложения или **назначаемого пользователем удостоверения** осуществляется добавление назначения роли.

![Добавить назначение роли для управляемого приложения](./media/publish-managed-identity/identity-role-assignment.png)

## <a name="linking-existing-azure-resources"></a>Связывание существующие ресурсы Azure

> [!NOTE]
> Объект **назначаемого пользователем удостоверения** должно быть [настроен](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) перед развертыванием управляемого приложения. Кроме того, связанный ресурс развертывания управляемых приложений поддерживается только для **marketplace** вид.

Управляемое удостоверение также может использоваться для развертывания управляемого приложения, которому требуется доступ к существующим ресурсам во время его развертывания. При подготовке клиента, управляемого приложения **назначенных пользователям удостоверений** могут добавляться для предоставления дополнительных разрешений для **mainTemplate** развертывания.

### <a name="authoring-the-createuidefinition-with-a-linked-resource"></a>Разработка CreateUIDefinition с связанного ресурса

При связывании развертывания управляемого приложения с существующими ресурсами, как существующий ресурс Azure и **назначаемого пользователем удостоверения** с применимой роли необходимо указать назначение для этого ресурса.

 Пример CreateUIDefinition, которое требует наличия двух входных значений: идентификатор ресурса сетевого интерфейса и идентификатор ресурса назначаемого пользователем удостоверения.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
    "handler": "Microsoft.Compute.MultiVm",
    "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {}
        ],
        "steps": [
            {
                "name": "managedApplicationSetting",
                "label": "Managed Application Settings",
                "subLabel": {
                    "preValidation": "Managed Application Settings",
                    "postValidation": "Done"
                },
                "bladeTitle": "Managed Application Settings",
                "elements": [
                    {
                        "name": "networkInterfaceId",
                        "type": "Microsoft.Common.TextBox",
                        "label": "network interface resource id",
                        "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Network/networkInterfaces/existingnetworkinterface",
                        "toolTip": "Must represent the identity as an Azure Resource Manager resource identifer format ex. /subscriptions/sub1/resourcegroups/myGroup/providers/Microsoft.Network/networkInterfaces/networkinterface1",
                        "visible": true
                    },
                    {
                        "name": "userAssignedId",
                        "type": "Microsoft.Common.TextBox",
                        "label": "user assigned identity resource id",
                        "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.ManagedIdentity/userassignedidentites/myuserassignedidentity",
                        "toolTip": "Must represent the identity as an Azure Resource Manager resource identifer format ex. /subscriptions/sub1/resourcegroups/myGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/identity1",
                        "visible": true
                    }
                ]
            }
        ],
        "outputs": {
            "existingNetworkInterfaceId": "[steps('managedApplicationSetting').networkInterfaceId]",
            "managedIdentity": "[parse(concat('{\"Type\":\"UserAssigned\",\"UserAssignedIdentities\":{',string(steps('managedApplicationSetting').userAssignedId),':{}}}'))]"
        }
    }
}
```

Этот CreateUIDefinition.json приводит к возникновению ошибки создать взаимодействие с пользователем, имеющий два поля. Первое поле позволяет пользователю вводить в идентификатор ресурса Azure для ресурса, связанным с развертывания управляемого приложения. Второй — для потребителей ввести **назначаемого пользователем удостоверения** идентификатор ресурса Azure, который имеет доступ к связанному ресурсу Azure. Созданный интерфейс выглядит следующим образом:

![Пример CreateUIDefinition с двумя входными данными: сетевой интерфейс, идентификатор ресурса и идентификатор ресурса назначаемого пользователем удостоверения](./media/publish-managed-identity/network-interface-cuid.png)

### <a name="authoring-the-maintemplate-with-a-linked-resource"></a>Разработка mainTemplate с связанного ресурса

Помимо обновления CreateUIDefinition, основного шаблона также должен быть обновлен для приема переданного идентификатор связанного ресурса. Основной шаблон можно обновить принимать новые выходные данные путем добавления нового параметра. Так как `managedIdentity` выходных данных переопределяет значения созданный шаблон управляемого приложения, он не передается в основной шаблон и не должен включаться в разделе "Параметры".

Основной пример шаблона, который задает сетевого профиля для существующего сетевого интерфейса, предоставляемые CreateUIDefinition.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "existingNetworkInterfaceId": { "type": "string" }
    },
    "variables": {
    },
    "resources": [
        {
            "apiVersion": "2016-04-30-preview",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "myLinkedResourceVM",
            "location": "[resourceGroup().location]",
            "properties": {
                …,
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[parameters('existingNetworkInterfaceId')]"
                        }
                    ]
                }
            }
        }
    ]
}
```

### <a name="consuming-the-managed-application-with-a-linked-resource"></a>Использование управляемого приложения с помощью связанного ресурса

После создания пакета управляемого приложения, управляемого приложения могут быть использованы через портал Azure. Прежде чем его можно будет использовать, существуют несколько необходимых шагов.

- Необходимо создать экземпляр требуется связанного ресурса Azure.
- **Назначаемого пользователем удостоверения** должно быть [присваивается назначения ролей](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) связанный ресурс.
- Существующие связанные идентификатор ресурса и **назначаемого пользователем удостоверения** идентификатору CreateUIDefinition.

## <a name="accessing-the-managed-identity-token"></a>Доступ к токен управляемого удостоверения

Токен управляемого приложения теперь может осуществляться через `listTokens` api в клиенте издателя. Пример запроса может выглядеть так:

``` HTTP
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Solutions/applications/{applicationName}?api-version=2018-09-01-preview HTTP/1.1
```

Пример ответа может выглядеть так:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json

{
    "value": [
        {
            "access_token": "eyJ0eXAi…",
            "expires_in": "2…",
            "expires_on": "1557…",
            "not_before": "1557…",
            "authorizationAudience": "https://management.azure.com/",
            "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Solutions/applications/{applicationName}",
            "token_type": "Bearer"
        }
    ]
}
```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Настройка управляемого приложения с помощью настраиваемого поставщика](./custom-providers-overview.md)