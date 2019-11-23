---
title: Концепция блокировки ресурсов
description: Learn about the locking options in Azure Blueprints to protect resources when assigning a blueprint.
ms.date: 04/24/2019
ms.topic: conceptual
ms.openlocfilehash: 50f506cc57f67ca2ae2b07e342750d6c5099e739
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406406"
---
# <a name="understand-resource-locking-in-azure-blueprints"></a>Общие сведения о блокировке ресурсов в Azure Blueprint

Создавать крупные согласованные среды имеет смысл только при наличии механизма, поддерживающего эту согласованность. В этой статье рассказывается, как работает блокировка ресурсов в Azure Blueprint. To see an example of resource locking and application of _deny assignments_, see the [protecting new resources](../tutorials/protect-new-resources.md) tutorial.

## <a name="locking-modes-and-states"></a>Режимы и состояния блокировки

Locking Mode applies to the blueprint assignment and it has three options: **Don't Lock**, **Read Only**, or **Do Not Delete**. Режим блокировки настраивается во время развертывания артефактов при назначении схемы. Вы можете задать другой режим блокировки, обновив назначение схемы.
Тем не менее режим блокировки нельзя изменить вне службы Blueprints.

Resources created by artifacts in a blueprint assignment have four states: **Not Locked**, **Read Only**, **Cannot Edit / Delete**, or **Cannot Delete**. Каждый тип артефакта может находиться в состоянии **Не заблокировано**. Следующую таблицу можно использовать для определения состояния ресурса:

|Mode|Тип ресурса артефакта|Состояние|Описание|
|-|-|-|-|
|Не блокировать|*|Не заблокировано|Ресурсы не защищены службой Blueprints. Это состояние также используется для ресурсов, добавленных к артефакту группы ресурсов **Только чтение** или **Do Not Delete** (Не удалять) за пределами назначения схемы.|
|Только чтение|группа ресурсов.|Невозможно изменить/удалить|Группа ресурсов доступна только для чтения, и теги в этой группе недоступны для изменения. Ресурсы с состоянием **Не заблокировано** можно добавлять, перемещать, изменять или удалять из этой группы ресурсов.|
|Только чтение|Нересурсная группа|Только чтение|Ресурс невозможно изменить или удалить каким-либо образом.|
|Не удалять|*|Cannot Delete (Невозможно удалить)|Ресурсы можно изменить, но невозможно удалить. Ресурсы с состоянием **Не заблокировано** можно добавлять, перемещать, изменять или удалять из этой группы ресурсов.|

## <a name="overriding-locking-states"></a>Переопределение состояний блокировки

Обычно сохраняется возможность предоставить права на изменение и (или) удаление любого ресурса владельцу соответствующих прав [управления доступом на основе ролей](../../../role-based-access-control/overview.md) (RBAC) в подписке. Но эта возможность недоступна, если схема применяет блокировку в составе развернутого назначения. Если назначение было установлено с состоянием **Только чтение** или **Do Not Delete** (Не удалять), даже владелец подписки не сможет выполнять заблокированное действие для защищенного ресурса.

Эта мера предосторожности защищает согласованность определенной схемы и среды, для создания которой эта схема предназначается, от случайного или программного удаления либо изменения.

## <a name="removing-locking-states"></a>Удаление состояний блокировки

Если нужно изменить или удалить ресурс, защищенный назначением, это можно сделать двумя способами.

- Установить для назначения схемы режим блокировки с состоянием **Не блокировать**.
- Удалить назначение схемы.

После удаления назначения созданные схемами блокировки удаляются. Но при этом сам ресурс сохраняется. Его следует удалить обычными средствами.

## <a name="how-blueprint-locks-work"></a>Как работают блокировки схемы

Действие [запрета назначений](../../../role-based-access-control/deny-assignments.md) RBAC применяется к ресурсам артефактов во время назначения схемы, если для назначения выбрано состояние **Только чтение** или **Do Not Delete** (Не удалять). Запрещающее действие добавляется управляемым удостоверением назначения схемы и может быть удалено из ресурсов артефактов только тем же управляемым удостоверением. Эта мера предосторожности обеспечивает механизм блокировки и предотвращает попытки удалить блокировку схемы за пределами схемы.

![Blueprint deny assignment on resource group](../media/resource-locking/blueprint-deny-assignment.png)

The [deny assignment properties](../../../role-based-access-control/deny-assignments.md#deny-assignment-properties) of each mode are as follows:

|Mode |Permissions.Actions |Permissions.NotActions |Principals[i].Type |ExcludePrincipals[i].Id | DoNotApplyToChildScopes |
|-|-|-|-|-|-|
|Только чтение |**\*** |**\*/read** |SystemDefined (Everyone) |blueprint assignment and user-defined in **excludedPrincipals** |Resource group - _true_; Resource - _false_ |
|Не удалять |**\*/delete** | |SystemDefined (Everyone) |blueprint assignment and user-defined in **excludedPrincipals** |Resource group - _true_; Resource - _false_ |

> [!IMPORTANT]
> Azure Resource Manager кэширует сведения о назначении роли на срок до 30 минут. Это означает, что действие запрета назначений для ресурсов схемы может не сразу вступать в полную силу. В течение этого периода сохраняется возможность удалить ресурс, который должен быть защищен блокировками схемы.

## <a name="exclude-a-principal-from-a-deny-assignment"></a>Exclude a principal from a deny assignment

In some design or security scenarios, it may be necessary to exclude a principal from the [deny assignment](../../../role-based-access-control/deny-assignments.md) the blueprint assignment creates. This is done in REST API by adding up to five values to the **excludedPrincipals** array in the **locks** property when [creating the assignment](/rest/api/blueprints/assignments/createorupdate). This is an example of a request body that includes **excludedPrincipals**:

```json
{
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "eastus",
  "properties": {
    "description": "enforce pre-defined simpleBlueprint to this XXXXXXXX subscription.",
    "blueprintId": "/providers/Microsoft.Management/managementGroups/{mgId}/providers/Microsoft.Blueprint/blueprints/simpleBlueprint",
    "locks": {
        "mode": "AllResourcesDoNotDelete",
        "excludedPrincipals": [
            "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
            "38833b56-194d-420b-90ce-cff578296714"
        ]
    },
    "parameters": {
      "storageAccountType": {
        "value": "Standard_LRS"
      },
      "costCenter": {
        "value": "Contoso/Online/Shopping/Production"
      },
      "owners": {
        "value": [
          "johnDoe@contoso.com",
          "johnsteam@contoso.com"
        ]
      }
    },
    "resourceGroups": {
      "storageRG": {
        "name": "defaultRG",
        "location": "eastus"
      }
    }
  }
}
```

## <a name="next-steps"></a>Дальнейшие действия

- Follow the [protect new resources](../tutorials/protect-new-resources.md) tutorial.
- Ознакомьтесь со сведениями о [жизненном цикле схем](lifecycle.md).
- Узнайте, как использовать [статические и динамические параметры](parameters.md).
- Научитесь настраивать [последовательность схемы](sequencing-order.md).
- Узнайте, как [обновлять существующие назначения](../how-to/update-existing-assignments.md).
- Устраняйте проблемы, возникающие во время назначения схемы, с помощью [общих инструкций по устранению неполадок](../troubleshoot/general.md).