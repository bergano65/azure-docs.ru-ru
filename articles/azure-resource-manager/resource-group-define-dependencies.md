---
title: Настройка порядка развертывания ресурсов Azure | Документация Майкрософт
description: В этой статье описан способ определения зависимостей между ресурсами во время развертывания для обеспечения правильного порядка развертывания ресурсов.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: ''
ms.assetid: 34ebaf1e-480c-4b4d-9bf6-251bd3f8f2cf
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/20/2019
ms.author: tomfitz
ms.openlocfilehash: 91325b7884eae4c6f4c85c142b1e81cf2121c039
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58295340"
---
# <a name="define-the-order-for-deploying-resources-in-azure-resource-manager-templates"></a>Определение порядка развертывания ресурсов в шаблонах Azure Resource Manager
У заданного ресурса могут быть другие ресурсы, которые должны существовать до его развертывания. Например, сервер SQL Server должен существовать до развертывания базы данных SQL. Эта связь определяется путем пометки одного ресурса как зависимого от другого. Для определения зависимостей можно использовать элемент **dependsOn** или функцию **reference**. 

Диспетчер ресурсов оценивает зависимости между ресурсами и развертывает эти ресурсы согласно установленным зависимостям. Если ресурсы не зависят друг от друга, диспетчер ресурсов развертывает их параллельно. Необходимо только определить зависимости для ресурсов, которые развертываются в том же шаблоне. 

См. [Руководство. Создание шаблонов Azure Resource Manager с зависимыми ресурсами](./resource-manager-tutorial-create-templates-with-dependent-resources.md).

## <a name="dependson"></a>Свойство dependsOn
В шаблоне элемент dependsOn позволяет определить один ресурс как зависимый от одного или нескольких ресурсов. В качестве значения свойства может выступать список имен ресурсов с разделителями-запятыми. 

В следующем примере показан масштабируемый набор виртуальных машин, который зависит от балансировщика нагрузки, виртуальная сеть и цикл, который создает несколько учетных записей хранения. Остальные ресурсы не показаны в этом примере, но они должны существовать в другом месте шаблона.

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "[variables('namingInfix')]",
  "location": "[variables('location')]",
  "apiVersion": "2016-03-30",
  "tags": {
    "displayName": "VMScaleSet"
  },
  "dependsOn": [
    "[variables('loadBalancerName')]",
    "[variables('virtualNetworkName')]",
    "storageLoop",
  ],
  ...
}
```

В приведенном выше примере добавляется зависимость от ресурсов, которые создаются с помощью цикла копирования **storageLoop**. Пример см. в статье [Создание нескольких экземпляров ресурсов в Azure Resource Manager](resource-group-create-multiple.md).

При определении зависимостей можно указать пространство имен поставщика ресурсов и тип ресурса, чтобы избежать неоднозначности. Например, чтобы уточнить сведения о подсистеме балансировки нагрузки и виртуальной сети, имена которых, возможно, совпадают с именами других ресурсов, используйте следующий формат.

```json
"dependsOn": [
  "[resourceId('Microsoft.Network/loadBalancers', variables('loadBalancerName'))]",
  "[resourceId('Microsoft.Network/virtualNetworks', variables('virtualNetworkName'))]"
]
``` 

Если вы склонны использовать свойство dependsOn для сопоставления связей между ресурсами, то вам важно понимать, зачем вы это делаете. Например, для документирования связей между ресурсами использование свойства dependsOn будет неправильным решением. После развертывания невозможно запросить, какие ресурсы были определены в элементе dependsOn. Использование свойства dependsOn потенциально влияет на время развертывания, так как диспетчер ресурсов не может выполнять развертывание двух зависимых ресурсов параллельно. 

## <a name="child-resources"></a>Дочерние ресурсы
Свойство resources позволяет указать дочерние ресурсы, связанные с определяемым ресурсом. Дочерние ресурсы можно определять максимум на пяти нижестоящих уровнях. Важно отметить, что зависимость неявное развертывания не создается между дочерним и родительским ресурсом. Если вам нужно, чтобы дочерний ресурс был развернут после родительского ресурса, эту зависимость необходимо явно указать в свойстве dependsOn. 

Каждый родительский ресурс принимает в качестве дочерних ресурсов только определенные типы ресурсов. Принимаемые типы ресурсов указаны в [схеме шаблона](https://github.com/Azure/azure-resource-manager-schemas) родительского ресурса. Имя типа дочернего ресурса содержит имя типа родительского ресурса, например: **Microsoft.Web/sites/config** и **Microsoft.Web/sites/extensions** являются дочерними ресурсами по отношению к **Microsoft.Web/sites**.

В следующем примере показаны SQL Server и база данных SQL. Обратите внимание, что несмотря на то, что база данных является дочерним ресурсом по отношению к серверу, между базой данных SQL и сервером SQL Server определена явная зависимость.

```json
"resources": [
  {
    "name": "[variables('sqlserverName')]",
    "type": "Microsoft.Sql/servers",
    "location": "[resourceGroup().location]",
    "tags": {
      "displayName": "SqlServer"
    },
    "apiVersion": "2014-04-01-preview",
    "properties": {
      "administratorLogin": "[parameters('administratorLogin')]",
      "administratorLoginPassword": "[parameters('administratorLoginPassword')]"
    },
    "resources": [
      {
        "name": "[parameters('databaseName')]",
        "type": "databases",
        "location": "[resourceGroup().location]",
        "tags": {
          "displayName": "Database"
        },
        "apiVersion": "2014-04-01-preview",
        "dependsOn": [
          "[variables('sqlserverName')]"
        ],
        "properties": {
          "edition": "[parameters('edition')]",
          "collation": "[parameters('collation')]",
          "maxSizeBytes": "[parameters('maxSizeBytes')]",
          "requestedServiceObjectiveName": "[parameters('requestedServiceObjectiveName')]"
        }
      }
    ]
  }
]
```

## <a name="reference-and-list-functions"></a>Функции list и reference
[Функция reference](resource-group-template-functions-resource.md#reference) позволяет выражению получать его значение из других пар "имя JSON — значение" или ресурсов среды выполнения. [Функции list*](resource-group-template-functions-resource.md#list) возвращают значение ресурса из списка операций.  Выражения и списки со ссылками неявно объявляют, что один ресурс зависит от другого, когда выходные ресурсы развернуты в одном шаблоне и обращаются друг к другу по именам (а не по идентификатору ресурса). При передаче идентификатора ресурса функциям list или reference неявные ссылки созданы не будут.

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
    "type": "endpoints",
    "location": "[resourceGroup().location]",
    "apiVersion": "2016-04-02",
    "dependsOn": [
            "[variables('profileName')]"
    ],
    "properties": {
        "originHostHeader": "[reference(variables('webAppName')).hostNames[0]]",
        ...
    }
```

Зависимости можно указать как с помощью этой функции, так и с помощью свойства dependsOn, но использовать оба варианта для одного зависимого ресурса не нужно. По возможности используйте неявные ссылки, чтобы избежать добавления ненужных зависимостей.

Дополнительные сведения см. в разделе о [функции reference](resource-group-template-functions-resource.md#reference).

## <a name="circular-dependencies"></a>Циклические зависимости

Resource Manager выявляет циклические зависимости во время проверки шаблона. Если возникает ошибка и появляется сообщение о том, что существует циклическая зависимость, оцените шаблон на предмет лишних зависимостей, которые можно удалить. Если удаление зависимостей не помогает, можно избежать появления циклических зависимостей, переместив некоторые операции развертывания в дочерние ресурсы, которые развертываются после ресурсов с циклической зависимостью. Предположим, что вы развертываете две виртуальные машины, но на каждой из них необходимо задать свойства, которые ссылаются на другую виртуальную машину. Их можно развернуть в следующем порядке.

1. vm1.
2. vm2.
3. Расширение на vm1 зависит от vm1 и vm2. Расширение задает на vm1 значения, получаемые от vm2.
4. Расширение на vm2 зависит от vm1 и vm2. Расширение задает на vm2 значения, получаемые от vm1.

Сведения об оценке порядка развертывания и устранении ошибок зависимостей см. в статье [Устранение распространенных ошибок развертывания в Azure с помощью Azure Resource Manager](resource-manager-common-deployment-errors.md).

## <a name="next-steps"></a>Дальнейшие действия

* См. [Руководство. Создание шаблонов Azure Resource Manager с зависимыми ресурсами](./resource-manager-tutorial-create-templates-with-dependent-resources.md).
* Дополнительные рекомендации по созданию зависимостей см. в статье [Рекомендации по работе с шаблонами Azure Resource Manager](template-best-practices.md).
* Чтобы узнать об устранении ошибок зависимостей во время развертывания, ознакомьтесь с разделом [Устранение распространенных ошибок развертывания в Azure с помощью Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Сведения о создании шаблонов диспетчера ресурсов Azure см. в статье о [создании шаблонов](resource-group-authoring-templates.md). 
* Список доступных в шаблоне функций см. в статье о [функциях шаблонов](resource-group-template-functions.md).

