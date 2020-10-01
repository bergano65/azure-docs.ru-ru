---
title: Добавление назначений ролей Azure с помощью шаблона Azure Resource Manager — Azure RBAC
description: Узнайте, как предоставляется доступ к ресурсам Azure пользователям, группам, субъектам-службам или управляемым удостоверениям с помощью шаблона Azure Resource Manager и управления доступом на основе ролей Azure (Azure RBAC).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 400f0b1b55136f133c9ad01fd0ba4b5dbc5e6bcb
ms.sourcegitcommit: 06ba80dae4f4be9fdf86eb02b7bc71927d5671d3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/01/2020
ms.locfileid: "91612750"
---
# <a name="add-azure-role-assignments-using-azure-resource-manager-templates"></a>Добавление назначений ролей Azure с помощью шаблона Azure Resource Manager

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)] В дополнение к использованию Azure PowerShell или Azure CLI вы можете назначать роли с помощью [шаблонов Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md). Шаблоны могут быть полезны для согласованного и многократного развертывания ресурсов. В этой статье описывается порядок назначения ролей с помощью шаблонов.

## <a name="get-object-ids"></a>Получение идентификаторов объектов

Чтобы назначить роль, необходимо указать идентификатор пользователя, группы или приложения для назначения роли. Идентификатор имеет следующий формат: `11111111-1111-1111-1111-111111111111`. Этот идентификатор можно получить с помощью портала Azure, Azure PowerShell или Azure CLI.

### <a name="user"></a>Пользователь

Чтобы получить идентификатор пользователя, можно использовать команды [Get-AzADUser](/powershell/module/az.resources/get-azaduser) или [az ad user show](/cli/azure/ad/user#az-ad-user-show).

```azurepowershell
$objectid = (Get-AzADUser -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad user show --id "{email}" --query objectId --output tsv)
```

### <a name="group"></a>Группа

Чтобы получить идентификатор группы, можно использовать команды [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup) или [az ad group show](/cli/azure/ad/group#az-ad-group-show).

```azurepowershell
$objectid = (Get-AzADGroup -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad group show --group "{name}" --query objectId --output tsv)
```

### <a name="managed-identities"></a>Управляемые удостоверения

Чтобы получить идентификатор управляемого удостоверения, можно использовать команды [Get-азадсервицепринЦипал](/powershell/module/az.resources/get-azadserviceprincipal) или [AZ AD SP](/cli/azure/ad/sp) .

```azurepowershell
$objectid = (Get-AzADServicePrincipal -DisplayName <Azure resource name>).id
```

```azurecli
objectid=$(az ad sp list --display-name <Azure resource name> --query [].objectId --output tsv)
```

### <a name="application"></a>Приложение

Чтобы получить идентификатор субъекта-службы (удостоверения, используемого приложением), можно использовать команды [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) или [az ad sp list](/cli/azure/ad/sp#az-ad-sp-list). Для субъекта-службы используйте идентификатор объекта, а **не** идентификатор приложения.

```azurepowershell
$objectid = (Get-AzADServicePrincipal -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad sp list --display-name "{name}" --query [].objectId --output tsv)
```

## <a name="add-a-role-assignment"></a>Добавление назначения роли

Чтобы предоставить доступ в Azure RBAC, нужно добавить назначение ролей.

### <a name="resource-group-scope-without-parameters"></a>Область группы ресурсов (без параметров)

Следующий шаблон демонстрирует базовый способ добавления назначения ролей. Некоторые значения указываются в шаблоне. Приведенный ниже шаблон демонстрирует:

-  как назначить роль [Reader](built-in-roles.md#reader) пользователю, группе или приложению в области действия группы ресурсов.

Чтобы использовать шаблон, необходимо выполнить следующие действия:

- создать новый JSON-файл и скопировать шаблон;
- Замените `<your-principal-id>` идентификатором пользователя, группы, управляемого удостоверения или приложения, которому назначается роль.

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

Ниже приведены примеры команд [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) и [az group deployment create](/cli/azure/group/deployment#az-group-deployment-create), показывающие, как запустить развертывание в группе ресурсов с именем ExampleGroup.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file rbac-test.json
```

Ниже приведен пример назначения роли Reader пользователю для группы ресурсов после развертывания шаблона.

![Назначение ролей в области группы ресурсов](./media/role-assignments-template/role-assignment-template.png)

### <a name="resource-group-or-subscription-scope"></a>Область группы ресурсов или подписки

Предыдущий шаблон не очень гибкий. В следующем шаблоне используются параметры, и его можно использовать в разных областях. Приведенный ниже шаблон демонстрирует:

- как назначить роль пользователю, группе или приложению в области группы ресурсов или подписки;
- как задать роли владельца, участника или читателя в качестве параметра.

Чтобы использовать шаблон, необходимо указать следующие входные данные:

- Идентификатор пользователя, группы, управляемого удостоверения или приложения, которому назначается роль
- уникальный идентификатор, который будет использоваться для назначения ролей (можно также использовать идентификатор по умолчанию).

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
> Этот шаблон не идемпотентный, если для каждого развертывания шаблона в качестве параметра не предоставляется одно и то же значение `roleNameGuid`. Если значение `roleNameGuid` не предоставляется, по умолчанию в каждом развертывании создается новый идентификатор GUID, и последующие развертывания завершаются с ошибкой `Conflict: RoleAssignmentExists`.

Область назначения ролей определяется исходя из уровня развертывания. Ниже приведены примеры команд [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) и [az group deployment create](/cli/azure/group/deployment#az-group-deployment-create), показывающие, как запустить развертывание в области группы ресурсов.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Reader
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Reader
```

Ниже приведены примеры команд [New-AzDeployment](/powershell/module/az.resources/new-azdeployment) и [az deployment create](/cli/azure/deployment#az-deployment-create), показывающие, как запустить развертывание в области подписки и указать расположение.

```azurepowershell
New-AzDeployment -Location centralus -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Reader
```

```azurecli
az deployment create --location centralus --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Reader
```

### <a name="resource-scope"></a>Область ресурса

Если необходимо добавить назначение ролей на уровне ресурса, используется другой формат назначения ролей. Вы указываете пространство имен поставщика ресурсов и тип того ресурса, которому нужно назначить роль. Вы также включаете имя ресурса в имя назначения ролей.

Для типа и имени назначения ролей используйте следующий формат:

```json
"type": "{resource-provider-namespace}/{resource-type}/providers/roleAssignments",
"name": "{resource-name}/Microsoft.Authorization/{role-assign-GUID}"
```

Приведенный ниже шаблон демонстрирует:

- Как создать учетную запись хранения
- как назначить роль пользователю, группе или приложению в области учетной записи хранения;
- как задать роли владельца, участника или читателя в качестве параметра.

Чтобы использовать шаблон, необходимо указать следующие входные данные:

- Идентификатор пользователя, группы, управляемого удостоверения или приложения, которому назначается роль

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

Чтобы развернуть предыдущий шаблон, используйте команды группы ресурсов. Ниже приведены примеры команд [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) и [az group deployment create](/cli/azure/group/deployment#az-group-deployment-create), показывающие, как запустить развертывание в области ресурса.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Contributor
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Contributor
```

Ниже приведен пример назначения роли Contributor пользователю для учетной записи хранения после развертывания шаблона.

![Назначение ролей в области ресурса](./media/role-assignments-template/role-assignment-template-resource.png)

### <a name="new-service-principal"></a>Новый субъект-служба

Если вы создадите новый субъект-службу и сразу же попытаетесь назначить роль этому субъекту-службе, в некоторых случаях такое назначение роли может завершиться ошибкой. Например, если создать новое управляемое удостоверение и затем попытаться назначить роль этому субъекту-службе в том же шаблоне Azure Resource Manager, назначение роли может завершиться ошибкой. Наиболее вероятной причиной этой ошибки является задержка репликации. Субъект-служба создается в одном регионе; однако назначение ролей может происходить в другом регионе, который еще не реплицировал субъект-службу. Для решения этой проблемы при создании назначения ролей необходимо задать для свойства `principalType` значение `ServicePrincipal`.

Приведенный ниже шаблон демонстрирует:

- как создать новый субъект-службу управляемого удостоверения;
- как указать свойство `principalType`;
- как назначить роль Contributor этому субъекту-службе в области группы ресурсов.

Чтобы использовать шаблон, необходимо указать следующие входные данные:

- базовое имя управляемого удостоверения (можно также использовать строку по умолчанию).

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

Ниже приведены примеры команд [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) и [az group deployment create](/cli/azure/group/deployment#az-group-deployment-create), показывающие, как запустить развертывание в области группы ресурсов.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup2 -TemplateFile rbac-test.json
```

```azurecli
az group deployment create --resource-group ExampleGroup2 --template-file rbac-test.json
```

Ниже приведен пример назначения роли Contributor новому субъекту-службе управляемого удостоверения после развертывания шаблона.

![Назначение ролей для нового субъекта-службы управляемого удостоверения](./media/role-assignments-template/role-assignment-template-msi.png)

## <a name="remove-a-role-assignment"></a>Удаление назначения ролей

В Azure RBAC для удаления доступа к ресурсу Azure следует удалить назначение ролей. Невозможно удалить назначение ролей с помощью шаблона. Чтобы удалить назначение ролей, необходимо использовать другие средства, такие как:

- [Портал Azure](role-assignments-portal.md#remove-a-role-assignment)
- [Azure PowerShell](role-assignments-powershell.md#remove-a-role-assignment)
- [Azure CLI](role-assignments-cli.md#remove-role-assignment)
- [REST API](role-assignments-rest.md#remove-a-role-assignment)

## <a name="next-steps"></a>Дальнейшие действия

- [Краткое руководство. Создание и развертывание шаблонов Azure Resource Manager с помощью портала Azure](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)
- [Описание структуры и синтаксиса шаблонов Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md)
- [Создание групп ресурсов и ресурсов на уровне подписки](../azure-resource-manager/templates/deploy-to-subscription.md)
- [Шаблоны быстрого запуска Azure](https://azure.microsoft.com/resources/templates/?term=rbac)
