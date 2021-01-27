---
title: Переменные в шаблонах
description: Описывает определение переменных в шаблоне Azure Resource Manager (шаблон ARM).
ms.topic: conceptual
ms.date: 01/26/2021
ms.openlocfilehash: feecc4b5df77e6a3bf51294cb12aabf44899dde5
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98874440"
---
# <a name="variables-in-arm-template"></a>Переменные в шаблоне ARM

В этой статье описывается определение и использование переменных в шаблоне Azure Resource Manager (шаблон ARM). Для упрощения шаблона используются переменные. Вместо повторения сложных выражений во всем шаблоне определяется переменная, которая содержит сложное выражение. Затем вы ссылаетесь на эту переменную по мере необходимости во всем шаблоне.

Диспетчер ресурсов разрешает переменные перед началом операций развертывания. Когда в шаблоне используется переменная, Resource Manager заменяет ее разрешенным значением.

## <a name="define-variable"></a>Определение переменной

При определении переменной укажите значение или выражение шаблона, которое разрешается в [тип данных](template-syntax.md#data-types). При построении переменной можно использовать значение из параметра или другой переменной.

В объявлении переменной можно использовать [функции шаблонов](template-functions.md) , но нельзя использовать функцию [Reference](template-functions-resource.md#reference) или любые функции [списка](template-functions-resource.md#list) . Эти функции получают состояние среды выполнения ресурса и не могут быть выполнены до развертывания при разрешении переменных.

В приведенном ниже примере показано определение переменной. В нем создается строковое значение имени учетной записи хранения. Он использует несколько функций шаблона для получения значения параметра и объединяет его в уникальную строку.

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

## <a name="use-variable"></a>Использование переменной

В шаблоне ссылка на значение параметра осуществляется с помощью функции [Variables](template-functions-deployment.md#variables) . В следующем примере показано, как использовать переменную для свойства ресурса.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageName')]",
    ...
  }
]
```

## <a name="example-template"></a>Пример шаблона

Следующий шаблон не развертывает ресурсы. В нем просто показаны некоторые способы объявления переменных.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/variables.json":::

## <a name="configuration-variables"></a>Переменные конфигурации

Можно определить переменные, которые содержат связанные значения для настройки среды. Переменная определяется как объект со значениями. В следующем примере показан объект, содержащий значения для двух сред — **Test** и **произ**. Вы передаете одно из этих значений во время развертывания.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/variablesconfigurations.json":::

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения о доступных свойствах переменных см. в разделе Общие сведения о [структуре и синтаксисе шаблонов ARM](template-syntax.md).
* Рекомендации по созданию переменных см. в разделе рекомендации [— переменные](template-best-practices.md#variables).
* Пример шаблона, который назначает правила безопасности для группы безопасности сети, см. в разделе [правила сетевой безопасности](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) и [файл параметров](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json).
