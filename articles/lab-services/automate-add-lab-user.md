---
title: Автоматизировать добавление пользователь лаборатории в Azure DevTest Labs | Документация Майкрософт
description: Узнайте, как автоматизировать добавление пользователь лаборатории в лабораторию в Azure DevTest Labs.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/02/2019
ms.author: spelluru
ms.openlocfilehash: 0eed874d405fcf99241a702292f8ceadae6c5a07
ms.sourcegitcommit: 1d257ad14ab837dd13145a6908bc0ed7af7f50a2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/09/2019
ms.locfileid: "65502026"
---
# <a name="automate-adding-a-lab-user-to-a-lab-in-azure-devtest-labs"></a>Автоматизировать добавление пользователь лаборатории в лабораторию в Azure DevTest Labs
Azure DevTest Labs позволяет быстро создавать среды разработки и тестирования, самообслуживания с помощью портала Azure. Тем не менее если у вас есть несколько команд и несколько экземпляров DevTest Labs, автоматизируя процесс создания можно сэкономить время. [Шаблоны Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates) позволяют создавать лабораторные занятия, виртуальных машин лаборатории, пользовательских образов, формул, и добавьте пользователей в автоматическом режиме. В этой статье особое внимание уделяется Добавление пользователей к экземпляру DevTest Labs.

Чтобы добавить пользователя в лабораторию, добавить пользователя **пользователь DevTest Labs** роли для лаборатории. В этой статье показано, как автоматизировать добавление пользователя в лабораторию с помощью одного из следующих способов:

- Шаблоны Azure Resource Manager
- Командлеты Azure PowerShell 
- Azure CLI.

## <a name="use-azure-resource-manager-templates"></a>Использование шаблонов диспетчера ресурсов Azure
Следующий пример шаблона Resource Manager указывает пользователя для добавления **пользователь DevTest Labs** роли лаборатории. 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "type": "string",
      "metadata": {
        "description": "The objectId of the user, group, or service principal for the role."
      }
    },
    "labName": {
      "type": "string",
      "metadata": {
        "description": "The name of the lab instance to be created."
      }
    },
    "roleAssignmentGuid": {
      "type": "string",
      "metadata": {
        "description": "Guid to use as the name for the role assignment."
      }
    }
  },
  "variables": {
    "devTestLabUserRoleId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/111111111-0000-0000-11111111111111111')]",
    "fullDevTestLabUserRoleName": "[concat(parameters('labName'), '/Microsoft.Authorization/', parameters('roleAssignmentGuid'))]",
    "roleScope": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.DevTestLab/labs/', parameters('labName'))]"
  },
  "resources": [
    {
      "apiVersion": "2016-05-15",
      "type": "Microsoft.DevTestLab/labs",
      "name": "[parameters('labName')]",
      "location": "[resourceGroup().location]"
    },
    {
      "apiVersion": "2016-07-01",
      "type": "Microsoft.DevTestLab/labs/providers/roleAssignments",
      "name": "[variables('fullDevTestLabUserRoleName')]",
      "properties": {
        "roleDefinitionId": "[variables('devTestLabUserRoleId')]",
        "principalId": "[parameters('principalId')]",
        "scope": "[variables('roleScope')]"
      },
      "dependsOn": [
        "[resourceId('Microsoft.DevTestLab/labs', parameters('labName'))]"
      ]
    }
  ]
}

```

Если назначить роль в том же шаблоне, используемая для создания лаборатории, не забудьте добавить зависимость между ресурса назначения роли и лаборатории. Дополнительные сведения см. в разделе [Определение зависимостей в шаблонах Azure Resource Manager](/azure-resource-manager/resource-group-define-dependencies.md) статьи.

### <a name="role-assignment-resource-information"></a>Сведения о ресурсах назначения роли
Ресурса назначения роли необходимо указать тип и имя.

Является прежде всего следует отметить, что тип ресурса не `Microsoft.Authorization/roleAssignments` как бы для группы ресурсов.  Вместо этого тип ресурса по шаблону `{provider-namespace}/{resource-type}/providers/roleAssignments`. В этом случае тип ресурса будет `Microsoft.DevTestLab/labs/providers/roleAssignments`.

Имя назначения роли сам должно быть глобально уникальным.  Имя назначения использует шаблон `{labName}/Microsoft.Authorization/{newGuid}`. `newGuid` Являются значением параметра шаблона. Он гарантирует, что имя назначения роли является уникальным. Так как отсутствуют функции шаблона для создания идентификаторов GUID, необходимо создать GUID самостоятельно с помощью любого средства генератор GUID.  

В шаблоне, определяется имя для назначения роли `fullDevTestLabUserRoleName` переменной. Ниже приведен точный идентификатор строки из шаблона.

```json
"fullDevTestLabUserRoleName": "[concat(parameters('labName'), '/Microsoft.Authorization/', parameters('roleAssignmentGuid'))]"
```


### <a name="role-assignment-resource-properties"></a>Свойства ресурса назначения роли
Назначение ролей, сам определяет три свойства. Он должен `roleDefinitionId`, `principalId`, и `scope`.

### <a name="role-definition"></a>Определение роли
Идентификатор определения роли — это идентификатор строки для существующего определения роли. Идентификатор роли в виде `/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}`. 

Идентификатор подписки можно получить с помощью `subscription().subscriptionId` функции шаблона.  

Вам нужно получить определение роли для `DevTest Labs User` встроенной роли. Чтобы получить идентификатор GUID для [пользователь DevTest Labs](../role-based-access-control/built-in-roles.md#devtest-labs-user) роли, можно использовать [API REST для назначений роли](/rest/api/authorization/roleassignments) или [Get AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition?view=azps-1.8.0) командлета.

```powershell
$dtlUserRoleDefId = (Get-AzRoleDefinition -Name "DevTest Labs User").Id
```

Идентификатор роли определяется в разделе переменных и с именем `devTestLabUserRoleId`. В шаблоне идентификатор роли имеет значение: 111111111-0000-0000-11111111111111111. 

```json
"devTestLabUserRoleId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/111111111-0000-0000-11111111111111111')]",
```

### <a name="principal-id"></a>Идентификатор субъекта
Идентификатор участника — идентификатор объекта пользователя Active Directory, группы или субъекта-службы, который требуется добавить в качестве пользователя лаборатории в лаборатории. В шаблоне используется `ObjectId` как параметр.

ObjectId можно получить с помощью [Get-AzureRMADUser](/powershell/module/azurerm.resources/get-azurermaduser?view=azurermps-6.13.0), [Get-AzureRMADGroup, или [Get-AzureRMADServicePrincipal](/powershell/module/azurerm.resources/get-azurermadserviceprincipal?view=azurermps-6.13.0) командлеты PowerShell. Эти командлеты возвращают один или списки объектов Active Directory, которые содержат свойства ID, это идентификатор объекта, который требуется. Приведенный ниже показано, как получить идентификатор объекта отдельного пользователя в компании.

```powershell
$userObjectId = (Get-AzureRmADUser -UserPrincipalName ‘email@company.com').Id
```

Можно также использовать командлеты Azure Active Directory PowerShell, включить [Get-MsolUser](/powershell/module/msonline/get-msoluser?view=azureadps-1.0), [Get-MsolGroup](/powershell/module/msonline/get-msolgroup?view=azureadps-1.0), и [Get-MsolServicePrincipal](/powershell/module/msonline/get-msolserviceprincipal?view=azureadps-1.0).

### <a name="scope"></a>`Scope`
Область указывает ресурс или группу ресурсов, для которого следует применить назначение ролей. Для ресурсов, область имеет следующий формат: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{provider-namespace}/{resource-type}/{resource-name}`. В шаблоне используется `subscription().subscriptionId` функции для заполнения `subscription-id` часть и `resourceGroup().name` функции шаблона для заполнения `resource-group-name` часть. Использование этих функций, это означает, что лаборатории, к которому назначение роли должны существовать в текущей подписке и той же группе ресурсов, на который производится развертывание шаблона. Последняя часть `resource-name`, имя лабораторной среде. Это значение получено с помощью параметра-шаблона в этом примере. 

Области применения роли в шаблоне: 

```json
"roleScope": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.DevTestLab/labs/', parameters('labName'))]"
```

### <a name="deploying-the-template"></a>Развертывание шаблона
Во-первых, создайте файл параметров (например: azuredeploy.parameters.json), передает значения для параметров в шаблоне Resource Manager. 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "value": "11111111-1111-1111-1111-111111111111"
    },
    "labName": {
      "value": "MyLab"
    },
    "roleAssignmentGuid": {
      "value": "22222222-2222-2222-2222-222222222222"
    }
  }
}
```

Затем с помощью [New-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment?view=azurermps-6.13.0) командлет PowerShell, чтобы развернуть шаблон Resource Manager. Следующая команда назначает роль пользователя DevTest Labs в лаборатории человека, группы или субъекта-службы.

```powershell
New-AzureRmResourceGroupDeployment -Name "MyLabResourceGroup-$(New-Guid)" -ResourceGroupName 'MyLabResourceGroup' -TemplateParameterFile .\azuredeploy.parameters.json -TemplateFile .\azuredeploy.json
```

Важно отметить, что группа развертывания имя и назначение ролей GUID должны быть уникальными. Если попытаться развернуть назначением ресурса не уникальный идентификатор GUID, то вы получите `RoleAssignmentUpdateNotPermitted` ошибки.

Если вы планируете использовать шаблон несколько раз, чтобы добавить несколько объектов Active Directory в роль пользователя DevTest Labs для лаборатории, рассмотрите возможность использования динамических объектов в вашей команде PowerShell. В следующем примере используется [New-Guid](/powershell/module/Microsoft.PowerShell.Utility/New-Guid?view=powershell-5.0) , чтобы указать ресурс группы развертывания имя и назначение ролей GUID динамически.

```powershell
New-AzureRmResourceGroupDeployment -Name "MyLabResourceGroup-$(New-Guid)" -ResourceGroupName 'MyLabResourceGroup' -TemplateFile .\azuredeploy.json -roleAssignmentGuid "$(New-Guid)" -labName "MyLab" -principalId "11111111-1111-1111-1111-111111111111"
```

## <a name="use-azure-powershell"></a>Использование Azure PowerShell
Как описано во введении, создается новое назначение роли Azure для добавления пользователя к **пользователь DevTest Labs** роли для лаборатории. В PowerShell, можно сделать с помощью [New-AzureRMRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment?view=azurermps-6.13.0) командлета. Этот командлет имеет много необязательных параметров для обеспечения гибкости. `ObjectId`, `SigninName`, Или `ServicePrincipalName` можно указать в качестве объекта наличия разрешений.  

Ниже приведен пример команды Azure PowerShell, которое добавляет пользователя в роль пользователя DevTest Labs в указанного лаборатории.

```powershell
New-AzureRmRoleAssignment -UserPrincipalName <email@company.com> -RoleDefinitionName 'DevTest Labs User' -ResourceName '<Lab Name>' -ResourceGroupName '<Resource Group Name>' -ResourceType 'Microsoft.DevTestLab/labs'
```

Для указания ресурса, к которому выполняется разрешения предоставляются можно указать с помощью сочетания `ResourceName`, `ResourceType`, `ResourceGroup` или `scope` параметра. Любое сочетание параметров используется, содержать достаточно информации для командлета для уникальной идентификации объекта Active Directory (пользователя, группы или субъекта-службы), область (группы ресурсов или ресурса) и определения ролей.

## <a name="use-azure-command-line-interface-cli"></a>Использовать Azure интерфейс командной строки (CLI)
В Azure CLI, добавление пользователь labs в лабораторию, выполняется с помощью `az role assignment create` команды. Дополнительные сведения о командлетах Azure CLI, см. в разделе [управление доступом к ресурсам Azure с помощью RBAC и Azure CLI](../role-based-access-control/role-assignments-cli.md).

Объект, которому предоставляются доступ может быть указан с `objectId`, `signInName`, `spn` параметров. Лаборатории, к которому объект предоставляется доступ, можно определить по `scope` URL-адрес или сочетание `resource-name`, `resource-type`, и `resource-group` параметров.

В следующем примере Azure CLI показано, как для добавления пользователя в роль пользователя DevTest Labs для указанного лаборатории.  

```azurecli
az role assignment create --roleName "DevTest Labs User" --signInName <email@company.com> -–resource-name "<Lab Name>" --resource-type “Microsoft.DevTestLab/labs" --resource-group "<Resource Group Name>"
```

## <a name="next-steps"></a>Дальнейшие действия
Ознакомьтесь со следующими статьями:

- [Создание и управление виртуальными машинами с помощью DevTest Labs с помощью Azure CLI](devtest-lab-vmcli.md)
- [Создание виртуальной машины с помощью DevTest Labs с помощью Azure PowerShell](devtest-lab-vm-powershell.md)
- [Использование средств командной строки для запуска и остановки виртуальных машин Azure DevTest Labs](use-command-line-start-stop-virtual-machines.md)

