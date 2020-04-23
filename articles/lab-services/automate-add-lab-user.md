---
title: Автоматизировать добавление лабораторного пользователя в Лаборатории Azure DevTest (ru) Документы Майкрософт
description: В этой статье показано, как автоматизировать добавление пользователя в лабораторию в Лабораториях Azure DevTest с помощью шаблонов Azure Resource Manager, PowerShell и CLI.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 81a8c5030f716246caf3dcd8b540bb47fcaf6520
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "82023628"
---
# <a name="automate-adding-a-lab-user-to-a-lab-in-azure-devtest-labs"></a>Автоматизировать добавление лабораторного пользователя в лабораторию в Лаборатории Azure DevTest Labs
Лазурная Лаборатория DevTest позволяет быстро создавать среды самообслуживания с помощью портала Azure. Однако, если у вас есть несколько групп и несколько экземпляров DevTest Labs, автоматизация процесса создания может сэкономить время. [Шаблоны Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/Environments) позволяют создавать лаборатории, лабораторные вм- и пользовательские изображения, формулы и автоматически добавлять пользователей. В этой статье особое внимание уделяется добавлению пользователей в экземпляр DevTest Labs.

Чтобы добавить пользователя в лабораторию, вы добавляете пользователя к роли **пользователя DevTest Labs** для лаборатории. В этой статье показано, как автоматизировать добавление пользователя в лабораторию одним из следующих способов:

- Шаблоны Azure Resource Manager
- Командлеты Azure PowerShell 
- Azure CLI.

## <a name="use-azure-resource-manager-templates"></a>Использование шаблонов Azure Resource Manager
Следующий пример шаблона менеджер ресурсов определяет пользователя, который будет добавлен в **роль пользователя DevTest Labs** в лаборатории. 

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

Если вы назначаете роль в том же шаблоне, который создает лабораторию, не забудьте добавить зависимость между ресурсом назначения ролей и лабораторией. Для получения дополнительной информации смотрите в статье [«Определение зависимостей» в статье шаблонов «Менеджер ресурсов Azure».](../azure-resource-manager/templates/define-resource-dependency.md)

### <a name="role-assignment-resource-information"></a>Информация о ресурсе назначения ролей
Ресурсу назначения ролей необходимо указать тип и имя.

Первое, что нужно отметить, что тип `Microsoft.Authorization/roleAssignments` для ресурса не так, как это было бы для группы ресурсов.  Вместо этого тип ресурса `{provider-namespace}/{resource-type}/providers/roleAssignments`следует шаблону. В этом случае тип ресурса будет `Microsoft.DevTestLab/labs/providers/roleAssignments`.

Само имя назначения роли должно быть глобально уникальным.  Название назначения использует шаблон. `{labName}/Microsoft.Authorization/{newGuid}` Значение `newGuid` параметра для шаблона. Это гарантирует, что имя назначения роли является уникальным. Поскольку нет функций шаблона для создания GUID, необходимо создать GUID самостоятельно, используя любой инструмент генератора GUID.  

В шаблоне имя назначения роли определяется `fullDevTestLabUserRoleName` переменной. Точная строка из шаблона:

```json
"fullDevTestLabUserRoleName": "[concat(parameters('labName'), '/Microsoft.Authorization/', parameters('roleAssignmentGuid'))]"
```


### <a name="role-assignment-resource-properties"></a>Свойства ресурсов назначения ролей
Само назначение роли определяет три свойства. Он нуждается `principalId`в `scope` `roleDefinitionId`, и .

### <a name="role-definition"></a>Определение роли
Идентификатор определения роли является идентификатором строки для существующего определения роли. Идентификатор роли `/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}`находится в форме. 

Идентификатор подписки `subscription().subscriptionId` получен с помощью функции шаблона.  

Необходимо получить определение роли для `DevTest Labs User` встроенной роли. Чтобы получить GUID для роли [пользователя DevTest Labs,](../role-based-access-control/built-in-roles.md#devtest-labs-user) вы можете использовать [API за назначения ролей REST](/rest/api/authorization/roleassignments) или cmdlet [Get-AzRoleDefinition.](/powershell/module/az.resources/get-azroledefinition?view=azps-1.8.0)

```powershell
$dtlUserRoleDefId = (Get-AzRoleDefinition -Name "DevTest Labs User").Id
```

Идентификатор роли определяется в `devTestLabUserRoleId`разделе переменных и назван. В шаблоне идентификатор роли установлен по телефону: 111111111-0000-0000-1111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111 

```json
"devTestLabUserRoleId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/111111111-0000-0000-11111111111111111')]",
```

### <a name="principal-id"></a>Идентификатор субъекта
Основной идентификатор — это идентификатор объекта пользователя, группы или службы Active Directory, который необходимо добавить в лабораторию в качестве лабораторного пользователя. Шаблон использует `ObjectId` в качестве параметра.

Вы можете получить ObjectId с помощью [Get-AzureRMADUser](/powershell/module/azurerm.resources/get-azurermaduser?view=azurermps-6.13.0), «Get-AzureRMADGroup» или [Get-AzureRMADServicePrincipal](/powershell/module/azurerm.resources/get-azurermadserviceprincipal?view=azurermps-6.13.0) PowerShell cmdlets. Эти cmdlets возвращают одиночный или списки объектов Active Directory, которые имеют свойство идентификатора, которое является идентификатором объекта, который вам нужен. Ниже приводится следующий пример, как получить идентификатор объекта одного пользователя в компании.

```powershell
$userObjectId = (Get-AzureRmADUser -UserPrincipalName ‘email@company.com').Id
```

Вы также можете использовать Azure Active Directory PowerShell cmdlets, которые включают [Get-MsolUser,](/powershell/module/msonline/get-msoluser?view=azureadps-1.0) [Get-MsolGroup](/powershell/module/msonline/get-msolgroup?view=azureadps-1.0)и [Get-MsolServicePrincipal](/powershell/module/msonline/get-msolserviceprincipal?view=azureadps-1.0).

### <a name="scope"></a>Область
Область определяет ресурс или группу ресурсов, для которой должно применяться назначение роли. Для ресурсов область действия находится `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{provider-namespace}/{resource-type}/{resource-name}`в форме: . Шаблон использует `subscription().subscriptionId` функцию для `subscription-id` заполнения детали `resourceGroup().name` и функции `resource-group-name` шаблона для заполнения детали. Использование этих функций означает, что лаборатория, которой вы назначаете роль, должна существовать в текущей подписке и той же группе ресурсов, в которой производится развертывание шаблона. Последняя часть, `resource-name`это название лаборатории. Это значение получено через параметр шаблона в этом примере. 

Область роли в шаблоне: 

```json
"roleScope": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.DevTestLab/labs/', parameters('labName'))]"
```

### <a name="deploying-the-template"></a>Развертывание шаблона
Во-первых, создайте файл параметров (например: azuredeploy.parameters.json), который передает значения для параметров в шаблоне менеджера ресурсов. 

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

Затем используйте [new-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment?view=azurermps-6.13.0) PowerShell cmdlet для развертывания шаблона менеджера ресурсов. В следующем примере команда присваивает человеку, группе или директору службы роль пользователя DevTest Labs для лаборатории.

```powershell
New-AzureRmResourceGroupDeployment -Name "MyLabResourceGroup-$(New-Guid)" -ResourceGroupName 'MyLabResourceGroup' -TemplateParameterFile .\azuredeploy.parameters.json -TemplateFile .\azuredeploy.json
```

Важно отметить, что имя развертывания группы и назначение ролей GUID должны быть уникальными. Если вы попытаетесь развернуть назначение ресурса с неуникальным GUID, то вы получите ошибку. `RoleAssignmentUpdateNotPermitted`

Если вы планируете использовать шаблон несколько раз, чтобы добавить несколько объектов Active Directory в роль пользователя DevTest Labs для вашей лаборатории, рассмотрите возможность использования динамических объектов в команде PowerShell. В следующем примере используется [cmdlet New-Guid](/powershell/module/Microsoft.PowerShell.Utility/New-Guid?view=powershell-5.0) для динамического указания имени развертывания группы ресурсов и назначения ролей GUID.

```powershell
New-AzureRmResourceGroupDeployment -Name "MyLabResourceGroup-$(New-Guid)" -ResourceGroupName 'MyLabResourceGroup' -TemplateFile .\azuredeploy.json -roleAssignmentGuid "$(New-Guid)" -labName "MyLab" -principalId "11111111-1111-1111-1111-111111111111"
```

## <a name="use-azure-powershell"></a>Использование Azure PowerShell
Как уже говорилось во введении, создается новое назначение ролей Azure для добавления пользователя в роль **пользователя DevTest Labs** для лаборатории. В PowerShell вы делаете это с помощью cmdlet [New-AzureRMRoleAssignment.](/powershell/module/azurerm.resources/new-azurermroleassignment?view=azurermps-6.13.0) Этот cmdlet имеет много дополнительных параметров, чтобы обеспечить гибкость. , `ObjectId` `SigninName`или `ServicePrincipalName` может быть указан как объект, выдаваемый разрешений.  

Вот пример команды Azure PowerShell, которая добавляет пользователя к роли пользователя DevTest Labs в указанной лаборатории.

```powershell
New-AzureRmRoleAssignment -UserPrincipalName <email@company.com> -RoleDefinitionName 'DevTest Labs User' -ResourceName '<Lab Name>' -ResourceGroupName '<Resource Group Name>' -ResourceType 'Microsoft.DevTestLab/labs'
```

`ResourceName`Указать ресурс, на который предоставляются разрешения, `ResourceType` `ResourceGroup` можно указать комбинацией `scope` или параметром. Независимо от того, какое сочетание параметров используется, предоставьте достаточно информации cmdlet, чтобы однозначно определить объект Active Directory (пользователь, группа или основной сервис), область (группа ресурсов или ресурс) и определение роли.

## <a name="use-azure-command-line-interface-cli"></a>Используйте интерфейс командной строки Azure (CLI)
В Azure CLI добавление пользователя лабораторий в лабораторию `az role assignment create` выполняется с помощью команды. Для получения дополнительной информации о cmdlets Azure CLI [см.](../role-based-access-control/role-assignments-cli.md)

Объект, доступ к нему предоставляется, `objectId` `signInName`может `spn` быть указан по параметрам, установленным. Лаборатория, к которой предоставляется доступ к объекту, может быть `resource-type`идентифицирована `resource-group` по `scope` URL или комбинации , и параметров. `resource-name`

Следующий пример Azure CLI показывает, как добавить человека к роли пользователя DevTest Labs для указанной лаборатории.  

```azurecli
az role assignment create --roleName "DevTest Labs User" --signInName <email@company.com> -–resource-name "<Lab Name>" --resource-type “Microsoft.DevTestLab/labs" --resource-group "<Resource Group Name>"
```

## <a name="next-steps"></a>Дальнейшие действия
См. следующие статьи:

- [Создание виртуальных машин и управление ими с DevTest Labs с использованием Azure CLI](devtest-lab-vmcli.md)
- [Создайте виртуальную машину с DevTest Labs с помощью Azure PowerShell](devtest-lab-vm-powershell.md)
- [Используйте инструменты командной строки для запуска и остановки виртуальных машин Azure DevTest Labs](use-command-line-start-stop-virtual-machines.md)

