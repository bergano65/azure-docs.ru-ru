---
title: Шаблоны ресурсов Azure Resource Manager | Документация Майкрософт
description: Раздел ресурсов в шаблонах Azure Resource Manager, в которых используется декларативный синтаксис JSON.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/18/2018
ms.author: tomfitz
ms.openlocfilehash: 5a2b38e5d627341b3684ee55d13ee06881fbae55
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/21/2018
ms.locfileid: "53728369"
---
# <a name="resources-section-of-azure-resource-manager-templates"></a>Раздел Resources в шаблонах Azure Resource Manager

В разделе resources определяются ресурсы, которые развертываются или обновляются. Этот раздел может еще больше усложниться, так как вы должны понимать принципы работы развертываемых типов, чтобы предоставить правильные значения.

## <a name="available-properties"></a>Доступные свойства

Ресурсы определяются с помощью следующей структуры:

```json
"resources": [
  {
      "condition": "<true-to-deploy-this-resource>",
      "apiVersion": "<api-version-of-resource>",
      "type": "<resource-provider-namespace/resource-type-name>",
      "name": "<name-of-the-resource>",
      "location": "<location-of-resource>",
      "tags": {
          "<tag-name1>": "<tag-value1>",
          "<tag-name2>": "<tag-value2>"
      },
      "comments": "<your-reference-notes>",
      "copy": {
          "name": "<name-of-copy-loop>",
          "count": <number-of-iterations>,
          "mode": "<serial-or-parallel>",
          "batchSize": <number-to-deploy-serially>
      },
      "dependsOn": [
          "<array-of-related-resource-names>"
      ],
      "properties": {
          "<settings-for-the-resource>",
          "copy": [
              {
                  "name": ,
                  "count": ,
                  "input": {}
              }
          ]
      },
      "sku": {
          "name": "<sku-name>",
          "tier": "<sku-tier>",
          "size": "<sku-size>",
          "family": "<sku-family>",
          "capacity": <sku-capacity>
      },
      "kind": "<type-of-resource>",
      "plan": {
          "name": "<plan-name>",
          "promotionCode": "<plan-promotion-code>",
          "publisher": "<plan-publisher>",
          "product": "<plan-product>",
          "version": "<plan-version>"
      },
      "resources": [
          "<array-of-child-resources>"
      ]
  }
]
```

| Имя элемента | Обязательно | ОПИСАНИЕ |
|:--- |:--- |:--- |
| condition | Нет  | Логическое значение, указывающее, будет ли подготавливаться ресурс во время этого развертывания. Если установлено значение `true`, при развертывании создается ресурс. Если установлено значение `false`, при развертывании ресурс не создается. |
| версия_API |Yes |Версия REST API, которая будет использована для создания ресурса. |
| Тип |Yes |Тип ресурса. Это значение представляет собой сочетание пространства имен поставщика ресурсов и типа ресурса (например, **Microsoft.Storage/storageAccounts**). |
| name |Yes |Имя ресурса. Имя должно соответствовать ограничениям компонентов URI, определенным в RFC3986. Кроме того, службы Azure, которые предоставляют имя ресурса внешним пользователям, проверяют это имя, чтобы убедиться, что это не попытка подделки другого удостоверения. |
| location |Varies |Поддерживаемые географические расположения указанного ресурса. Вы можете выбрать любое из доступных расположений. Но обычно имеет смысл выбрать расположение, которое находится недалеко от пользователей. Кроме того, целесообразно разместить взаимодействующие ресурсы в одном регионе. Большинству типов ресурсов нужно расположение, но некоторым типам (например, назначению роли) оно не требуется. |
| tags |Нет  |Теги, связанные с ресурсом. Примените теги, чтобы логически организовать ресурсы в подписке. |
| комментарии |Нет  |Заметки по ресурсам в шаблоне |
| копирование |Нет  |Количество создаваемых ресурсов (если нужно несколько экземпляров). Параллельный режим используется по умолчанию. Используйте последовательный режим, если вы не хотите развертывать все ресурсы одновременно. Дополнительные сведения см. в статье [Создание нескольких экземпляров ресурсов в Azure Resource Manager](resource-group-create-multiple.md). |
| Свойство dependsOn |Нет  |Ресурсы, которые должны быть развернуты перед развертыванием этого ресурса. Resource Manager оценивает зависимости между ресурсами и развертывает эти ресурсы в правильном порядке. Если ресурсы не зависят друг от друга, они развертываются параллельно. Значение может представлять собой разделенный запятыми список имен ресурсов или уникальных идентификаторов ресурсов. Выводится только список ресурсов, развертываемых в этом шаблоне. Ресурсы, которые не определены в этом шаблоне, уже должны существовать. Избегайте добавления ненужных зависимостей, так как это может замедлить развертывание и привести к созданию циклических зависимостей. Рекомендации по настройке зависимостей см. в статье [Определение зависимостей в шаблонах диспетчера ресурсов Azure](resource-group-define-dependencies.md). |
| properties |Нет  |Параметры конфигурации ресурса. Значения свойств совпадают со значениями, указываемыми в тексте запроса для операции REST API (метод PUT) для создания ресурса. Кроме того, можно указать массив copy для создания нескольких экземпляров свойства. |
| sku | Нет  | В некоторых ресурсах допускается использовать значения, определяющие номер SKU для развертывания. Например, можно указать тип избыточности для учетной записи хранения. |
| kind | Нет  | В некоторых ресурсах допускается использовать значение, которое определяет тип развертываемого ресурса. Например, можно указать в качестве типа создаваемой базы данных Cosmos DB. |
| План | Нет  | В некоторых ресурсах допускается использовать значения, определяющие номер плана для развертывания. Например, можно указать образ Marketplace для виртуальной машины. | 
| ресурсов |Нет  |Дочерние ресурсы, которые зависят от определяемого ресурса. Следует указать только те типы ресурсов, которые разрешены в схеме родительского ресурса. Полное имя типа дочернего ресурса содержит тип родительского ресурса, например **Microsoft.Web/sites/extensions**. Зависимость от родительского ресурса не подразумевается. Ее необходимо определить явным образом. |

## <a name="condition"></a>Условие

Чтобы во время развертывания решить, нужно ли создавать ресурс, добавьте элемент `condition`. Этот элемент возвращает значение True или False. Если значение true, ресурс создан. Если значение false, ресурс не создан. Значение может применяться только ко всему ресурсу.

Как правило, это значение применяется для создания нового ресурса или использования существующего. Например, чтобы указать, развернута ли новая учетная запись хранения или используется ли имеющаяся, сделайте следующее:

```json
{
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2017-06-01",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "[variables('storageAccountType')]"
    },
    "kind": "Storage",
    "properties": {}
}
```

Полный пример шаблона с использованием элемента `condition` см. по адресу [VM with a new or existing Virtual Network, Storage, and Public IP](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-new-or-existing-conditions) (Виртуальная машина с новой или существующей виртуальной сетью, хранилищем и общедоступным IP-адресом).

## <a name="resource-specific-values"></a>Значения ресурсов

Элементы **apiVersion**, **type** и **properties** различны для каждого типа ресурса. Элементы **sku**, **kind** и **plan** доступны только для некоторых типов ресурсов. Чтобы определить значения для этих свойств, ознакомьтесь со [справочником по шаблонам](/azure/templates/).

## <a name="resource-names"></a>Имена ресурсов

Обычно в Resource Manager вы работаете с именами ресурсов трех типов:

* имена ресурсов, которые должны быть уникальными;
* имена ресурсов, которые могут не быть уникальными, но должны помогать в определении ресурса;
* имена ресурсов, которые могут быть универсальными.

### <a name="unique-resource-names"></a>Уникальные имена ресурсов

Задает уникальное имя для любого типа ресурса, который имеет конечную точку доступа к данным. Ниже приведено несколько распространенных типов ресурсов, для которых требуются уникальные имена:

* служба хранилища Azure<sup>1</sup>; 
* веб-приложения службы приложений Azure;
* SQL Server;
* Хранилище ключей Azure
* Кэш Azure для Redis
* Пакетная служба Azure
* Azure Traffic Manager
* Поиск Azure
* Azure HDInsight

<sup>1</sup> Имена учетных записей хранения также должны содержать до 24 знаков в нижнем регистре без дефисов.

При задании имени можно вручную создать уникальное имя или использовать для этого функцию [uniqueString()](resource-group-template-functions-string.md#uniquestring). Вам также может потребоваться добавить префикс или суффикс к результату **uniqueString**. Измените уникальное имя, чтобы было проще определить тип ресурса по его имени. Например, можно создать уникальное имя учетной записи хранения с помощью следующей переменной:

```json
"variables": {
    "storageAccountName": "[concat(uniqueString(resourceGroup().id),'storage')]"
}
```

### <a name="resource-names-for-identification"></a>Имена ресурсов для идентификации
Для некоторых типов ресурсов необязательно указывать уникальные имена. Вы можете просто указать имя, которое определяет контекст и тип ресурса.

```json
"parameters": {
    "vmName": { 
        "type": "string",
        "defaultValue": "demoLinuxVM",
        "metadata": {
            "description": "The name of the VM to create."
        }
    }
}
```

### <a name="generic-resource-names"></a>Универсальные имена ресурсов
Для типов ресурсов, к которым часто обращаются через другой ресурс, можно использовать универсальное имя, жестко заданное в шаблоне. Например, можно задать стандартное универсальное имя для правил брандмауэра на SQL Server:

```json
{
    "type": "firewallrules",
    "name": "AllowAllWindowsAzureIps",
    ...
}
```

## <a name="location"></a>Расположение
При развертывании шаблона вам нужно указать расположение для каждого ресурса. Различные типы ресурсов поддерживаются в разных расположениях. Чтобы просмотреть список расположений, доступных вашей подписке для определенного типа ресурса, используйте Azure PowerShell или Azure CLI. 

В следующем примере список расположений для типа ресурса `Microsoft.Web\sites` отображается с помощью PowerShell:

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
```

В следующем примере список расположений для типа ресурса `Microsoft.Web\sites` отображается с помощью Azure CLI.

```azurecli
az provider show -n Microsoft.Web --query "resourceTypes[?resourceType=='sites'].locations"
```

Определив поддерживаемые расположения для ресурсов, укажите нужное расположение в шаблоне. Самый простой способ задать это значение — это создать группу ресурсов в расположении, которое поддерживает типы ресурсов, а затем указать для каждого расположения `[resourceGroup().location]`. Можно развернуть шаблон в группы ресурсов в разных расположениях, но при этом не изменять параметры или значения в шаблоне. 

В следующем примере показана учетная запись хранения, которая развертывается в том же расположении, что и группа ресурсов:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
      "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageName')]",
      "location": "[resourceGroup().location]",
      "tags": {
        "Dept": "Finance",
        "Environment": "Production"
      },
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": { }
    }
    ]
}
```

Если необходимо жестко определить расположение в шаблоне, укажите имя одного из поддерживаемых регионов. В следующем примере показано учетная запись хранения, которая всегда развертывается в центрально-северной части США:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat('storageloc', uniqueString(resourceGroup().id))]",
      "location": "North Central US",
      "tags": {
        "Dept": "Finance",
        "Environment": "Production"
      },
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": { }
    }
    ]
}
```

## <a name="tags"></a>Теги
[!INCLUDE [resource-manager-governance-tags](../../includes/resource-manager-governance-tags.md)]

### <a name="add-tags-to-your-template"></a>Добавление тегов в шаблон

[!INCLUDE [resource-manager-tags-in-templates](../../includes/resource-manager-tags-in-templates.md)]

## <a name="child-resources"></a>Дочерние ресурсы

Внутри ресурсов некоторых типов можно также определить набор дочерних ресурсов. Дочерние ресурсы — это ресурсы, которые существуют только в контексте другого ресурса. Например, база данных SQL не может существовать без сервера SQL Server, то есть база данных является дочерним объектом для сервера. Базу данных можно указать в определении сервера.

```json
{
  "name": "exampleserver",
  "type": "Microsoft.Sql/servers",
  "apiVersion": "2014-04-01",
  ...
  "resources": [
    {
      "name": "exampledatabase",
      "type": "databases",
      "apiVersion": "2014-04-01",
      ...
    }
  ]
}
```

Для вложенного ресурса задается тип `databases`, но его полным типом ресурса является `Microsoft.Sql/servers/databases`. `Microsoft.Sql/servers/` не указывается, так как предполагается из типа родительского ресурса. Дочернему ресурсу присваивается имя `exampledatabase`, но его полное имя включает в себя имя родительского ресурса. `exampleserver` не указывается, так как предполагается из родительского ресурса.

Формат типа дочернего ресурса: `{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}`

Формат имени дочернего ресурса: `{parent-resource-name}/{child-resource-name}`

Однако нет необходимости определять базу данных на сервере. Дочерний ресурс можно определить на верхнем уровне. Этот подход можно использовать, если родительский ресурс не развертывается в том же шаблоне или требуется использовать `copy` для создания нескольких дочерних ресурсов. В этом случае нужно указать полный тип ресурса и добавить в имя дочернего ресурса имя родительского ресурса.

```json
{
  "name": "exampleserver",
  "type": "Microsoft.Sql/servers",
  "apiVersion": "2014-04-01",
  "resources": [ 
  ],
  ...
},
{
  "name": "exampleserver/exampledatabase",
  "type": "Microsoft.Sql/servers/databases",
  "apiVersion": "2014-04-01",
  ...
}
```

При создании полной ссылки на ресурс порядок объединения сегментов из типа и имени представляет собой не только использование этих двух вариантов. Вместо этого после пространства имен используйте пары *типа и имени*, начиная от наименее подходящей к наиболее подходящей.

```json
{resource-provider-namespace}/{parent-resource-type}/{parent-resource-name}[/{child-resource-type}/{child-resource-name}]*
```

Например: 

`Microsoft.Compute/virtualMachines/myVM/extensions/myExt` — правильно, `Microsoft.Compute/virtualMachines/extensions/myVM/myExt` — неправильно.



## <a name="next-steps"></a>Дополнительная информация
* Полные шаблоны для различных типов решений доступны на странице [Шаблоны быстрого запуска Azure](https://azure.microsoft.com/documentation/templates/).
* Дополнительные сведения о функциях, которые можно использовать в шаблонах, см. в статье [Функции шаблонов Azure Resource Manager](resource-group-template-functions.md).
* Дополнительные рекомендации по созданию шаблонов см. в статье [Рекомендации по работе с шаблонами Azure Resource Manager](template-best-practices.md).
* Может потребоваться использовать ресурсы, которые существуют в другой группе ресурсов. Это распространенная ситуация при работе с учетными записями хранения или виртуальными сетями, которые совместно используются в нескольких группах ресурсов. Дополнительные сведения см. в описании [функции resourceId](resource-group-template-functions-resource.md#resourceid).
* Сведения об ограничениях имен ресурсов см. в статье [Рекомендуемые соглашения об именовании для ресурсов Azure](../guidance/guidance-naming-conventions.md).