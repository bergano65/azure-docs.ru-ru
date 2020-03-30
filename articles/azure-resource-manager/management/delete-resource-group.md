---
title: Удаление группы ресурсов и ресурсов
description: Описывает, как удалить группы ресурсов и ресурсы. В нем описывается, как менеджер ресурсов Azure заказывает удаление ресурсов при удалении группы ресурсов. В этой статье описываются коды отклика и объясняется, как Resource Manager обрабатывает их для определения, успешно ли выполнено удаление.
ms.topic: conceptual
ms.date: 09/03/2019
ms.custom: seodec18
ms.openlocfilehash: db56cf0897cd90f1e6e51199032d0d9712530f1c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274025"
---
# <a name="azure-resource-manager-resource-group-and-resource-deletion"></a>Группа ресурсов ресурсов Azure и удаление ресурсов

В этой статье показано, как удалять группы ресурсов и ресурсы. В нем описывается, как менеджер ресурсов Azure заказывает удаление ресурсов при удалении группы ресурсов.

## <a name="how-order-of-deletion-is-determined"></a>Как определяется порядок удаления

При удалении группы ресурсов Resource Manager определяет порядок удаления ресурсов. Он использует следующий порядок:

1. Удаляются все дочерние (вложенные) элементы.

2. Затем удаляются ресурсы, которые управляют другими ресурсами. Для ресурса может быть задано свойство `managedBy`, указывающее, что этим ресурсов управляет другой ресурс. Если это свойство задано, ресурс, который управляет каким-либо ресурсом, удаляется перед другими ресурсами.

3. Остальные ресурсы будут удалены после предыдущих двух категорий.

После определения порядка Resource Manager вызывает операции удаления для каждого ресурса. Он ожидает выполнения всех зависимостей, прежде чем продолжить.

Для синхронных операций ожидаемыми успешными кодами отклика являются:

* 200
* 204
* 404

Для асинхронных операций ожидаемый успешный ответ — 202. Resource Manager отслеживает заголовок location или заголовок асинхронной операции azure-async, чтобы определить состояние асинхронной операции удаления.
  
### <a name="deletion-errors"></a>Ошибки удаления

Когда операция удаления возвращает ошибку, Resource Manager повторяет попытки вызова команды DELETE. Повторные попытки происходят для кодов состояния 5xx, 429 и 408. По умолчанию интервал времени между повторными попытками составляет 15 минут.

## <a name="after-deletion"></a>После удаления

Resource Manager отправляет вызов GET для каждого ресурса, который требовалось удалить. Ожидаемый ответ на вызов GET — 404. Когда Resource Manager получает ответ 404, он считает удаление успешно выполненным. Resource Manager также удаляет ресурс из кэша.

Однако, если вызов GET для ресурса возвращает ответ 200 или 201, Resource Manager повторно создает ресурс.

Если операция GET возвращает ошибку, Resource Manager повторяет операцию GET для следующего кода ошибки:

* меньше 100;
* 408
* 429
* больше 500.

Для других кодов ошибок Resource Manager не выполняет повторных попыток, и удаление ресурса завершается сбоем.

## <a name="delete-resource-group"></a>Удалить группу ресурсов

Используйте один из следующих методов для удаления группы ресурсов.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Remove-AzResourceGroup -Name ExampleResourceGroup
```

# <a name="azure-cli"></a>[Лазурный CLI](#tab/azure-cli)

```azurecli-interactive
az group delete --name ExampleResourceGroup
```

# <a name="portal"></a>[Портал](#tab/azure-portal)

1. На [портале](https://portal.azure.com)выберите группу ресурсов, которые необходимо удалить.

1. Выберите **Удалить группу ресурсов**.

   ![Удалить группу ресурсов](./media/delete-resource-group/delete-group.png)

1. Чтобы подтвердить удаление, введите имя группы ресурсов

---

## <a name="delete-resource"></a>Удалить ресурс

Используйте один из следующих методов для удаления ресурса.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Remove-AzResource `
  -ResourceGroupName ExampleResourceGroup `
  -ResourceName ExampleVM `
  -ResourceType Microsoft.Compute/virtualMachines
```

# <a name="azure-cli"></a>[Лазурный CLI](#tab/azure-cli)

```azurecli-interactive
az resource delete \
  --resource-group ExampleResourceGroup \
  --name ExampleVM \
  --resource-type "Microsoft.Compute/virtualMachines"
```

# <a name="portal"></a>[Портал](#tab/azure-portal)

1. На [портале](https://portal.azure.com)выберите ресурс, который вы хотите удалить.

1. Выберите **Удалить**. На следующем скриншоте показаны варианты управления виртуальной машиной.

   ![Удалить ресурс](./media/delete-resource-group/delete-resource.png)

1. При появлении запроса подтвердите удаление.

---


## <a name="next-steps"></a>Дальнейшие действия

* Основные понятия Azure Resource Manager см. в [этой статье](overview.md).
* Команды удаления см. в разделах [PowerShell](/powershell/module/az.resources/Remove-AzResourceGroup), [Azure CLI](/cli/azure/group?view=azure-cli-latest#az-group-delete) и [REST API](/rest/api/resources/resourcegroups/delete).
