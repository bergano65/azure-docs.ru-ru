---
title: Выходы в шаблонах
description: Описывает, как определить значения вывода в шаблоне управления ресурсами Azure.
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: 203bfc66e9515ef14a5fe1315ef5b9ee07075041
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460030"
---
# <a name="outputs-in-azure-resource-manager-template"></a>Выходы в шаблоне менеджера ресурсов Azure

В этой статье описывается, как определить значения выходных данных в шаблоне Azure Resource Manager. Выможете использовать результаты, когда необходимо вернуть значения из развернутых ресурсов.

## <a name="define-output-values"></a>Определение значений вывода

В следующем примере показано, как вернуть идентификатор ресурса для общедоступного IP-адреса.

```json
"outputs": {
  "resourceID": {
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

## <a name="conditional-output"></a>Условный выход

В разделе выводов можно условно вернуть значение. Обычно условие используется в выводах при [условном развертывании](conditional-resource-deployment.md) ресурса. В следующем примере показано, как условно вернуть идентификатор ресурса для общедоступного IP-адреса в зависимости от того, был ли развернут новый:

```json
"outputs": {
  "resourceID": {
    "condition": "[equals(parameters('publicIpNewOrExisting'), 'new')]",
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

Для простого примера условного вывода [см.](https://github.com/bmoore-msft/AzureRM-Samples/blob/master/conditional-output/azuredeploy.json)

## <a name="dynamic-number-of-outputs"></a>Динамическое количество выходов

В некоторых сценариях вы не знаете количество экземпляров значения, необходимого для возврата при создании шаблона. Вы можете вернуть переменное количество значений с помощью элемента **копирования.**

```json
"outputs": {
  "storageEndpoints": {
    "type": "array",
    "copy": {
      "count": "[parameters('storageCount')]",
      "input": "[reference(concat(copyIndex(), variables('baseName'))).primaryEndpoints.blob]"
    }
  }
}
```

Для получения дополнительной информации смотрите [итерацию выходов в шаблонах Azure Resource Manager.](copy-outputs.md)

## <a name="linked-templates"></a>Связанные шаблоны

Чтобы получить значение вывода из связанного шаблона, используйте [функцию ссылки](template-functions-resource.md#reference) в шаблоне родительского вывода. Синтаксис в шаблоне родительского составляет:

```json
"[reference('<deploymentName>').outputs.<propertyName>.value]"
```

При получении выходного свойства из связанного шаблона имя свойства не должно содержать тире.

В следующем примере показано, как установить IP-адрес на балансере нагрузки, извлекая значение из связанного шаблона.

```json
"publicIPAddress": {
  "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

Вы не можете использовать функцию `reference` в разделе выходных данных [вложенного шаблона](linked-templates.md#nested-template). Чтобы извлечь значения для развернутого ресурса во вложенном шаблоне, преобразуйте этот шаблон в связанный.

## <a name="get-output-values"></a>Получить значения вывода

При успешном развертывании значения вывода автоматически возвращаются в результатах развертывания.

Чтобы получить значения вывода из истории развертывания, можно использовать сценарий.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
(Get-AzResourceGroupDeployment `
  -ResourceGroupName <resource-group-name> `
  -Name <deployment-name>).Outputs.resourceID.value
```

# <a name="azure-cli"></a>[Лазурный CLI](#tab/azure-cli)

```azurecli-interactive
az deployment group show \
  -g <resource-group-name> \
  -n <deployment-name> \
  --query properties.outputs.resourceID.value
```

---

## <a name="example-templates"></a>Образцы шаблонов

В следующих примерах показаны сценарии использования выходных данных.

|Шаблон  |Описание  |
|---------|---------|
|[Копирование переменных](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) | Позволяет создать сложные переменные и вывести эти значения. Не используется для развертывания ресурсов. |
|[Общественный IP-адрес](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) | Позволяет создать общедоступный IP-адрес и вывести идентификатор ресурса. |
|[Балансизатор нагрузки](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) | Позволяет создать ссылку на предыдущий шаблон. При создании подсистемы балансировки нагрузки использует идентификатор ресурса в выходных данных. |

## <a name="next-steps"></a>Дальнейшие действия

* Чтобы узнать о доступных свойствах для выводов, [см. Понимание структуры и синтаксиса шаблонов Azure Resource Manager.](template-syntax.md)
