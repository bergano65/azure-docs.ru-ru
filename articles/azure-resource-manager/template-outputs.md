---
title: Выходные данные в шаблоне Azure Resource Manager
description: Описывает, как определить выходные значения в шаблоне Azure Resource Manager.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: tomfitz
ms.openlocfilehash: 3805e0bb86772836ba4a1c91661477f29d5e0f70
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/05/2019
ms.locfileid: "70384051"
---
# <a name="outputs-in-azure-resource-manager-template"></a>Выходные данные в шаблоне Azure Resource Manager

В этой статье описывается, как определить выходные значения в шаблоне Azure Resource Manager. Выходные данные используются при необходимости возврата значений из развернутых ресурсов.

## <a name="define-output-values"></a>Определение выходных значений

В следующем примере показано, как вернуть идентификатор ресурса для общедоступного IP-адреса.

```json
"outputs": {
  "resourceID": {
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

## <a name="conditional-output"></a>Условный вывод

В разделе Outputs можно условно возвращать значение. Как правило, условие используется в выходных данных при [условном развертывании](conditional-resource-deployment.md) ресурса. В следующем примере показано, как условно вернуть идентификатор ресурса для общедоступного IP-адреса в зависимости от того, был ли развернут новый.

```json
"outputs": {
  "resourceID": {
    "condition": "[equals(parameters('publicIpNewOrExisting'), 'new')]",
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

Простой пример условного вывода см. в разделе [шаблон условного вывода](https://github.com/bmoore-msft/AzureRM-Samples/blob/master/conditional-output/azuredeploy.json).

## <a name="linked-templates"></a>Связанные шаблоны

Чтобы получить выходное значение из связанного шаблона, используйте функцию [Reference](resource-group-template-functions-resource.md#reference) в родительском шаблоне. В родительском шаблоне используется следующий синтаксис:

```json
"[reference('<deploymentName>').outputs.<propertyName>.value]"
```

При получении выходного свойства из связанного шаблона имя свойства не должно содержать тире.

В следующем примере показано, как задать IP-адрес в подсистеме балансировки нагрузки, извлекая значение из связанного шаблона.

```json
"publicIPAddress": {
  "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

Вы не можете использовать функцию `reference` в разделе выходных данных [вложенного шаблона](resource-group-linked-templates.md#link-or-nest-a-template). Чтобы извлечь значения для развернутого ресурса во вложенном шаблоне, преобразуйте этот шаблон в связанный.

## <a name="get-output-values"></a>Получение выходных значений

После завершения развертывания выходные значения автоматически возвращаются в результатах развертывания.

Чтобы получить выходные значения из журнала развертывания, можно использовать скрипт.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
(Get-AzResourceGroupDeployment `
  -ResourceGroupName <resource-group-name> `
  -Name <deployment-name>).Outputs.resourceID.value
```

# <a name="azure-clitabazure-cli"></a>[Интерфейс командной строки Azure](#tab/azure-cli)

```azurecli-interactive
az group deployment show \
  -g <resource-group-name> \
  -n <deployment-name> \
  --query properties.outputs.resourceID.value
```

---

## <a name="example-templates"></a>Образцы шаблонов

В следующих примерах демонстрируются сценарии использования выходных данных.

|Шаблон  |Описание  |
|---------|---------|
|[Копирование переменных](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) | Позволяет создать сложные переменные и вывести эти значения. Не используется для развертывания ресурсов. |
|[Общедоступный IP-адрес](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) | Позволяет создать общедоступный IP-адрес и вывести идентификатор ресурса. |
|[Подсистема балансировки нагрузки](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) | Позволяет создать ссылку на предыдущий шаблон. При создании подсистемы балансировки нагрузки использует идентификатор ресурса в выходных данных. |

## <a name="next-steps"></a>Следующие шаги

* Дополнительные сведения о доступных свойствах выходных данных см. в разделе Общие сведения о [структуре и синтаксисе шаблонов Azure Resource Manager](resource-group-authoring-templates.md).
* Рекомендации по созданию выходов см. в разделе "рекомендации [-выходные данные](template-best-practices.md#outputs)".
