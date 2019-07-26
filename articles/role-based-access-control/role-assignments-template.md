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
ms.date: 07/19/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: e6511ff84c251577a5ff483f892387ab7d3d4d41
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68360450"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-azure-resource-manager-templates"></a>Управление доступом к ресурсам Azure с помощью RBAC и шаблонов Azure Resource Manager

[Управление доступом на основе ролей (RBAC)](overview.md) — это способ управления доступом к ресурсам Azure. В дополнение к использованию Azure PowerShell или Azure CLI доступом к ресурсам Azure можно управлять с помощью RBAC и [шаблонов Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md). Шаблоны могут быть полезны для согласованного и многократного развертывания ресурсов. В этой статье описывается, как управлять доступом с помощью RBAC и шаблонов.

## <a name="assign-role-to-resource-group-or-subscription"></a>Назначение роли группе ресурсов или подписке

При использовании RBAC, чтобы предоставить доступ, нужно создать назначение ролей. Приведенный ниже шаблон демонстрирует:
- Назначение роли пользователю, группе или приложению в области группы ресурсов или подписки
- как задать роли владельца, участника или читателя в качестве параметра.

Чтобы использовать шаблон, необходимо указать следующие входные данные:
- уникальный идентификатор пользователя, группы или приложения, которым назначается роль;
- роль для назначения;
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

Ниже приведен пример назначения роли читателя пользователю для группы ресурсов после развертывания шаблона.

![Назначение роли с помощью шаблона](./media/role-assignments-template/role-assignment-template.png)

Область назначения роли определяется на основе уровня развертывания. В этой статье приведены команды развертывания на уровне группы ресурсов и подписки.

## <a name="assign-role-to-resource"></a>Назначение роли ресурсу

Если необходимо создать назначение роли на уровне ресурса, то формат назначения роли отличается. Укажите пространство имен поставщика ресурсов и тип ресурса для назначения роли. Вы также включаете имя ресурса в имя назначения роли.

Для типа и имени назначения роли используйте следующий формат:

```json
"type": "{resource-provider-namespace}/{resource-type}/providers/roleAssignments",
"name": "{resource-name}/Microsoft.Authorization/{role-assign-GUID}"
```

Следующий шаблон развертывает учетную запись хранения и назначает ей роль. Его можно развернуть с помощью команд группы ресурсов.

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
      "name": "[concat(variables('storageName'), '/Microsoft.Authorization/', parameters('roleNameGuid'))]",
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

Ниже приведен пример назначения роли участника пользователю для учетной записи хранения после развертывания шаблона.

![Назначение роли с помощью шаблона](./media/role-assignments-template/role-assignment-template-resource.png)

## <a name="deploy-template-using-azure-powershell"></a>Развертывание шаблона с помощью Azure PowerShell

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

Чтобы развернуть предыдущий шаблон либо в группе ресурсов, либо в подписке с помощью Azure PowerShell, выполните следующие действия.

1. Создайте файл с именем rbac rg.json и скопируйте предыдущий шаблон.

1. Войдите в [Azure PowerShell](/powershell/azure/authenticate-azureps).

1. Получите уникальный идентификатор пользователя, группы или приложения. Например, чтобы получить список пользователей Azure AD, можно использовать команду [Get-AzADUser](/powershell/module/az.resources/get-azaduser).

    ```azurepowershell
    $userid = (Get-AzADUser -DisplayName "{name}").id
    ```

1. Шаблон создает значение по умолчанию для идентификатора GUID, используемого для задания назначения роли. Если необходимо указать конкретный GUID, передайте это значение в параметре Роленамегуид. Этот идентификатор имеет следующий формат: `11111111-1111-1111-1111-111111111111`

Чтобы назначить роль на уровне ресурса или группы ресурсов, выполните следующие действия.

1. Создайте пример группы ресурсов.

    ```azurepowershell
    New-AzResourceGroup -Name ExampleGroup -Location "Central US"
    ```

1. Чтобы начать развертывание, используйте команду [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment).

    ```azurepowershell
    New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-rg.json -principalId $userid -builtInRoleType Reader
    ```

    Ниже приведен пример выходных данных.

    ```Output
    PS /home/user> New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-rg.json -principalId $userid -builtInRoleType Reader
    
    DeploymentName          : rbac-rg
    ResourceGroupName       : ExampleGroup
    ProvisioningState       : Succeeded
    Timestamp               : 7/17/2018 7:46:32 PM
    Mode                    : Incremental
    TemplateLink            :
    Parameters              :
                              Name             Type                       Value
                              ===============  =========================  ==========
                              principalId      String                     22222222-2222-2222-2222-222222222222
                              builtInRoleType  String                     Reader
                              roleNameGuid     String                     11111111-1111-1111-1111-111111111111
    
    Outputs                 :
    DeploymentDebugLogLevel :
    ```

Чтобы назначить роль на уровне подписки, используйте команду [New-аздеплоймент](/powershell/module/az.resources/new-azdeployment) и укажите расположение для развертывания.

```azurepowershell
New-AzDeployment -Location centralus -TemplateFile rbac-rg.json -principalId $userid -builtInRoleType Reader
```

Он имеет аналогичные выходные данные команды развертывания для групп ресурсов.

## <a name="deploy-template-using-the-azure-cli"></a>Развертывание шаблона с помощью интерфейса командной строки Azure

Чтобы развернуть предыдущий шаблон с помощью Azure CLI либо в группе ресурсов, либо в подписке, выполните следующие действия.

1. Создайте файл с именем rbac rg.json и скопируйте предыдущий шаблон.

1. Войдите в [Azure CLI](/cli/azure/authenticate-azure-cli).

1. Получите уникальный идентификатор пользователя, группы или приложения. Например, можно использовать команду [AZ AD user показ](/cli/azure/ad/user#az-ad-user-show) , чтобы отобразить пользователя Azure AD.

    ```azurecli
    userid=$(az ad user show --upn-or-object-id "{email}" --query objectId --output tsv)
    ```

1. Шаблон создает значение по умолчанию для идентификатора GUID, используемого для задания назначения роли. Если необходимо указать конкретный GUID, передайте это значение в параметре Роленамегуид. Этот идентификатор имеет следующий формат: `11111111-1111-1111-1111-111111111111`

Чтобы назначить роль на уровне ресурса или группы ресурсов, выполните следующие действия.

1. Создайте пример группы ресурсов.

    ```azurecli
    az group create --name ExampleGroup --location "Central US"
    ```

1. Чтобы начать развертывание, используйте команду [az group deployment create](/cli/azure/group/deployment#az-group-deployment-create).

    ```azurecli
    az group deployment create --resource-group ExampleGroup --template-file rbac-rg.json --parameters principalId=$userid builtInRoleType=Reader
    ```

    Ниже приведен пример выходных данных.

    ```Output
    C:\Azure\Templates>az group deployment create --resource-group ExampleGroup --template-file rbac-rg.json --parameters principalId=$userid builtInRoleType=Reader
    
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ExampleGroup/providers/Microsoft.Resources/deployments/rbac-rg",
      "name": "rbac-rg",
      "properties": {
        "additionalProperties": {
          "duration": "PT9.5323924S",
          "outputResources": [
            {
              "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ExampleGroup/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111",
              "resourceGroup": "ExampleGroup"
            }
          ],
          "templateHash": "0000000000000000000"
        },
        "correlationId": "33333333-3333-3333-3333-333333333333",
        "debugSetting": null,
        "dependencies": [],
        "mode": "Incremental",
        "outputs": null,
        "parameters": {
          "builtInRoleType": {
            "type": "String",
            "value": "Reader"
          },
          "principalId": {
            "type": "String",
            "value": "22222222-2222-2222-2222-222222222222"
          },
          "roleNameGuid": {
            "type": "String",
            "value": "11111111-1111-1111-1111-111111111111"
          }
        },
        "parametersLink": null,
        "providers": [
          {
            "id": null,
            "namespace": "Microsoft.Authorization",
            "registrationState": null,
            "resourceTypes": [
              {
                "aliases": null,
                "apiVersions": null,
                "locations": [
                  null
                ],
                "properties": null,
                "resourceType": "roleAssignments"
              }
            ]
          }
        ],
        "provisioningState": "Succeeded",
        "template": null,
        "templateLink": null,
        "timestamp": "2018-07-17T19:00:31.830904+00:00"
      },
      "resourceGroup": "ExampleGroup"
    }
    ```

Чтобы назначить роль на уровне подписки, используйте команду [AZ Deployment Create](/cli/azure/deployment#az-deployment-create) и укажите расположение развертывания.

```azurecli
az deployment create --location centralus --template-file rbac-rg.json --parameters principalId=$userid builtInRoleType=Reader
```

Он имеет аналогичные выходные данные команды развертывания для групп ресурсов.

## <a name="next-steps"></a>Следующие шаги

- [Краткое руководство Создание и развертывание шаблонов Azure Resource Manager с помощью портала Azure](../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md)
- [Описание структуры и синтаксиса шаблонов Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md)
- [Шаблоны быстрого запуска Azure](https://azure.microsoft.com/resources/templates/?term=rbac)
