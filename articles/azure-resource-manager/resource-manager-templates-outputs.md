---
title: Выходные данные шаблона Azure Resource Manager | Документация Майкрософт
description: В этой статье объясняется, как определить выходные данные шаблонов Azure Resource Manager с помощью декларативного синтаксиса JSON.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/04/2019
ms.author: tomfitz
ms.openlocfilehash: aadc92c232d32d827644caa52b3c362d9c8d4c9b
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/04/2019
ms.locfileid: "55691037"
---
# <a name="outputs-section-in-azure-resource-manager-templates"></a>Раздел выходных данных в шаблонах Azure Resource Manager

В разделе выходных данных следует указать значения, которые возвращаются после развертывания. Например, можно возвращать URI для доступа к развернутому ресурсу.

## <a name="define-and-use-output-values"></a>Определение и использование выходных значений

В следующем примере показано, как вернуть идентификатор ресурса для общедоступного IP-адреса.

```json
"outputs": {
  "resourceID": {
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

После развертывания вы можете извлечь значение с помощью скрипта. Для PowerShell используйте команду:

```powershell
(Get-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -Name <deployment-name>).Outputs.resourceID.value
```

Для интерфейса командной строки Azure:

```azurecli-interactive
az group deployment show -g <resource-group-name> -n <deployment-name> --query properties.outputs.resourceID.value
```

Вы можете получить выходное значение из связанного шаблона с помощью функции [reference](resource-group-template-functions-resource.md#reference). Чтобы получить выходные значения из связанного шаблона, извлеките значение свойства с синтаксисом, например: `"[reference('deploymentName').outputs.propertyName.value]"`.

При получении выходного свойства из связанного шаблона имя свойства не должно содержать тире.

Например, можно задать IP-адрес в подсистеме балансировки нагрузки, получив значение из связанного шаблона.

```json
"publicIPAddress": {
    "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

Вы не можете использовать функцию `reference` в разделе выходных данных [вложенного шаблона](resource-group-linked-templates.md#link-or-nest-a-template). Чтобы извлечь значения для развернутого ресурса во вложенном шаблоне, преобразуйте этот шаблон в связанный.

## <a name="available-properties"></a>Доступные свойства

В следующем примере показана структура определения выходных данных:

```json
"outputs": {
    "<outputName>" : {
        "type" : "<type-of-output-value>",
        "value": "<output-value-expression>"
    }
}
```

| Имя элемента | Обязательно | ОПИСАНИЕ |
|:--- |:--- |:--- |
| outputName |Yes |Имя выходного значения. Должно быть допустимым идентификатором JavaScript. |
| Тип |Yes |Тип выходного значения. Выходные значения поддерживает те же типы, что и входные параметры шаблона. |
| value |Yes |Выражение на языке шаблона, которое вычисляется и возвращается в качестве выходного значения. |

Дополнительные сведения о добавлении комментариев см. в разделе [о комментариях в шаблонах](resource-group-authoring-templates.md#comments).

## <a name="example-templates"></a>Образцы шаблонов

|Шаблон  |ОПИСАНИЕ  |
|---------|---------|
|[Копирование переменных](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) | Позволяет создать сложные переменные и вывести эти значения. Не используется для развертывания ресурсов. |
|[Общедоступный IP-адрес](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) | Позволяет создать общедоступный IP-адрес и вывести идентификатор ресурса. |
|[Подсистема балансировки нагрузки](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) | Позволяет создать ссылку на предыдущий шаблон. При создании подсистемы балансировки нагрузки использует идентификатор ресурса в выходных данных. |


## <a name="next-steps"></a>Дополнительная информация
* Полные шаблоны для различных типов решений доступны на странице [Шаблоны быстрого запуска Azure](https://azure.microsoft.com/documentation/templates/).
* Дополнительные сведения о функциях, которые можно использовать в шаблонах, см. в статье [Функции шаблонов Azure Resource Manager](resource-group-template-functions.md).
* Дополнительные рекомендации по созданию шаблонов см. в статье [Рекомендации по работе с шаблонами Azure Resource Manager](template-best-practices.md).
