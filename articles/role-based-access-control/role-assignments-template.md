---
title: Управление доступом к ресурсам Azure с помощью RBAC и шаблонов Azure Resource Manager | Документация Майкрософт
description: Узнайте, как управлять доступом к ресурсам Azure пользователей, групп и приложений с помощью управления доступом на основе ролей (RBAC) и шаблонов Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/21/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 268913fb7aebd1d6c8b377b95939c3bc1f77daca
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383988"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-azure-resource-manager-templates"></a>Управление доступом к ресурсам Azure с помощью RBAC и шаблонов Azure Resource Manager

[Управление доступом на основе ролей (RBAC)](overview.md) — это способ управления доступом к ресурсам в Azure. Помимо использования Azure PowerShell или Azure CLI вы можете управлять доступом к ресурсам Azure с помощью [шаблонов Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md). Шаблоны могут быть полезны для согласованного и многократного развертывания ресурсов. В этой статье описывается, как управлять доступом с помощью RBAC и шаблонов.

## <a name="get-object-ids"></a>Получение идентификаторов объектов

Чтобы назначить роль, необходимо указать идентификатор пользователя, группы или приложения, которым будет назначена роль. Идентификатор имеет формат: `11111111-1111-1111-1111-111111111111`. Идентификатор можно получить с помощью портал Azure, Azure PowerShell или Azure CLI.

### <a name="user"></a>Пользователь

Чтобы получить идентификатор пользователя, можно использовать команды [Get-азадусер](/powershell/module/az.resources/get-azaduser) или [AZ AD user показа](/cli/azure/ad/user#az-ad-user-show) .

```azurepowershell
$objectid = (Get-AzADUser -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad user show --id "{email}" --query objectId --output tsv)
```

### <a name="group"></a>Группа

Чтобы получить идентификатор группы, можно воспользоваться командами [Get-азадграуп](/powershell/module/az.resources/get-azadgroup) или [AZ AD Group показывать](/cli/azure/ad/group#az-ad-group-show) команды.

```azurepowershell
$objectid = (Get-AzADGroup -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad group show --group "{name}" --query objectId --output tsv)
```

### <a name="application"></a>Приложение

Чтобы получить идентификатор субъекта-службы (удостоверения, используемого приложением), можно использовать команды [Get-азадсервицепринЦипал](/powershell/module/az.resources/get-azadserviceprincipal) или [AZ AD SP List](/cli/azure/ad/sp#az-ad-sp-list) . Для субъекта-службы используйте идентификатор объекта, а **не** идентификатор приложения.

```azurepowershell
$objectid = (Get-AzADServicePrincipal -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad sp list --display-name "{name}" --query [].objectId --output tsv)
```

## <a name="create-a-role-assignment-at-a-resource-group-scope-without-parameters"></a>Создание назначения роли в области группы ресурсов (без параметров)

Чтобы в RBAC предоставить доступ, нужно создать назначение ролей. В следующем шаблоне показан базовый способ создания назначения роли. Некоторые значения указываются в шаблоне. Приведенный ниже шаблон демонстрирует:

-  Назначение роли [читателя](built-in-roles.md#reader) пользователю, группе или приложению в области группы ресурсов

Чтобы использовать шаблон, необходимо выполнить следующие действия.

- Создание нового JSON-файла и копирование шаблона
- Замените `<your-principal-id>` ИДЕНТИФИКАТОРом пользователя, группы или приложения, которому назначается роль.

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

Ниже приведены примеры команд [New-азресаурцеграупдеплоймент](/powershell/module/az.resources/new-azresourcegroupdeployment) и [AZ Group Deployment Create](/cli/azure/group/deployment#az-group-deployment-create) для запуска развертывания в группе ресурсов с именем ексамплеграуп.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file rbac-test.json
```

Ниже приведен пример назначения роли читателя пользователю для группы ресурсов после развертывания шаблона.

![Назначение ролей в области группы ресурсов](./media/role-assignments-template/role-assignment-template.png)

## <a name="create-a-role-assignment-at-a-resource-group-or-subscription-scope"></a>Создание назначения роли в группе ресурсов или области подписки

Предыдущий шаблон не очень гибок. В следующем шаблоне используются параметры, и их можно использовать в разных областях. Приведенный ниже шаблон демонстрирует:

- Назначение роли пользователю, группе или приложению в области группы ресурсов или подписки
- как задать роли владельца, участника или читателя в качестве параметра.

Чтобы использовать шаблон, необходимо указать следующие входные данные:

- Идентификатор пользователя, группы или приложения, которым назначается роль
- Уникальный идентификатор, который будет использоваться для назначения роли, или можно использовать идентификатор по умолчанию

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
> Этот шаблон не идемпотентными, если в качестве параметра для каждого развертывания шаблона не указано одно и то же `roleNameGuid` значение. Если `roleNameGuid` не указано, по умолчанию создается новый идентификатор GUID для каждого развертывания, и последующие развертывания завершатся ошибкой `Conflict: RoleAssignmentExists`.

Область назначения роли определяется на основе уровня развертывания. Ниже приведены примеры команд [New-азресаурцеграупдеплоймент](/powershell/module/az.resources/new-azresourcegroupdeployment) и [AZ Group Deployment Create](/cli/azure/group/deployment#az-group-deployment-create) для запуска развертывания в области группы ресурсов.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Reader
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Reader
```

Ниже приведены примеры команд [New-аздеплоймент](/powershell/module/az.resources/new-azdeployment) и [AZ Deployment Create](/cli/azure/deployment#az-deployment-create) , чтобы запустить развертывание в области действия подписки и указать расположение.

```azurepowershell
New-AzDeployment -Location centralus -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Reader
```

```azurecli
az deployment create --location centralus --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Reader
```

## <a name="create-a-role-assignment-at-a-resource-scope"></a>Создание назначения роли в области действия ресурса

Если необходимо создать назначение роли на уровне ресурса, то формат назначения роли отличается. Укажите пространство имен поставщика ресурсов и тип ресурса для назначения роли. Вы также включаете имя ресурса в имя назначения роли.

Для типа и имени назначения роли используйте следующий формат:

```json
"type": "{resource-provider-namespace}/{resource-type}/providers/roleAssignments",
"name": "{resource-name}/Microsoft.Authorization/{role-assign-GUID}"
```

Приведенный ниже шаблон демонстрирует:

- Как создать учетную запись хранения
- Назначение роли пользователю, группе или приложению в области учетной записи хранения
- как задать роли владельца, участника или читателя в качестве параметра.

Чтобы использовать шаблон, необходимо указать следующие входные данные:

- Идентификатор пользователя, группы или приложения, которым назначается роль

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

Чтобы развернуть предыдущий шаблон, используйте команды группы ресурсов. Ниже приведены примеры команд [New-азресаурцеграупдеплоймент](/powershell/module/az.resources/new-azresourcegroupdeployment) и [AZ Group Deployment Create](/cli/azure/group/deployment#az-group-deployment-create) для запуска развертывания в области действия ресурса.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Contributor
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Contributor
```

Ниже приведен пример назначения роли участника пользователю для учетной записи хранения после развертывания шаблона.

![Назначение ролей в области действия ресурса](./media/role-assignments-template/role-assignment-template-resource.png)

## <a name="create-a-role-assignment-for-a-new-service-principal"></a>Создание назначения роли для нового субъекта-службы

Если создать новый субъект-службу и сразу же попытаться назначить роль этому субъекту-службе, в некоторых случаях такое назначение роли может завершиться ошибкой. Например, если создать новое управляемое удостоверение и затем попытаться назначить роль этому субъекту-службе в том же шаблоне Azure Resource Manager, то назначение роли может завершиться ошибкой. Причиной этого сбоя может быть задержка репликации. Субъект-служба создается в одном регионе; Однако назначение роли может происходить в другом регионе, который еще не реплицирует субъект-службу. Для решения этой ситуации следует задать для свойства `principalType` значение `ServicePrincipal` при создании назначения роли.

Приведенный ниже шаблон демонстрирует:

- Создание нового субъекта-службы управляемого удостоверения
- Как указать `principalType`
- Назначение роли участника этому субъекту-службе в области группы ресурсов

Чтобы использовать шаблон, необходимо указать следующие входные данные:

- Базовое имя управляемого удостоверения или можно использовать строку по умолчанию

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

Ниже приведены примеры команд [New-азресаурцеграупдеплоймент](/powershell/module/az.resources/new-azresourcegroupdeployment) и [AZ Group Deployment Create](/cli/azure/group/deployment#az-group-deployment-create) для запуска развертывания в области группы ресурсов.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup2 -TemplateFile rbac-test.json
```

```azurecli
az group deployment create --resource-group ExampleGroup2 --template-file rbac-test.json
```

Ниже приведен пример назначения роли участника новому управляемому субъекту-службе удостоверений после развертывания шаблона.

![Назначение ролей для нового субъекта-службы управляемого удостоверения](./media/role-assignments-template/role-assignment-template-msi.png)

## <a name="next-steps"></a>Дополнительная информация

- [Краткое руководство по созданию и развертыванию шаблонов Azure Resource Manager с помощью портала Azure](../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md)
- [Описание структуры и синтаксиса шаблонов Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md)
- [Создание групп ресурсов и ресурсов на уровне подписки](../azure-resource-manager/deploy-to-subscription.md)
- [Шаблоны быстрого запуска Azure](https://azure.microsoft.com/resources/templates/?term=rbac)
