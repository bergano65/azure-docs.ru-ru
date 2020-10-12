---
title: Создание & удаление назначенного пользователем управляемого удостоверения с помощью Azure Resource Manager
description: Пошаговые инструкции по созданию и удалению назначенных пользователем управляемых удостоверений с помощью Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2019
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd49c60a23a0ad58f0907d3c1d7c38178ac08c97
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "89266363"
---
# <a name="create-list-and-delete-a-user-assigned-managed-identity-using-azure-resource-manager"></a>Создание и удаление управляемых удостоверений, назначаемых пользователем, а также получение их списка с помощью Azure Resource Manager


Управляемые удостоверения для ресурсов Azure предоставляют службы Azure с управляемым удостоверением в Azure Active Directory. Это удостоверение можно использовать для аутентификации в службах, которые поддерживают аутентификацию Azure AD, без учетных данных в коде. 

В этой статье вы узнаете, как создать управляемое удостоверение, назначаемое пользователем, с помощью Azure Resource Manager.

С помощью шаблона Azure Resource Manager невозможно получить список управляемых удостоверений, назначаемых пользователем, или удалить их.  Создание управляемых удостоверений, назначаемых пользователем, и получение их списка описывается в следующих статьях:

- [Отображение управляемого удостоверения, назначаемого пользователем](how-to-manage-ua-identity-cli.md#list-user-assigned-managed-identities)
- [Удаление управляемого удостоверения, назначаемого пользователем](how-to-manage-ua-identity-cli.md#delete-a-user-assigned-managed-identity)
  ## <a name="prerequisites"></a>Предварительные требования

- Если вы не работали с управляемыми удостоверениями для ресурсов Azure, изучите [общие сведения](overview.md). **Обратите внимание на [различие между управляемыми удостоверениями, назначаемыми системой и назначаемыми пользователями](overview.md#managed-identity-types)**.
- Если у вас нет учетной записи Azure, [зарегистрируйтесь для получения бесплатной пробной учетной записи](https://azure.microsoft.com/free/), прежде чем продолжать.

## <a name="template-creation-and-editing"></a>Создание и редактирование шаблона

Как портал Azure и сценарии, шаблоны Azure Resource Manager предоставляют возможность развертывать новые и измененные ресурсы, определенные группой ресурсов Azure. Доступно несколько способов редактирования и развертывания шаблона, локально и на портале, в том числе:

- Применение [пользовательского шаблона из Azure Marketplace](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template), что позволяет создать шаблон с нуля или взять за основу существующий общий шаблон или [шаблон быстрого запуска](https://azure.microsoft.com/documentation/templates/).
- Наследование от имеющейся группы ресурсов путем экспорта шаблона из [исходного развертывания](../../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates) или от [текущего состояния развертывания](../../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates).
- Использование локального [редактора JSON (например, VS Code)](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md), а затем передача и развертывание с помощью PowerShell или интерфейса командной строки.
- Использование [проекта группы ресурсов Azure](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md) Visual Studio для создания и развертывания шаблона. 

## <a name="create-a-user-assigned-managed-identity"></a>Создание управляемого удостоверения, назначаемого пользователем 

Чтобы создать назначаемое пользователем управляемое удостоверение, учетной записи должна быть назначена роль [участника управляемого удостоверения](../../role-based-access-control/built-in-roles.md#managed-identity-contributor).

Для создания управляемого удостоверения, назначаемого пользователем, используйте следующий шаблон. Замените `<USER ASSIGNED IDENTITY NAME>` собственным значением.

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "resourceName": {
          "type": "string",
          "metadata": {
            "description": "<USER ASSIGNED IDENTITY NAME>"
          }
        }
  },
  "resources": [
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "name": "[parameters('resourceName')]",
      "apiVersion": "2018-11-30",
      "location": "[resourceGroup().location]"
    }
  ],
  "outputs": {
      "identityName": {
          "type": "string",
          "value": "[parameters('resourceName')]"
      }
  }
}
```
## <a name="next-steps"></a>Дальнейшие шаги

Сведения о том, как задать управляемое удостоверение, назначаемое пользователем, для виртуальной машины Azure с помощью шаблона Azure Resource Manager, см. в статье [Настройка управляемых удостоверений для ресурсов Azure на виртуальной машине Azure с помощью шаблонов](qs-configure-template-windows-vm.md).


