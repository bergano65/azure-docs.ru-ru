---
title: Добавление ролевых заданий с шаблонами RBAC и Azure Resource Manager
description: Узнайте, как предоставить доступ к ресурсам Azure для пользователей, групп, основ служб или управляемых идентификаторов с помощью шаблонов управления доступом на основе ролей Azure (RBAC) и Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 9f817880f938f5d03024e3aacd9b84817a5ac721
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77138299"
---
# <a name="add-role-assignments-using-azure-rbac-and-azure-resource-manager-templates"></a>Добавление ролевых заданий с помощью шаблонов Azure RBAC и менеджера ресурсов Azure

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)]Помимо использования Azure PowerShell или ClI Azure, можно назначить роли с помощью [шаблонов управления ресурсами Azure.](../azure-resource-manager/templates/template-syntax.md) Шаблоны могут быть полезны для согласованного и многократного развертывания ресурсов. В этой статье описывается, как назначить роли с помощью шаблонов.

## <a name="get-object-ids"></a>Получить идоты объектов

Чтобы назначить роль, необходимо указать идентификатор пользователя, группу или приложение, которое вы хотите назначить. Идентификатор `11111111-1111-1111-1111-111111111111`имеет формат: . Идентификатор можно получить с помощью портала Azure, Azure PowerShell или Azure CLI.

### <a name="user"></a>Пользователь

Чтобы получить идентификатор пользователя, вы можете использовать команды [Get-AzADUser](/powershell/module/az.resources/get-azaduser) или [az ad-пользователя.](/cli/azure/ad/user#az-ad-user-show)

```azurepowershell
$objectid = (Get-AzADUser -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad user show --id "{email}" --query objectId --output tsv)
```

### <a name="group"></a>Группа

Чтобы получить идентификатор группы, вы можете использовать команды [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup) или [группы ad.](/cli/azure/ad/group#az-ad-group-show)

```azurepowershell
$objectid = (Get-AzADGroup -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad group show --group "{name}" --query objectId --output tsv)
```

### <a name="application"></a>Приложение

Чтобы получить идентификатор основного обслуживания (идентификация, используемая приложением), можно использовать команды [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) или [az ad sp list.](/cli/azure/ad/sp#az-ad-sp-list) Для основного обслуживания используйте идентификатор объекта, а **не** идентификатор приложения.

```azurepowershell
$objectid = (Get-AzADServicePrincipal -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad sp list --display-name "{name}" --query [].objectId --output tsv)
```

## <a name="add-a-role-assignment"></a>Добавление назначения роли

В RBAC, чтобы предоставить доступ, вы добавляете назначение ролей.

### <a name="resource-group-without-parameters"></a>Группа ресурсов (без параметров)

Следующий шаблон показывает основной способ добавления назначения ролей. Некоторые значения указаны в шаблоне. Приведенный ниже шаблон демонстрирует:

-  Как назначить роль [Читателя](built-in-roles.md#reader) пользователю, группе или приложению в области группы ресурсов

Чтобы использовать шаблон, необходимо сделать следующее:

- Создайте новый файл JSON и скопируйте шаблон
- Заменить `<your-principal-id>` идентификатор пользователя, группу или приложение для присвоения роли

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[guid(resourceGroup().id)]",
            "properties": {
                "roleDefinitionId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]",
                "principalId": "<your-principal-id>"
            }
        }
    ]
}
```

Вот [пример, New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) и [развертывание группы аз создают](/cli/azure/group/deployment#az-group-deployment-create) команды для начала развертывания в группе ресурсов под названием ExampleGroup.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file rbac-test.json
```

Ниже приводится пример назначения роли Reader пользователю для группы ресурсов после развертывания шаблона.

![Назначение ролей в области группы ресурсов](./media/role-assignments-template/role-assignment-template.png)

### <a name="resource-group-or-subscription"></a>Группа ресурсов или подписка

Предыдущий шаблон не очень гибок. Следующий шаблон использует параметры и может использоваться в различных областях. Приведенный ниже шаблон демонстрирует:

- Как назначить роль пользователю, группе или приложению в группе ресурсов или области подписки
- как задать роли владельца, участника или читателя в качестве параметра.

Чтобы использовать шаблон, необходимо указать следующие входные данные:

- Идентификатор пользователя, группы или приложения для присвоения роли
- Уникальный идентификатор, который будет использоваться для назначения ролей, или вы можете использовать идентификатор по умолчанию

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "The principal to assign the role to"
            }
        },
        "builtInRoleType": {
            "type": "string",
            "allowedValues": [
                "Owner",
                "Contributor",
                "Reader"
            ],
            "metadata": {
                "description": "Built-in role to assign"
            }
        },
        "roleNameGuid": {
            "type": "string",
            "defaultValue": "[newGuid()]",
            "metadata": {
                "description": "A new GUID used to identify the role assignment"
            }
        }
    },
    "variables": {
        "Owner": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')]",
        "Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
        "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[parameters('roleNameGuid')]",
            "properties": {
                "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

> [!NOTE]
> Этот шаблон не является идемпотентным, если одно и то же `roleNameGuid` значение не предусмотрено в качестве параметра для каждого развертывания шаблона. Если `roleNameGuid` нет, по умолчанию новый GUID генерируется на каждом развертывании и последующие развертывания сбой с ошибкой. `Conflict: RoleAssignmentExists`

Сфера назначения ролей определяется с уровня развертывания. Вот пример, [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) и [развертывание группы аз создают](/cli/azure/group/deployment#az-group-deployment-create) команды для начала развертывания в области группы ресурсов.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Reader
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Reader
```

Вот [пример: New-AzDeployment](/powershell/module/az.resources/new-azdeployment) и [развертывание az создают](/cli/azure/deployment#az-deployment-create) команды для начала развертывания в области подписки и указано местоположение.

```azurepowershell
New-AzDeployment -Location centralus -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Reader
```

```azurecli
az deployment create --location centralus --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Reader
```

### <a name="resource"></a>Ресурс

Если вам нужно добавить назначение ролей на уровне ресурса, формат назначения роли отличается. Вы предоставляете пространство имен поставщика ресурсов и тип ресурса ресурса для присвоения роли. Вы также включаете имя ресурса в имя назначения роли.

Для типа и имени назначения роли используйте следующий формат:

```json
"type": "{resource-provider-namespace}/{resource-type}/providers/roleAssignments",
"name": "{resource-name}/Microsoft.Authorization/{role-assign-GUID}"
```

Приведенный ниже шаблон демонстрирует:

- Как создать учетную запись хранения
- Как назначить роль пользователю, группе или приложению в области учетной записи хранилища
- как задать роли владельца, участника или читателя в качестве параметра.

Чтобы использовать шаблон, необходимо указать следующие входные данные:

- Идентификатор пользователя, группы или приложения для присвоения роли

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "The principal to assign the role to"
            }
        },
        "builtInRoleType": {
            "type": "string",
            "allowedValues": [
                "Owner",
                "Contributor",
                "Reader"
            ],
            "metadata": {
                "description": "Built-in role to assign"
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {
        "Owner": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')]",
        "Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
        "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]",
        "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageName')]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        },
        {
            "type": "Microsoft.Storage/storageAccounts/providers/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[concat(variables('storageName'), '/Microsoft.Authorization/', guid(uniqueString(variables('storageName'))))]",
            "dependsOn": [
                "[variables('storageName')]"
            ],
            "properties": {
                "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

Для развертывания предыдущего шаблона используются команды группы ресурсов. Вот [пример, New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) и [развертывание группы аз создают](/cli/azure/group/deployment#az-group-deployment-create) команды для запуска развертывания в области ресурсов.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Contributor
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Contributor
```

Ниже приводится пример назначения роли contributor пользователю для учетной записи хранилища после развертывания шаблона.

![Назначение ролей в области ресурсов](./media/role-assignments-template/role-assignment-template-resource.png)

### <a name="new-service-principal"></a>Новый директор службы

Если вы создаете новый директор службы и сразу же попытаетесь назначить роль директору службы, в некоторых случаях это назначение роли может выйти из строя. Например, если вы создаете новое управляемое удостоверение и затем пытаетесь назначить роль директору службы в том же шаблоне управления ресурсами Azure, назначение роли может выйти из строя. Причиной сбоя, скорее всего, является задержка репликации. Директор службы создается в одном регионе; однако назначение ролей может произойти в другом регионе, который еще не реплицировал основной службы. Чтобы устранить этот сценарий, необходимо настроить свойство `principalType` `ServicePrincipal` при создании назначения ролей.

Приведенный ниже шаблон демонстрирует:

- Как создать новый директор службы управляемого удостоверения данных
- Как указать`principalType`
- Как назначить роль вкладчика руководителю службы в области ресурсной группы

Чтобы использовать шаблон, необходимо указать следующие входные данные:

- Базовое имя управляемого иждивества или можно использовать строку по умолчанию

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "baseName": {
            "type": "string",
            "defaultValue": "msi-test"
        }
    },
    "variables": {
        "identityName": "[concat(parameters('baseName'), '-bootstrap')]",
        "bootstrapRoleAssignmentId": "[guid(concat(resourceGroup().id, 'contributor'))]",
        "contributorRoleDefinitionId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]"
    },
    "resources": [
        {
            "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
            "name": "[variables('identityName')]",
            "apiVersion": "2018-11-30",
            "location": "[resourceGroup().location]"
        },
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[variables('bootstrapRoleAssignmentId')]",
            "dependsOn": [
                "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]"
            ],
            "properties": {
                "roleDefinitionId": "[variables('contributorRoleDefinitionId')]",
                "principalId": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName')), '2018-11-30').principalId]",
                "scope": "[resourceGroup().id]",
                "principalType": "ServicePrincipal"
            }
        }
    ]
}
```

Вот пример, [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) и [развертывание группы аз создают](/cli/azure/group/deployment#az-group-deployment-create) команды для начала развертывания в области группы ресурсов.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup2 -TemplateFile rbac-test.json
```

```azurecli
az group deployment create --resource-group ExampleGroup2 --template-file rbac-test.json
```

Ниже приводится пример назначения ролей contributor новому руководителю службы управляемой идентификации после развертывания шаблона.

![Назначение ролей для нового директора службы управляемого удостоверения](./media/role-assignments-template/role-assignment-template-msi.png)

## <a name="next-steps"></a>Дальнейшие действия

- [Краткое руководство по созданию и развертыванию шаблонов Azure Resource Manager с помощью портала Azure](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)
- [Понимание структуры и синтаксиса шаблонов менеджера ресурсов Azure](../azure-resource-manager/templates/template-syntax.md)
- [Создание групп ресурсов и ресурсов на уровне подписки](../azure-resource-manager/templates/deploy-to-subscription.md)
- [Шаблоны Azure кикстарта](https://azure.microsoft.com/resources/templates/?term=rbac)
