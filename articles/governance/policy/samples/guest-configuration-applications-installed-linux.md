---
title: Пример. Выполнение аудита в случае, если приложения не установлены на виртуальных машинах Linux
description: Этот пример инициативы и определений гостевой конфигурации Политики проверяет, установлены ли указанные приложения на виртуальных машинах Linux.
author: DCtheGeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
ms.date: 03/18/2019
ms.author: dacoulte
ms.openlocfilehash: b432d8557c4244d58c23e7b068874dd747f6249f
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59256470"
---
# <a name="sample---audit-if-specified-applications-are-not-installed-inside-linux-vms"></a>Пример. Выполнение аудита в случае, если заданные приложения не установлены на виртуальных машинах Linux

Эта инициатива гостевой конфигурации Политики проверяет, установлено ли указанное приложение на виртуальных машинах Linux. Идентификатор данной встроенной инициативы: `/providers/Microsoft.Authorization/policySetDefinitions/c937dcb4-4398-4b39-8d63-4a6be432252e`.

> [!IMPORTANT]
> Все инициативы гостевой конфигурации состоят из определений политики **audit** и **deployIfNotExists** . Назначение только одного из определений политики приводит к неправильной работе гостевой конфигурации.

Вы можете назначить этот пример с помощью таких средств:

- [портал Azure](#azure-portal).
- [Azure PowerShell](#azure-powershell)

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="components-of-the-initiative"></a>Компоненты инициативы

Инициатива [гостевой конфигурации](../concepts/guest-configuration.md) состоит из следующих политик:

- [audit](#audit-definition) — проверка, что приложение установлено на виртуальные машины Linux.
  - ID: `/providers/Microsoft.Authorization/policyDefinitions/fee5cb2b-9d9b-410e-afe3-2902d90d0004`
- [deployIfNotExists](#deployIfNotExists-definition) — развертывание расширения виртуальной машины для проверки, что приложение установлено на виртуальные машины Linux.
  - ID: `/providers/Microsoft.Authorization/policyDefinitions/4d1c04de-2172-403f-901b-90608c35c721`

### <a name="initiative-definition"></a>Определение инициативы

Инициатива создается путем объединения определений **audit** и **deployIfNotExists** с [параметрами инициативы](#initiative-parameters). Ниже приведен код JSON определения.

[!code-json[initiative-definition](../../../../policy-templates/samples/GuestConfiguration/installed-application-linux/azurepolicyset.json "Initiative definition (JSON)")]

### <a name="initiative-parameters"></a>Параметры инициативы

|Имя |Тип ||Описание | |---|---||---| |applicationName | Строка | Имена приложений. Например, 'python', 'powershell' или список через запятую ('python, powershell'). Используйте символ \* для сопоставления с подстановочными знаками, например 'power\*'.|

При создании задания через PowerShell или Azure CLI значения параметров могут передаваться как JSON или в строке, или через файл с использованием `-PolicyParameter` (PowerShell) или `--params` (Azure CLI).
PowerShell также поддерживает `-PolicyParameterObject`, требующий передать командлету хэш-таблицу имен и значений, где **Имя** является именем параметра, а **значение** — отдельным значением или массивом значений, передаваемых во время назначения.

В этом примере параметра проверяется установка приложений _python_ и _powershell_.

```json
{
    "applicationName": {
        "value": "python,powershell"
    }
}
```

Только определение политики **deployIfNotExists** использует параметры инициативы.

### <a name="audit-definition"></a>Определение audit

Код JSON, определяющий правила определения политики **audit**.

[!code-json[audit-definition](../../../../policy-templates/samples/GuestConfiguration/installed-application-linux/audit/azurepolicy.rules.json "audit policy rules (JSON)")]

### <a name="deployifnotexists-definition"></a>Определение deployIfNotExists

Код JSON, определяющий правила определения политики **deployIfNotExists**.

[!code-json[deployIfNotExists-definition](../../../../policy-templates/samples/GuestConfiguration/installed-application-linux/deployIfNotExists/azurepolicy.rules.json "deployIfNotExists policy rules (JSON)")]

Определение политики **deployIfNotExists** определяет образы Azure, в которых была проверена политика:

|ИЗДАТЕЛЬ |ПРЕДЛОЖЕНИЕ |SKU |
|-|-|-|
|OpenLogic |CentOS\* |Все, кроме 6\* |
|RedHat |RHEL |Все, кроме 6\* |
|RedHat |osa | Все |
|credativ |Debian | Все, кроме 7\* |
|SUSE |SLES\* |Все, кроме 11\* |
|Canonical| UbuntuServer |Все, кроме 12\* |
|microsoft-dsvm |linux-data-science-vm-ubuntu |Все |
|microsoft-dsvm |azureml |Все |
|cloudera |cloudera-centos-os |Все, кроме 6\* |
|cloudera |cloudera-altus-centos-os |Все |
|microsoft-ads |linux\* |Все |
|microsoft-aks |Все |Все |
|AzureDatabricks |Все |Все |
|qubole-inc |Все |Все |
|datastax |Все |Все |
|couchbase |Все |Все |
|scalegrid |Все |Все |
|checkpoint |Все |Все |
|paloaltonetworks |Все |Все |

Часть **deployment** правила передает параметр _installedApplication_ агенту гостевой конфигурации на виртуальной машине. Эта конфигурация позволяет агенту выполнять проверки и отправлять отчет о соответствии обратно через определение политики **audit**.

## <a name="azure-portal"></a>Портал Azure

После создания определений **audit** и **deployIfNotExists** на портале мы советуем объединить их в [инициативу](../concepts/definition-structure.md#initiatives) для назначения.

### <a name="create-copy-of-audit-definition"></a>Создание копии определения audit

[![Развертывание примера политики в Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FGuestConfiguration%2Finstalled-application-linux%2Faudit%2Fazurepolicy.json)
[![Deploy the Policy sample to Azure Gov](https://docs.microsoft.com/azure/governance/policy/media/deploy/deployGovbutton.png)](https://portal.azure.us/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FGuestConfiguration%2Finstalled-application-linux%2Faudit%2Fazurepolicy.json)

При нажатии кнопок развертывания с помощью портала будет создана копия определения политики **audit**.
Без парного определения политики **deployIfNotExists** гостевая конфигурация будет работать неправильно.

### <a name="create-copy-of-deployifnotexists-definition"></a>Создание копии определения deployIfNotExists

[![Развертывание примера политики в Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FGuestConfiguration%2Finstalled-application-linux%2FdeployIfNotExists%2Fazurepolicy.json)
[![Deploy the Policy sample to Azure Gov](https://docs.microsoft.com/azure/governance/policy/media/deploy/deployGovbutton.png)](https://portal.azure.us/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FGuestConfiguration%2Finstalled-application-linux%2FdeployIfNotExists%2Fazurepolicy.json)

При нажатии кнопок развертывания с помощью портала будет создана копия определения политики **deployIfNotExists**. Без парного определения политики **audit** гостевая конфигурация будет работать неправильно.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

### <a name="deploy-with-azure-powershell"></a>Развертывание с помощью Azure PowerShell

#### <a name="copy-and-assign-the-initiative"></a>Копирование и назначение инициативы

Приведенные ниже команды создают копию инициативы, которая включает встроенные политики для определений **audit** и **deployIfNotExists**, а также назначает инициативу группе ресурсов.

```azurepowershell-interactive
# Create the policy initiative (Subscription scope)
$initDef = New-AzPolicySetDefinition -Name 'guestconfig-installed-application-linux' -DisplayName 'GuestConfig - Audit that an application is installed inside Linux VMs' -description 'This initiative will both deploy the policy requirements and audit that the specified application is installed inside Linux virtual machines.' -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/GuestConfiguration/installed-application-linux/azurepolicyset.definitions.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/GuestConfiguration/installed-application-linux/azurepolicyset.parameters.json' -Mode All

# Set the scope to a resource group; may also be a resource, subscription, or management group
$scope = Get-AzResourceGroup -Name 'YourResourceGroup'

# Set the initiative parameter (JSON format)
$initParam = '{ "applicationName": { "value": "python,powershell" } }'

# Create the initiative assignment
$assignment = New-AzPolicyAssignment -Name 'guestconfig-installed-application-linux-assignment' -DisplayName 'GuestConfig - Python and PowerShell apps on Linux' -Scope $scope.ResourceID -PolicySetDefinition $initDef -PolicyParameter $initParam -AssignIdentity -Location 'westus2'

# Get the system-assigned managed identity created by the assignment with -AssignIdentity
$saIdentity = $assignment.Identity.principalId

# Give the system-assigned managed identity the 'Contributor' role on the scope (needed by deployIfNotExists)
$roleAssignment = New-AzRoleAssignment -ObjectId $saIdentity -Scope $scope.ResourceId -RoleDefinitionName 'Contributor'
```

Чтобы удалить предыдущее назначение и определение, выполните следующую команду.

```azurepowershell-interactive
# Remove the initiative assignment
Remove-AzPolicyAssignment -Id $assignment.ResourceId

# Remove the 'Contributor' role from the system-assigned managed identity
Remove-AzRoleAssignment -ObjectId $saIdentity -Scope $scope.ResourceId -RoleDefinitionName 'Contributor'

# Remove the initiative definition
Remove-AzPolicySetDefinition -Id $initDef
```

#### <a name="copy-and-assign-the-audit-definition"></a>Копирование и назначение определения audit

Приведенные ниже команды создают копию определения **audit** и назначают ее группе ресурсов. Это определение будет работать неправильно без назначения парного определения **deployIfNotExists**.

```azurepowershell-interactive
# Create the policy definition (Subscription scope)
$definition = New-AzPolicyDefinition -Name 'guestconfig-installed-application-linux-audit' -DisplayName 'GuestConfig - Audit that an application is installed inside Linux VMs' -description 'This policy audits that the specified application is installed inside Linux virtual machines. This policy should only be used along with its corresponding deploy policy in an initiative/policy set.' -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/GuestConfiguration/installed-application-linux/audit/azurepolicy.rules.json' -Mode All

# Set the scope to a resource group; may also be a resource, subscription, or management group
$scope = Get-AzResourceGroup -Name 'YourResourceGroup'

# Create the policy assignment
$assignment = New-AzPolicyAssignment -Name 'guestconfig-installed-application-linux-audit-assignment' -DisplayName 'GuestConfig - Python and PowerShell apps on Linux' -Scope $scope.ResourceID -PolicyDefinition $definition
```

Чтобы удалить предыдущее назначение и определение, выполните следующую команду.

```azurepowershell-interactive
# Remove the policy definition
Remove-AzPolicyAssignment -Id $assignment.ResourceId

# Remove the policy definition
Remove-AzPolicyDefinition -Id $definition
```

#### <a name="copy-and-assign-the-deployifnotexists-definition"></a>Копирование и назначение определения deployIfNotExists

Приведенные ниже команды создают копию определения **deployIfNotExists** и назначают ее группе ресурсов.
Это определение будет работать неправильно без назначения парного определения **audit**.

```azurepowershell-interactive
# Create the policy definition (Subscription scope)
$definition = New-AzPolicyDefinition -Name 'guestconfig-installed-application-linux-deployIfNotExists' -DisplayName 'GuestConfig - Deploy VM extension to audit that an application is installed inside Linux VMs' -description 'Include this rule to deploy the VM extension for Microsoft Guest Configuration, the VM extension for Microsoft Azure Managed Service Identity, and the content required to audit that an application is installed inside Linux virtual machines. This policy should only be used along with its corresponding audit policy in an initiative/policy set.' -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/GuestConfiguration/installed-application-linux/deployIfNotExists/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/GuestConfiguration/installed-application-linux/deployIfNotExists/azurepolicy.parameters.json' -Mode All

# Set the scope to a resource group; may also be a resource, subscription, or management group
$scope = Get-AzResourceGroup -Name 'YourResourceGroup'

# Set the definition parameter (JSON format)
$policyParam  = '{ "applicationName": { "value": "python,powershell" } }'

# Create the policy assignment
$assignment = New-AzPolicyAssignment -Name 'guestconfig-installed-application-linux-deployIfNotExists-assignment' -DisplayName 'GuestConfig - Deploy VM extension to audit that Python and PowerShell are installed inside Linux VMs' -Scope $scope.ResourceID -PolicyDefinition $definition -PolicyParameter $policyParam -AssignIdentity -Location 'westus2'

# Get the system-assigned managed identity created by the assignment with -AssignIdentity
$saIdentity = $assignment.Identity.principalId

# Give the system-assigned managed identity the 'Contributor' role on the scope (needed by deployIfNotExists)
$roleAssignment = New-AzRoleAssignment -ObjectId $saIdentity -Scope $scope.ResourceId -RoleDefinitionName 'Contributor'
```

Чтобы удалить предыдущее назначение и определение, выполните следующую команду.

```azurepowershell-interactive
# Remove the policy assignment
Remove-AzPolicyAssignment -Id $assignment.ResourceId

# Remove the 'Contributor' role from the system-assigned managed identity
Remove-AzRoleAssignment -ObjectId $saIdentity -Scope $scope.ResourceId -RoleDefinitionName 'Contributor'

# Remove the policy definition
Remove-AzPolicyDefinition -Id $definition
```

### <a name="azure-powershell-explanation"></a>Пояснение Azure PowerShell

Чтобы развернуть и удалить скрипты используйте следующие команды. Для каждой команды в следующей таблице приведены ссылки на соответствующую документацию:

| Команда | Примечания |
|---|---|
| [New-AzPolicySetDefinition](/powershell/module/az.resources/New-AzPolicySetDefinition) | Создание инициативы Политики Azure. |
| [New-AzPolicyDefinition](/powershell/module/az.resources/New-AzPolicyDefinition) | Создание определения Политики Azure. |
| [Get-AzResourceGroup](/powershell/module/az.resources/Get-AzResourceGroup) | Получение одной группы ресурсов. |
| [New-AzPolicyAssignment](/powershell/module/az.resources/New-AzPolicyAssignment) | Создание назначения Политики Azure для инициативы или определения. |
| [New-AzRoleAssignment](/powershell/module/az.resources/New-AzRoleAssignment) | Присвоение имеющегося назначения роли конкретному субъекту. |
| [Remove-AzPolicyAssignment](/powershell/module/az.resources/Remove-AzPolicyAssignment) | Удаление существующего назначения политики Azure. |
| [Remove-AzPolicySetDefinition](/powershell/module/az.resources/Remove-AzPolicySetDefinition) | Удаление инициативы. |
| [Remove-AzPolicyDefinition](/powershell/module/az.resources/Remove-AzPolicyDefinition) | Удаление определения. |

## <a name="next-steps"></a>Дополнительная информация

- Просмотрите дополнительные [примеры для Политики Azure](index.md).
- Ознакомьтесь с дополнительными сведениями о [гостевой конфигурации Политики Azure](../concepts/guest-configuration.md).
- Изучите статью о [структуре определения Политики Azure](../concepts/definition-structure.md).
