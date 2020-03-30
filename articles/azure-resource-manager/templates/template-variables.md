---
title: Переменные в шаблонах
description: Описывает, как определить переменные в шаблоне управления ресурсами Azure.
ms.topic: conceptual
ms.date: 09/05/2019
ms.openlocfilehash: cf135959d30702ea58b7a1d4fdd82625a39245d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75483821"
---
# <a name="variables-in-azure-resource-manager-template"></a>Переменные в шаблоне менеджера ресурсов Azure

В этой статье описывается, как определить и использовать переменные в шаблоне Azure Resource Manager. Для упрощения шаблона используются переменные. Вместо того, чтобы повторять сложные выражения по всему шаблону, вы определяете переменную, которая содержит сложное выражение. Затем вы ссылаетесь на эту переменную по мере необходимости на протяжении всего шаблона.

Диспетчер ресурсов решает переменные перед началом операций развертывания. Везде, где переменная используется в шаблоне, менеджер ресурсов заменяет ее на разрешенное значение.

## <a name="define-variable"></a>Определение переменной

В приведенном ниже примере показано определение переменной. В нем создается строковое значение имени учетной записи хранения. Он использует несколько функций шаблона, чтобы получить значение параметра, и переплетает его на уникальную строку.

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

Вы не можете использовать [функцию ссылки](template-functions-resource.md#reference) или любую из функций [списка](template-functions-resource.md#list) в разделе переменных. Эти функции получают состояние времени выполнения ресурса и не могут быть выполнены до развертывания при развертывании переменных.

## <a name="use-variable"></a>Использование переменной

В шаблоне вы ссылаетесь на значение параметра с помощью функции [переменных.](template-functions-deployment.md#variables) В следующем примере показано, как использовать переменную для свойства ресурса.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageName')]",
    ...
  }
]
```

## <a name="configuration-variables"></a>Переменные конфигурации

Можно определить переменные, которые удерживают связанные значения для настройки среды. Вы определяете переменную как объект со значениями. В следующем примере показан объект, который содержит значения для двух сред - **теста** и **prod.**

```json
"variables": {
  "environmentSettings": {
    "test": {
      "instanceSize": "Small",
      "instanceCount": 1
    },
    "prod": {
      "instanceSize": "Large",
      "instanceCount": 4
    }
  }
},
```

В параметрах следует создать значение, указывающее, какие значения конфигурации будут использоваться.

```json
"parameters": {
  "environmentName": {
    "type": "string",
    "allowedValues": [
      "test",
      "prod"
    ]
  }
},
```

Для получения параметров для указанной среды используйте переменную и параметр вместе.

```json
"[variables('environmentSettings')[parameters('environmentName')].instanceSize]"
```

## <a name="example-templates"></a>Образцы шаблонов

В следующих примерах показаны сценарии использования переменных.

|Шаблон  |Описание  |
|---------|---------|
| [variable definitions](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variables.json) | Демонстрирует различные типы переменных. Шаблон не развертывает ресурсы. При его помощи только создаются и возвращаются значения переменных. |
| [configuration variable](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variablesconfigurations.json) | Демонстрирует использование переменной, определяющей значения конфигурации. Шаблон не развертывает ресурсы. При его помощи только создаются и возвращаются значения переменных. |
| [network security rules](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) и [parameter file](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json) | Отвечает за создание массива в правильном формате для назначения правил безопасности в группе безопасности сети. |

## <a name="next-steps"></a>Дальнейшие действия

* Чтобы узнать о доступных свойствах для переменных, [см. Понимание структуры и синтаксиса шаблонов Azure Resource Manager.](template-syntax.md)
* Рекомендации по созданию [Best practices - variables](template-best-practices.md#variables)переменных см.
