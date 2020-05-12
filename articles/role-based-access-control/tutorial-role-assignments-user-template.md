---
title: Руководство по Предоставление пользователям доступа к ресурсам Azure с помощью шаблона Azure Resource Manager — Azure RBAC
description: Узнайте, как предоставить пользователям доступ к ресурсам Azure с помощью шаблона Azure Resource Manager и управления доступом на основе ролей Azure (Azure RBAC).
services: role-based-access-control,azure-resource-manager
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 05/15/2019
ms.author: rolyon
ms.openlocfilehash: f98b1e9c57cf9f624c5af3e59c8afd56d073dd28
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/03/2020
ms.locfileid: "82735476"
---
# <a name="tutorial-grant-a-user-access-to-azure-resources-using-an-azure-resource-manager-template"></a>Руководство по Предоставление пользователям доступа к ресурсам Azure с помощью шаблона Azure Resource Manager

[Управление доступом на основе ролей Azure (Azure RBAC)](overview.md) — это способ управления доступом к ресурсам в Azure. В этом учебнике объясняется, как создать группу ресурсов и предоставить пользователю доступ к созданию и администрированию виртуальных машин в группе ресурсов. Кроме того, здесь рассматривается процесс развертывания шаблона Resource Manager для предоставления доступа. Дополнительные сведения о разработке шаблонов Resource Manager см. в разделе [Документация по Azure Resource Manager](/azure/azure-resource-manager/) и в [справочнике по шаблонам](/azure/templates/microsoft.authorization/allversions
).

В этом руководстве описано следующее:

> [!div class="checklist"]
> * Предоставление доступа пользователю в области действия группы ресурсов
> * Проверка развертывания
> * Очистка

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Для добавления и удаления назначений ролей требуются разрешения:

* `Microsoft.Authorization/roleAssignments/write` и `Microsoft.Authorization/roleAssignments/delete`, такие как [Администратор доступа пользователей](built-in-roles.md#user-access-administrator) или [Владелец](built-in-roles.md#owner).

## <a name="grant-access"></a>Предоставление доступа

Шаблон, используемый в этом кратком руководстве, взят из [шаблонов быстрого запуска Azure](https://azure.microsoft.com/resources/templates/101-rbac-builtinrole-resourcegroup/). Дополнительные сведения о связанных шаблонах авторизации Azure можно найти [здесь](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization).

Для развертывания шаблона выберите **Попробовать**, чтобы открыть Azure Cloud Shell, а затем вставьте следующий сценарий PowerShell в окно оболочки. Чтобы вставить код, щелкните окно оболочки правой кнопкой мыши, а затем выберите **Вставить**.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used to generate Azure resource names"
$emailAddress = Read-Host -Prompt "Enter your email address that is associated with your Azure subscription (used to find the principal ID)"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

$resourceGroupName = "${projectName}rg"
$roleAssignmentName = New-Guid
$principalId = (Get-AzAdUser -Mail $emailAddress).id
$roleDefinitionId = (Get-AzRoleDefinition -name "Virtual Machine Contributor").id
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-rbac-builtinrole-resourcegroup/azuredeploy.json"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -roleAssignmentName $roleAssignmentName -roleDefinitionID $roleDefinitionId -principalId $principalId
```

## <a name="validate-the-deployment"></a>Проверка развертывания

1. Войдите на [портал Azure](https://portal.azure.com).
1. Откройте группу ресурсов, созданную в предыдущей процедуре. Имя по умолчанию — имя проекта с добавлением **rg**.
1. В меню слева выберите **Управление доступом (IAM)** .
1. Выберите **Назначения ролей**. 
1. В поле **Имя** введите адрес электронной почты, использованный в предыдущей процедуре. Должен появиться пользователь с адресом электронной почты и ролью **Участник виртуальных машин**.

## <a name="clean-up"></a>Очистка

Для удаления группы ресурсов, созданной в рамках предыдущей процедуры, выберите **Попробовать**, чтобы открыть Azure Cloud Shell, а затем вставьте следующий сценарий PowerShell в окно оболочки.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a same project name you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Руководство. Предоставление доступа пользователям к ресурсам Azure с помощью Azure PowerShell](tutorial-role-assignments-user-powershell.md)