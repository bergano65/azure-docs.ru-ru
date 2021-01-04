---
title: Задание порядка развертывания для ресурсов
description: Описание настройки одного ресурса в зависимости от другого ресурса во время развертывания. Зависимости гарантируют развертывание ресурсов в правильном порядке.
ms.topic: conceptual
ms.date: 12/21/2020
ms.openlocfilehash: a96dca0ab30d0baee2688427d78867ea128e673a
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/22/2020
ms.locfileid: "97722017"
---
# <a name="define-the-order-for-deploying-resources-in-arm-templates"></a>Определение порядка развертывания ресурсов в шаблонах ARM

При развертывании ресурсов может потребоваться убедиться, что некоторые ресурсы существуют до других ресурсов. Например, для развертывания базы данных требуется логический сервер SQL Server. Вы устанавливаете эту связь, помечая один ресурс как зависимый от другого ресурса. Используйте элемент **dependsOn** для определения явной зависимости. Используйте функции **Reference** или **List** для определения неявной зависимости.

Диспетчер ресурсов оценивает зависимости между ресурсами и развертывает эти ресурсы согласно установленным зависимостям. Если ресурсы не зависят друг от друга, диспетчер ресурсов развертывает их параллельно. Необходимо только определить зависимости для ресурсов, которые развертываются в том же шаблоне.

## <a name="dependson"></a>Свойство dependsOn

В шаблоне элемент dependsOn позволяет определить один ресурс как зависимый от одного или нескольких ресурсов. Его значение — это массив JSON строк, каждый из которых является именем ресурса или ИДЕНТИФИКАТОРом. Массив может содержать ресурсы, которые [условно развернуты](conditional-resource-deployment.md). Если условный ресурс не развернут, Azure Resource Manager автоматически удаляет его из необходимых зависимостей.

В следующем примере показан сетевой интерфейс, который зависит от виртуальной сети, группы безопасности сети и общедоступного IP-адреса. Полный шаблон см. [в шаблоне быстрого запуска для виртуальной машины Linux](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-simple-linux/azuredeploy.json).

```json
{
    "type": "Microsoft.Network/networkInterfaces",
    "apiVersion": "2020-06-01",
    "name": "[variables('networkInterfaceName')]",
    "location": "[parameters('location')]",
    "dependsOn": [
      "[resourceId('Microsoft.Network/networkSecurityGroups/', parameters('networkSecurityGroupName'))]",
      "[resourceId('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]",
      "[resourceId('Microsoft.Network/publicIpAddresses/', variables('publicIpAddressName'))]"
    ],
    ...
}
```

Если вы склонны использовать свойство dependsOn для сопоставления связей между ресурсами, то вам важно понимать, зачем вы это делаете. Например, для документирования связей между ресурсами использование свойства dependsOn будет неправильным решением. После развертывания невозможно запросить, какие ресурсы были определены в элементе dependsOn. Установка ненужных зависимостей снижает время развертывания, так как диспетчер ресурсов не может развернуть эти ресурсы параллельно.

## <a name="child-resources"></a>Дочерние ресурсы

Неявная зависимость развертывания автоматически не создается между [дочерним](child-resource-name-type.md) и родительским ресурсами. Если необходимо развернуть дочерний ресурс после родительского ресурса, задайте свойство dependsOn.

В следующем примере показан логический сервер SQL Server и база данных. Обратите внимание, что между базой данных и сервером определена явная зависимость, несмотря на то, что база данных является дочерней по отношению к серверу.

```json
"resources": [
  {
    "type": "Microsoft.Sql/servers",
    "apiVersion": "2020-02-02-preview",
    "name": "[parameters('serverName')]",
    "location": "[parameters('location')]",
    "properties": {
      "administratorLogin": "[parameters('administratorLogin')]",
      "administratorLoginPassword": "[parameters('administratorLoginPassword')]"
    },
    "resources": [
      {
        "type": "databases",
        "apiVersion": "2020-08-01-preview",
        "name": "[parameters('sqlDBName')]",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Standard",
          "tier": "Standard"
          },
        "dependsOn": [
          "[resourceId('Microsoft.Sql/servers', concat(parameters('serverName')))]"
        ]
      }
    ]
  }
]
```

Полный шаблон см. в разделе [Быстрый запуск шаблона базы данных SQL Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/101-sql-database/azuredeploy.json).

## <a name="reference-and-list-functions"></a>Функции list и reference

[Функция reference](template-functions-resource.md#reference) позволяет выражению получать его значение из других пар "имя JSON — значение" или ресурсов среды выполнения. [Функции list*](template-functions-resource.md#list) возвращают значение ресурса из списка операций.

Выражения Reference и List неявно объявляют, что один ресурс зависит от другого. По возможности используйте неявные ссылки, чтобы избежать добавления ненужных зависимостей.

Чтобы принудительно применить неявную зависимость, обратитесь к ресурсу по имени, а не к ИДЕНТИФИКАТОРу ресурса. При передаче идентификатора ресурса функциям list или reference неявные ссылки созданы не будут.

Общий формат функции reference выглядит следующим образом.

```json
reference('resourceName').propertyPath
```

Общий формат функции listKeys выглядит следующим образом.

```json
listKeys('resourceName', 'yyyy-mm-dd')
```

В следующем примере конечная точка CDN явно зависит от профиля CDN и неявно зависит от веб-приложения.

```json
{
    "name": "[variables('endpointName')]",
    "apiVersion": "2016-04-02",
    "type": "endpoints",
    "location": "[resourceGroup().location]",
    "dependsOn": [
      "[variables('profileName')]"
    ],
    "properties": {
      "originHostHeader": "[reference(variables('webAppName')).hostNames[0]]",
      ...
    }
```

Дополнительные сведения см. в разделе о [функции reference](template-functions-resource.md#reference).

## <a name="depend-on-resources-in-a-loop"></a>Зависимость от ресурсов в цикле

Для развертывания ресурсов, зависящих от ресурсов в [цикле копирования](copy-resources.md), существует два варианта. Зависимость от отдельных ресурсов можно задать в цикле или во всем цикле.

> [!NOTE]
> В большинстве случаев зависимость от отдельных ресурсов должна быть задана в цикле копирования. Зависят от всего цикла, только если необходимо, чтобы все ресурсы в цикле существовали до создания следующего ресурса. Установка зависимости во всем цикле приводит к значительному увеличению диаграммы зависимостей, особенно если эти циклические ресурсы зависят от других ресурсов. Развернутые зависимости затрудняют выполнение развертывания.

В следующем примере показано, как развернуть несколько виртуальных машин. Шаблон создает то же количество сетевых интерфейсов. Каждая виртуальная машина зависит от одного сетевого интерфейса, а не от всего цикла.

```json
{
  "type": "Microsoft.Network/networkInterfaces",
  "apiVersion": "2020-05-01",
  "name": "[concat(variables('nicPrefix'),'-',copyIndex())]",
  "location": "[parameters('location')]",
  "copy": {
    "name": "nicCopy",
    "count": "[parameters('vmCount')]"
  },
  ...
},
{
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2020-06-01",
  "name": "[concat(variables('vmPrefix'),copyIndex())]",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[resourceId('Microsoft.Network/networkInterfaces',concat(variables('nicPrefix'),'-',copyIndex()))]"
  ],
  "copy": {
    "name": "vmCopy",
    "count": "[parameters('vmCount')]"
  },
  "properties": {
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "[resourceId('Microsoft.Network/networkInterfaces',concat(variables('nicPrefix'),'-',copyIndex()))]",
          "properties": {
            "primary": "true"
          }
        }
      ]
    },
    ...
  }
}
```

В следующем примере показано, как развернуть три учетные записи хранения перед развертыванием виртуальной машины. Обратите внимание, что для элемента copy задано имя `storagecopy` , а элемент dependsOn для виртуальной машины также имеет значение `storagecopy` .

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

## <a name="circular-dependencies"></a>Циклические зависимости

Resource Manager выявляет циклические зависимости во время проверки шаблона. Если вы получаете сообщение об ошибке для циклической зависимости, оцените шаблон, чтобы узнать, можно ли удалить какие-либо зависимости. Если удаление зависимостей не работает, можно избежать циклических зависимостей, переместив некоторые операции развертывания в дочерние ресурсы. Разверните дочерние ресурсы после ресурсов с циклической зависимостью. Предположим, что вы развертываете две виртуальные машины, но на каждой из них необходимо задать свойства, которые ссылаются на другую виртуальную машину. Их можно развернуть в следующем порядке.

1. vm1.
2. vm2.
3. Расширение на vm1 зависит от vm1 и vm2. Расширение задает на vm1 значения, получаемые от vm2.
4. Расширение на vm2 зависит от vm1 и vm2. Расширение задает на vm2 значения, получаемые от vm1.

Сведения об оценке порядка развертывания и устранении ошибок зависимостей см. в статье [Устранение распространенных ошибок развертывания в Azure с помощью Azure Resource Manager](common-deployment-errors.md).

## <a name="next-steps"></a>Дальнейшие действия

* См. [Руководство. Создание шаблонов Azure Resource Manager с зависимыми ресурсами](template-tutorial-create-templates-with-dependent-resources.md).
* Сведения о модуле Microsoft Learn, охватывающем зависимости ресурсов, см. в статье [управление комплексными облачными развертываниями с помощью расширенных функций шаблонов ARM](/learn/modules/manage-deployments-advanced-arm-template-features/).
* Дополнительные рекомендации по созданию зависимостей см. в статье [Рекомендации по работе с шаблонами Azure Resource Manager](template-best-practices.md).
* Чтобы узнать об устранении ошибок зависимостей во время развертывания, ознакомьтесь с разделом [Устранение распространенных ошибок развертывания в Azure с помощью Azure Resource Manager](common-deployment-errors.md).
* Сведения о создании шаблонов диспетчера ресурсов Azure см. в статье о [создании шаблонов](template-syntax.md).
* Список доступных в шаблоне функций см. в статье о [функциях шаблонов](template-functions.md).

