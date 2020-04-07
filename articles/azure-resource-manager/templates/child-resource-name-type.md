---
title: Детские ресурсы в шаблонах
description: Описывает, как установить имя и тип для ресурсов ребенка в шаблоне управления ресурсами Azure.
ms.topic: conceptual
ms.date: 08/26/2019
ms.openlocfilehash: 3a69829e674925982c618807f49433a033d8c5f9
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80743834"
---
# <a name="set-name-and-type-for-child-resources"></a>Установка имени и типа для ресурсов ребенка

Детские ресурсы являются ресурсами, которые существуют только в контексте другого ресурса. Например, [виртуальное расширение машины](/azure/templates/microsoft.compute/2019-03-01/virtualmachines/extensions) не может существовать без [виртуальной машины.](/azure/templates/microsoft.compute/2019-03-01/virtualmachines) Ресурс расширения является ребенком виртуальной машины.

В шаблоне «Менеджер ресурсов» можно указать ресурс ребенка как в родительском ресурсе, так и за пределами родительского ресурса. В следующем примере показан ресурс ребенка, включенный в свойство ресурсов родительского ресурса.

```json
"resources": [
  {
    <parent-resource>
    "resources": [
      <child-resource>
    ]
  }
]
```

Следующий пример показывает ресурс ребенка за пределами родительского ресурса. Этот подход можно использовать, если родительский ресурс не развернут в одном шаблоне, или если вы хотите использовать [копию](copy-resources.md) для создания более одного детского ресурса.

```json
"resources": [
  {
    <parent-resource>
  },
  {
    <child-resource>
  }
]
```

Значения, которые вы предоставляете для имени ресурса и типа, варьируются в зависимости от того, определяется ли ресурс ребенка внутри или за пределами родительского ресурса.

## <a name="within-parent-resource"></a>В родительском ресурсе

При определении в типе родительского ресурса вы форматируете значения типа и имени как одно слово без слэши.

```json
"type": "{child-resource-type}",
"name": "{child-resource-name}",
```

Ниже приводится пример виртуальной сети и с подсетью. Обратите внимание, что подсеть включена в массив ресурсов для виртуальной сети. Имя устанавливается для **Subnet1** и тип установлен в **подсети.** Ресурс ребенка помечен как зависимый от родительского ресурса, поскольку родительский ресурс должен существовать до развертывания ресурса .

```json
"resources": [
  {
    "type": "Microsoft.Network/virtualNetworks",
    "apiVersion": "2018-10-01",
    "name": "VNet1",
    "location": "[parameters('location')]",
    "properties": {
      "addressSpace": {
        "addressPrefixes": [
          "10.0.0.0/16"
        ]
      }
    },
    "resources": [
      {
        "type": "subnets",
        "apiVersion": "2018-10-01",
        "name": "Subnet1",
        "location": "[parameters('location')]",
        "dependsOn": [
          "VNet1"
        ],
        "properties": {
          "addressPrefix": "10.0.0.0/24"
        }
      }
    ]
  }
]
```

Полный тип ресурса по-прежнему **Microsoft.Network/virtualNetworks/subnets**. Вы не предоставляете **Microsoft.Network/virtualNetworks/,** потому что это предполагается из типа родительского ресурса.

Имя ресурса ребенка устанавливается на **Subnet1,** но полное имя включает родительское имя. Вы не предоставляете **VNet1,** потому что это предполагается из родительского ресурса.

## <a name="outside-parent-resource"></a>Внешний родительский ресурс

При определении вне родительского ресурса вы форматируете тип и слэши, чтобы включить тип и имя родителя.

```json
"type": "{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}",
"name": "{parent-resource-name}/{child-resource-name}",
```

В следующем примере показана виртуальная сеть и подсеть, которые определяются на корневом уровне. Обратите внимание, что подсеть не включена в массив ресурсов для виртуальной сети. Название устанавливается на **VNet1/Subnet1** и тип установлен на **Microsoft.Network/virtualNetworks/subnets**. Ресурс ребенка помечен как зависимый от родительского ресурса, поскольку родительский ресурс должен существовать до развертывания ресурса .

```json
"resources": [
  {
    "type": "Microsoft.Network/virtualNetworks",
    "apiVersion": "2018-10-01",
    "name": "VNet1",
    "location": "[parameters('location')]",
    "properties": {
      "addressSpace": {
        "addressPrefixes": [
          "10.0.0.0/16"
        ]
      }
    }
  },
  {
    "type": "Microsoft.Network/virtualNetworks/subnets",
    "apiVersion": "2018-10-01",
    "location": "[parameters('location')]",
    "name": "VNet1/Subnet1",
    "dependsOn": [
      "VNet1"
    ],
    "properties": {
      "addressPrefix": "10.0.0.0/24"
    }
  }
]
```

## <a name="next-steps"></a>Дальнейшие действия

* Сведения о создании шаблонов диспетчера ресурсов Azure см. в статье о [создании шаблонов](template-syntax.md).

* Чтобы узнать о формате имени ресурса при ссылках [reference function](template-functions-resource.md#reference)на ресурс, см.
