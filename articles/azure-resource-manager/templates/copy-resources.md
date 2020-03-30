---
title: Развертывание нескольких экземпляров ресурсов
description: Используйте операции копирования и массивы в шаблоне менеджера ресурсов Azure для развертывания типа ресурсов много раз.
ms.topic: conceptual
ms.date: 09/27/2019
ms.openlocfilehash: e65ab93c21daffa0053e53d953fe95fa9f28e2a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153324"
---
# <a name="resource-iteration-in-arm-templates"></a>Итерация ресурсов в шаблонах ARM

В этой статье показано, как создать несколько экземпляров ресурса в шаблоне Azure Resource Manager (ARM). Добавляя элемент **копирования** в раздел ресурсов шаблона, можно динамически установить количество развертываемых ресурсов. Вы также избежать необходимости повторять шаблон синтаксиса.

Вы также можете использовать копию со [свойствами,](copy-properties.md) [переменными](copy-variables.md) и [выходами.](copy-outputs.md)

Если вам нужно указать, развернут ли ресурс, см. описание [элемента condition](conditional-resource-deployment.md).

## <a name="resource-iteration"></a>Итерация ресурса

Элемент копирования имеет следующий общий формат:

```json
"copy": {
  "name": "<name-of-loop>",
  "count": <number-of-iterations>,
  "mode": "serial" <or> "parallel",
  "batchSize": <number-to-deploy-serially>
}
```

Свойство **имени** — это любое значение, идентифицируя цикл. Свойство **подсчета** определяет количество итераций, которые вы хотите для типа ресурса.

Используйте **свойства режима** и **batchSize,** чтобы указать, если ресурсы развернуты параллельно или последовательно. Эти свойства описаны в [Serial или Parallel](#serial-or-parallel).

В следующем примере создается количество учетных записей хранилища, указанных в параметре **storageCount.**

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageCount": {
            "type": "int",
            "defaultValue": 2
        }
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": "[parameters('storageCount')]"
            }
        }
    ],
    "outputs": {}
}
```

Обратите внимание, что имя каждого ресурса содержит функцию `copyIndex()`, которая возвращает текущую итерацию в цикле. `copyIndex()` отсчитывается, начиная с нуля. См. приведенный ниже пример.

```json
"name": "[concat('storage', copyIndex())]",
```

Он создает следующие имена:

* storage0;
* storage1;
* storage2.

Чтобы сместить значение индекса, можно передать нужное значение в функцию copyIndex(). Количество итераций по-прежнему указывается в элементе копирования, но значение copyIndex компенсируется указанным значением. См. приведенный ниже пример.

```json
"name": "[concat('storage', copyIndex(1))]",
```

Он создает следующие имена:

* storage1;
* storage2;
* storage3.

Операция копирования удобна при работе с массивами, так как позволяет выполнить итерацию по каждому элементу в массиве. Используйте функцию `length` в массиве, чтобы указать число итераций, а также функцию `copyIndex` для получения текущего индекса в массиве.

Следующий пример создает одну учетную запись для хранения каждого имени, предусмотренного в параметре.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "storageNames": {
          "type": "array",
          "defaultValue": [
            "contoso",
            "fabrikam",
            "coho"
          ]
      }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(parameters('storageNames')[copyIndex()], uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": "[length(parameters('storageNames'))]"
      }
    }
  ],
  "outputs": {}
}
```

Если требуется вернуть значения из развернутых ресурсов, можно использовать [копию в разделе выводов.](copy-outputs.md)

## <a name="serial-or-parallel"></a>Серийный или параллельный

По умолчанию Resource Manager создает ресурсы параллельно. Он не применяет никаких ограничений на количество ресурсов, развертываемых параллельно, за исключением общего лимита в 800 ресурсов в шаблоне. Порядок, в котором они создаются, не гарантируется.

Тем не менее можно настроить последовательное развертывание ресурсов. Например, при обновлении рабочей среды может потребоваться дифференцировать обновления, чтобы только определенное число элементов могло быть обновлено в любой момент времени. Чтобы последовательно развернуть несколько экземпляров ресурса, задайте параметру `mode` значение **serial**, а в качестве значения `batchSize` введите число экземпляров, которое необходимо развернуть за раз. В последовательном режиме Resource Manager создает зависимость от предыдущих экземпляров в цикле, поэтому он не начинает выполнение следующего пакета до завершения предыдущего.

Например, чтобы последовательно развернуть две учетные записи хранения за раз, используйте следующую команду:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "copy": {
        "name": "storagecopy",
        "count": 4,
        "mode": "serial",
        "batchSize": 2
      },
      "properties": {}
    }
  ],
  "outputs": {}
}
```

Свойство mode также принимает значение **parallel**, которое является значением по умолчанию.

## <a name="depend-on-resources-in-a-loop"></a>Зависимость от ресурсов в цикле

С помощью элемента `dependsOn` можно определить последовательность развертывания ресурсов. Чтобы развернуть ресурс, который зависит от коллекции ресурсов в цикле, укажите имя цикла копирования в элементе dependsOn. В следующем примере показано, как развернуть три учетные записи хранения перед развертыванием виртуальной машины. Полное определение виртуальной машины не отображается. Обратите внимание, что элемент `storagecopy` копирования имеет имя, установленное и `storagecopy`элемент dependsOn для виртуальной машины также установлен на .

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "copy": {
        "name": "storagecopy",
        "count": 3
      },
      "properties": {}
    },
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2015-06-15",
      "name": "[concat('VM', uniqueString(resourceGroup().id))]",
      "dependsOn": ["storagecopy"],
      ...
    }
  ],
  "outputs": {}
}
```

## <a name="iteration-for-a-child-resource"></a>Итерация дочерних ресурсов

Нельзя включить дочерний ресурс в цикл копирования. Чтобы создать несколько экземпляров ресурса, которые определяются как вложенные в другой ресурс, необходимо создать его как ресурс верхнего уровня. Вы можете определить связь с родительским ресурсом с помощью свойств type и name.

Предположим, вы определяете набор данных как дочерний ресурс фабрики данных.

```json
"resources": [
{
  "type": "Microsoft.DataFactory/datafactories",
  "name": "exampleDataFactory",
  ...
  "resources": [
    {
      "type": "datasets",
      "name": "exampleDataSet",
      "dependsOn": [
        "exampleDataFactory"
      ],
      ...
    }
  ]
```

Чтобы создать несколько наборов данных, переместите его за пределы фабрики данных. Набор данных должен находиться на том же уровне, что и фабрика данных, но при этом он должен быть дочерним ресурсом фабрики данных. Вы можете сохранить связь между набором данных и фабрикой данных с помощью свойств type и name. Так как тип нельзя определить по положению в шаблоне, укажите полное имя типа в следующем формате: `{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}`.

Чтобы установить связь между родительским и дочерним ресурсами, укажите имя набора данных, которое включает имя родительского ресурса. Используйте формат `{parent-resource-name}/{child-resource-name}`.

В следующем примере показано, как это сделать:

```json
"resources": [
{
  "type": "Microsoft.DataFactory/datafactories",
  "name": "exampleDataFactory",
  ...
},
{
  "type": "Microsoft.DataFactory/datafactories/datasets",
  "name": "[concat('exampleDataFactory', '/', 'exampleDataSet', copyIndex())]",
  "dependsOn": [
    "exampleDataFactory"
  ],
  "copy": {
    "name": "datasetcopy",
    "count": "3"
  },
  ...
}]
```

## <a name="copy-limits"></a>Ограничения копирования

Количество не может превышать 800.

Подсчет не может быть отрицательным числом. Если вы развернете шаблон с Azure PowerShell 2.6 или позже, Azure CLI 2.0.74 или позже, или rest API версии **2019-05-10** или позже, можно установить значение до нуля. Более ранние версии PowerShell, CLI и REST API не поддерживают ноль для подсчета.

Будьте осторожны, используя [полное развертывание режима](deployment-modes.md) с копией. При передислокации в полный режим в группу ресурсов удаляются любые ресурсы, которые не указаны в шаблоне после разрешения цикла копирования.

## <a name="example-templates"></a>Образцы шаблонов

Ниже приведены примеры распространенных сценариев для создания нескольких экземпляров ресурса или свойства.

|Шаблон  |Описание  |
|---------|---------|
|[Копирования хранилища](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystorage.json) |Развертывает несколько учетных записей хранения с номером индекса в имени. |
|[Последовательное копирование хранилища](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/serialcopystorage.json) |Последовательно развертывает несколько учетных записей хранения. Имя содержит номер индекса. |
|[Копирования хранилища с массивом](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystoragewitharray.json) |Развертывает несколько учетных записей хранения. Имя содержит значение из массива. |
|[Развертывание виртуальной машины с переменным количеством дисков данных](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |Развертывает несколько дисков данных с виртуальной машиной. |
|[Несколько правил безопасности](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) |Развертывает несколько правил безопасности в группу безопасности сети. Кроме того, этот шаблон создает правила безопасности на основе параметра. Чтобы узнать параметр, см. [файл параметров нескольких групп безопасности сети](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json). |

## <a name="next-steps"></a>Дальнейшие действия

* Чтобы пройти через учебник, [см. Tutorial: создайте несколько экземпляров ресурсов с помощью шаблонов ARM.](template-tutorial-create-multiple-instances.md)
* Для других видов использования элемента копирования см.:
  * [Итерация свойств в шаблонах ARM](copy-properties.md)
  * [Переменная итерация в шаблонах ARM](copy-variables.md)
  * [Итерация вывода в шаблонах ARM](copy-outputs.md)
* Для получения информации об использовании копии с вложенными шаблонами, [см.](linked-templates.md#using-copy)
* Если вы хотите узнать о разделах шаблона, [см.](template-syntax.md)
* Чтобы узнать, как развернуть шаблон, [см. Развертывание приложения с шаблоном ARM.](deploy-powershell.md)

