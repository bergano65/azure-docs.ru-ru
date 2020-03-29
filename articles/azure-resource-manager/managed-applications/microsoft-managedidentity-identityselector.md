---
title: Элемент uI IdentitySelector
description: Описывает элемент uI Microsoft.ManagedIdentity.IdentitySelector для портала Azure. Используется для присвоения управляемым идентификаторам ресурсу.
author: tfitzmac
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tomfitz
ms.openlocfilehash: cb66a2684e0b83f4f0cc01a07cc724f6beab4d68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77087548"
---
# <a name="microsoftmanagedidentityidentityselector-ui-element"></a>Элемент uI Microsoft.ManagedIdentity.IdentitySelector

Контроль для присвоения [управляемых идентификаторов](../../active-directory/managed-identities-azure-resources/overview.md) для ресурса в развертывании.

## <a name="ui-sample"></a>Пример элемента пользовательского интерфейса

Элементы управления состоят из следующих элементов:

![Microsoft.ManagedIdentity.IdentitySelector первый шаг](./media/managed-application-elements/microsoft.managedidentity.identityselector1.png)

Когда пользователь выбирает **Добавить,** следующая форма открывается. Пользователь может выбрать один или несколько пользовательских идентификаторов для ресурса.

![Microsoft.ManagedIdentity.IdentitySelector второй шаг](./media/managed-application-elements/microsoft.managedidentity.identityselector2.png)

Выбранные идентификаторы отображаются в таблице. Пользователь может добавлять или удалять элементы из этой таблицы.

![Microsoft.ManagedIdentity.IdentitySelector третий шаг](./media/managed-application-elements/microsoft.managedidentity.identityselector3.png)

## <a name="schema"></a>схема

```json
{
  "name": "identity",
  "type": "Microsoft.ManagedIdentity.IdentitySelector",
  "label": "Managed Identity Configuration",
  "toolTip": {
    "systemAssignedIdentity": "Enable system assigned identity to grant the resource access to other existing resources.",
    "userAssignedIdentity": "Add user assigned identities to grant the resource access to other existing resources."
  },
  "defaultValue": {
    "systemAssignedIdentity": "Off"
  },
  "options": {
    "hideSystemAssignedIdentity": false,
    "hideUserAssignedIdentity": false
  },
  "visible": true
}
```

## <a name="sample-output"></a>Пример выходных данных

```json
{
  "identity": {
    "value": {
      "type": "UserAssigned",
      "userAssignedIdentities": {
        "/subscriptions/xxxx/resourceGroups/TestResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/TestUserIdentity1": {}
      }
    }
  }
}
```

## <a name="remarks"></a>Remarks

- Используйте **defaultValue.systemAssignedIdentity** для установки начального значения для системы, назначенной контролем параметров идентификации. Значение по умолчанию **выключено.** Разрешены следующие значения:
  - **On** - К ресурсу присваивается система, назначенная идентификация.
  - **Off** - Система, назначенная идентификация не назначена ресурсу.
  - **OnOnly** - К ресурсу присваивается система, назначенная идентификационным данным. Пользователи не могут отсеивать это значение во время развертывания.
  - **OffOnly** - Система, назначенная идентификация не присваивается ресурсу. Пользователи не могут отсеивать это значение во время развертывания.

- Если **options.hideSystemAssignedIdentity** установлен к **истине,** то UI для настройки установленной идентификации системы не отображается. Значение по умолчанию для этой опции **является ложным.**
- Если **options.hideUserAssignedidentity** установлен в **истину,** пользовательский интерфейс для настройки назначенного пользователя удостоверения не отображается. Ресурсне присваивается удостоверению, назначенному пользователю. Значение по умолчанию для этой опции **является ложным.**

## <a name="next-steps"></a>Дальнейшие действия

- Общие сведения о создании определений пользовательского интерфейса см. в статье [Начало работы с CreateUiDefinition](create-uidefinition-overview.md).
- Дополнительные сведения об общих свойствах элементов пользовательского интерфейса см. в статье [Элементы CreateUiDefinition](create-uidefinition-elements.md).