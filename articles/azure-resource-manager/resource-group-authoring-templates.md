---
title: Структура и синтаксис шаблона Azure Resource Manager | Документация Майкрософт
description: Описывается создание структуры и свойств шаблонов Azure Resource Manager с помощью декларативного синтаксиса JSON.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 08/02/2019
ms.author: tomfitz
ms.openlocfilehash: 53b2f9783b33c859ca2c5de5f35353b8482ea5c7
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70275137"
---
# <a name="understand-the-structure-and-syntax-of-azure-resource-manager-templates"></a>Описание структуры и синтаксиса шаблонов Azure Resource Manager

В этой статье описана структура шаблона Azure Resource Manager. Статья содержит информацию о разных разделах шаблона и свойствах, которые доступны в этих разделах. Шаблон состоит из JSON и выражений, на основе которых можно создавать значения для развертывания.

Эта статья предназначена для пользователей, знакомых с шаблонами диспетчер ресурсов. Он предоставляет подробные сведения о структуре и синтаксисе шаблона. Если вы хотите ознакомиться с введением в создание шаблона, см. статью [Создание первого шаблона Azure Resource Manager](resource-manager-create-first-template.md).

## <a name="template-format"></a>Формат шаблона

Шаблон с самой простой структурой содержит следующие элементы:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "",
  "apiProfile": "",
  "parameters": {  },
  "variables": {  },
  "functions": [  ],
  "resources": [  ],
  "outputs": {  }
}
```

| Имя элемента | Обязательное значение | Описание |
|:--- |:--- |:--- |
| $schema |Да |Расположение файла схемы JSON, который описывает версию языка шаблона.<br><br> Для развертывания групп ресурсов используйте `https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#`<br><br>Для развертывания подписки используйте `https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#` |
| contentVersion |Да |Версия шаблона (например, 1.0.0.0). Для этого элемента можно предоставить любое значение. Это значение позволяет задокументировать важные изменения в шаблоне. При развертывании ресурсов с помощью шаблона это значение позволяет убедиться в том, что используется нужный шаблон. |
| апипрофиле |Нет | Версия API, которая служит коллекцией версий API для типов ресурсов. Используйте это значение, чтобы не указывать версии API для каждого ресурса в шаблоне. Если указана версия профиля API и не указана версия API для типа ресурса, диспетчер ресурсов использует версию API для этого типа ресурсов, который определен в профиле.<br><br>Свойство профиля API особенно полезно при развертывании шаблона в различных средах, таких как Azure Stack и глобальные Azure. Используйте версию профиля API, чтобы убедиться, что шаблон автоматически использует версии, поддерживаемые в обеих средах. Список текущих версий профиля API и версий API ресурсов, определенных в профиле, см. в разделе [профиль API](https://github.com/Azure/azure-rest-api-specs/tree/master/profile).<br><br>Дополнительные сведения см. в статье [Tracking Versions using API Profiles](templates-cloud-consistency.md#track-versions-using-api-profiles). |
| [parameters](#parameters) |Нет |Значения, которые предоставляются при выполнении развертывания для настройки развертывания ресурсов. |
| [variables](#variables) |Нет |Значения, используемые в виде фрагментов JSON в шаблоне для упрощения выражений на языке шаблона. |
| [functions](#functions) |Нет |Определяемые пользователем функции, доступные в шаблоне. |
| [resources](#resources) |Да |Типы ресурсов, которые развертываются или обновляются в группе ресурсов или подписке. |
| [outputs](#outputs) |Нет |Значения, возвращаемые после развертывания. |

Каждый элемент содержит свойства, которые можно задать. В этой статье подробнее описаны разделы шаблона.

## <a name="parameters"></a>Параметры

В разделе параметров шаблона указываются значения, которые вы можете вводить во время развертывания ресурсов. Значения этих параметров позволяют настраивать развертывание путем предоставления значений, предназначенных для конкретной среды (например, для среды разработки, тестирования и рабочей среды). Задавать параметры в шаблоне не обязательно, однако без них шаблон всегда будет развертывать одни и те же ресурсы с одинаковыми именами, расположениями и свойствами.

Существует ограничение в 256 параметров на один шаблон. Вы можете сократить число параметров, создавая объекты с несколькими свойствами. Этот метод описан далее в этой статье.

### <a name="available-properties"></a>Доступные свойства

Для параметра доступны следующие свойства:

```json
"parameters": {
  "<parameter-name>" : {
    "type" : "<type-of-parameter-value>",
    "defaultValue": "<default-value-of-parameter>",
    "allowedValues": [ "<array-of-allowed-values>" ],
    "minValue": <minimum-value-for-int>,
    "maxValue": <maximum-value-for-int>,
    "minLength": <minimum-length-for-string-or-array>,
    "maxLength": <maximum-length-for-string-or-array-parameters>,
    "metadata": {
      "description": "<description-of-the parameter>" 
    }
  }
}
```

| Имя элемента | Обязательное значение | Описание |
|:--- |:--- |:--- |
| parameterName |Да |Имя параметра. Должно быть допустимым идентификатором JavaScript. |
| type |Да |Тип значения параметра. Допустимые типы и значения: **string**, **securestring**, **int**, **bool**, **object**, **secureObject** и **array**. |
| defaultValue |Нет |Значение параметра, используемое по умолчанию, если пользователь не задал иное значение. |
| allowedValues |Нет |Массив допустимых значений параметра, по которому сверяются правильные значения. |
| minValue |Нет |Минимальное значение для параметров типа int. Это включающее значение. |
| maxValue |Нет |Максимальное значение для параметров типа int. Это включающее значение. |
| minLength |Нет |Минимальная длина (включительно) параметров типа string, secure string и array. |
| maxLength |Нет |Максимальная длина (включительно) параметров типа string, secure string и array. |
| description |Нет |Описание параметра, отображаемого для пользователей на портале. Дополнительные сведения см. в разделе [комментариев в шаблонах](#comments). |

### <a name="define-and-use-a-parameter"></a>Определение и применение параметра

Следующий пример демонстрирует определение простого параметра. Здесь указывается имя параметра и тип его значения (строковое значение). Параметр принимает только те значения, которые имеют смысл для его назначения. Также указано значение по умолчанию, которое используется, если во время развертывания значение параметра не указано. И наконец, в определении параметра есть описание его назначения.

```json
"parameters": {
  "storageSKU": {
    "type": "string",
    "allowedValues": [
      "Standard_LRS",
      "Standard_ZRS",
      "Standard_GRS",
      "Standard_RAGRS",
      "Premium_LRS"
    ],
    "defaultValue": "Standard_LRS",
    "metadata": {
      "description": "The type of replication to use for the storage account."
    }
  }   
}
```

Ссылка на значение параметра указывается в шаблоне следующим образом:

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "sku": {
      "name": "[parameters('storageSKU')]"
    },
    ...
  }
]
```

### <a name="template-functions-with-parameters"></a>Использование функций шаблонов с параметрами

Чтобы определить значение по умолчанию для параметра, можно использовать большинство функций шаблонов. Также для создания значения по умолчанию можно использовать значение другого параметра. Следующий шаблон демонстрирует применение функций в значениях по умолчанию:

```json
"parameters": {
  "siteName": {
    "type": "string",
    "defaultValue": "[concat('site', uniqueString(resourceGroup().id))]",
    "metadata": {
      "description": "The site name. To use the default value, do not specify a new value."
    }
  },
  "hostingPlanName": {
    "type": "string",
    "defaultValue": "[concat(parameters('siteName'),'-plan')]",
    "metadata": {
      "description": "The host name. To use the default value, do not specify a new value."
    }
  }
}
```

В разделе параметров недоступна функция `reference`. Параметры вычисляются до развертывания, поэтому функция `reference` не может получить состояние выполнения ресурса. 

### <a name="objects-as-parameters"></a>Объекты в качестве параметров

Чтобы структурировать связанные друг с другом значения, их можно передать в виде объекта. Что не менее важно, этот подход сокращает число параметров в шаблоне.

Задайте в шаблоне параметр обычным образом, а во время развертывания передайте для этого параметра не одно значение, а объект в формате JSON. 

```json
"parameters": {
  "VNetSettings": {
    "type": "object",
    "defaultValue": {
      "name": "VNet1",
      "location": "eastus",
      "addressPrefixes": [
        {
          "name": "firstPrefix",
          "addressPrefix": "10.0.0.0/22"
        }
      ],
      "subnets": [
        {
          "name": "firstSubnet",
          "addressPrefix": "10.0.0.0/24"
        },
        {
          "name": "secondSubnet",
          "addressPrefix": "10.0.1.0/24"
        }
      ]
    }
  }
},
```

Тогда вы сможете ссылаться в шаблоне на вложенные свойства этого параметра с помощью оператора-точки.

```json
"resources": [
  {
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[parameters('VNetSettings').name]",
    "location": "[parameters('VNetSettings').location]",
    "properties": {
      "addressSpace":{
        "addressPrefixes": [
          "[parameters('VNetSettings').addressPrefixes[0].addressPrefix]"
        ]
      },
      "subnets":[
        {
          "name":"[parameters('VNetSettings').subnets[0].name]",
          "properties": {
            "addressPrefix": "[parameters('VNetSettings').subnets[0].addressPrefix]"
          }
        },
        {
          "name":"[parameters('VNetSettings').subnets[1].name]",
          "properties": {
            "addressPrefix": "[parameters('VNetSettings').subnets[1].addressPrefix]"
          }
        }
      ]
    }
  }
]
```

### <a name="parameter-example-templates"></a>Примеры шаблонов параметров

Эти примеры шаблонов демонстрируют разные сценарии использования параметров. Разверните их и изучите, как обрабатываются параметры в различных сценариях.

|Шаблон  |Описание  |
|---------|---------|
|[Использование параметров с функциями для определения значений по умолчанию](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterswithfunctions.json) | Демонстрирует, как можно применить функции шаблонов при определении значений по умолчанию для параметров. Шаблон не развертывает ресурсы. Он только создает значения параметров и возвращает их. |
|[Объект параметров](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterobject.json) | Демонстрирует использование объекта в качестве параметра. Шаблон не развертывает ресурсы. Он только создает значения параметров и возвращает их. |

## <a name="variables"></a>Переменные

В разделе переменных вы создаете значения, которые можно использовать в разных частях шаблона. Переменные определять не обязательно, однако они часто упрощают шаблон, снижая число сложных выражений.

### <a name="available-definitions"></a>Доступные определения

В следующем примере показаны доступные параметры для определения переменной.

```json
"variables": {
  "<variable-name>": "<variable-value>",
  "<variable-name>": { 
    <variable-complex-type-value> 
  },
  "<variable-object-name>": {
    "copy": [
      {
        "name": "<name-of-array-property>",
        "count": <number-of-iterations>,
        "input": <object-or-value-to-repeat>
      }
    ]
  },
  "copy": [
    {
      "name": "<variable-array-name>",
      "count": <number-of-iterations>,
      "input": <object-or-value-to-repeat>
    }
  ]
}
```

Сведения об использовании `copy` для создания нескольких значений для переменной см. в разделе [Переменная iteration](resource-group-create-multiple.md#variable-iteration).

### <a name="define-and-use-a-variable"></a>Определение и применение переменной

В приведенном ниже примере показано определение переменной. В нем создается строковое значение имени учетной записи хранения. Он использует несколько функций шаблона для получения значения параметра и объединяет его в уникальную строку.

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

Переменная используется при определении ресурса.

```json
"resources": [
  {
    "name": "[variables('storageName')]",
    "type": "Microsoft.Storage/storageAccounts",
    ...
```

### <a name="configuration-variables"></a>Переменные конфигурации

С помощью сложных типов JSON можно определить связанные значения для среды.

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

Получить текущие настройки можно следующим образом:

```json
"[variables('environmentSettings')[parameters('environmentName')].instanceSize]"
```

### <a name="variable-example-templates"></a>Примеры шаблонов переменных

Эти примеры шаблонов демонстрируют разные сценарии использования переменных. Разверните их и изучите, как обрабатываются переменные в различных сценариях. 

|Шаблон  |Описание  |
|---------|---------|
| [variable definitions](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variables.json) | Демонстрирует различные типы переменных. Шаблон не развертывает ресурсы. При его помощи только создаются и возвращаются значения переменных. |
| [configuration variable](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variablesconfigurations.json) | Демонстрирует использование переменной, определяющей значения конфигурации. Шаблон не развертывает ресурсы. При его помощи только создаются и возвращаются значения переменных. |
| [network security rules](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) и [parameter file](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json) | Отвечает за создание массива в правильном формате для назначения правил безопасности в группе безопасности сети. |


## <a name="functions"></a>Функции

В шаблоне можно создать свои собственные функции. Эти функции доступны для использования в шаблоне. Как правило, определяется сложное выражение, которое вы не хотите повторять в шаблоне. Создаются определяемые пользователем функции на основе выражений и [функции](resource-group-template-functions.md), которые поддерживаются в шаблонах.

При определении пользовательской функции есть несколько ограничений:

* Функция не может обращаться к переменным.
* Функция может использовать только параметры, определенные в самой функции. При использовании [функции Parameters](resource-group-template-functions-deployment.md#parameters) в определяемой пользователем функции вы можете использовать только параметры этой функции.
* Функция не может вызывать другие функции, определяемые пользователем.
* Для функции нельзя использовать [ссылочную функцию](resource-group-template-functions-resource.md#reference).
* Для параметров этой функции нельзя задавать значения по умолчанию.

Для функции требуется значение пространства имен, чтобы избежать конфликтов именования с функциями шаблона. В следующем примере показана функция, которая возвращает имя учетной записи хранения:

```json
"functions": [
  {
    "namespace": "contoso",
    "members": {
      "uniqueName": {
        "parameters": [
          {
            "name": "namePrefix",
            "type": "string"
          }
        ],
        "output": {
          "type": "string",
          "value": "[concat(toLower(parameters('namePrefix')), uniqueString(resourceGroup().id))]"
        }
      }
    }
  }
],
```

Вызов функции выполняется с помощью следующего кода:

```json
"resources": [
  {
    "name": "[contoso.uniqueName(parameters('storageNamePrefix'))]",
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2016-01-01",
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "location": "South Central US",
    "tags": {},
    "properties": {}
  }
]
```

## <a name="resources"></a>Ресурсы
В разделе resources определяются ресурсы, которые развертываются или обновляются.

### <a name="available-properties"></a>Доступные свойства

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

| Имя элемента | Обязательное значение | Описание |
|:--- |:--- |:--- |
| условие | Нет | Логическое значение, указывающее, будет ли подготавливаться ресурс во время этого развертывания. Если установлено значение `true`, при развертывании создается ресурс. Если установлено значение `false`, при развертывании ресурс не создается. См. раздел [условное развертывание](conditional-resource-deployment.md). |
| apiVersion |Да |Версия REST API, которая будет использована для создания ресурса. Чтобы определить доступные значения, см. раздел [Справочник по шаблону](/azure/templates/). |
| type |Да |Тип ресурса. Это значение представляет собой сочетание пространства имен поставщика ресурсов и типа ресурса (например, **Microsoft.Storage/storageAccounts**). Чтобы определить доступные значения, см. раздел [Справочник по шаблону](/azure/templates/). Для дочернего ресурса формат типа зависит от того, является ли он вложенным в родительский ресурс или определен за пределами родительского ресурса. См. о [настройке имени и типа дочернего ресурса](child-resource-name-type.md). |
| name |Да |Имя ресурса. Имя должно соответствовать ограничениям компонентов URI, определенным в RFC3986. Кроме того, службы Azure, которые предоставляют имя ресурса внешним пользователям, проверяют это имя, чтобы убедиться, что это не попытка подделки другого удостоверения. Для дочернего ресурса формат имени зависит от того, является ли он вложенным в родительский ресурс или определен за пределами родительского ресурса. См. о [настройке имени и типа дочернего ресурса](child-resource-name-type.md). |
| расположение |Varies |Поддерживаемые географические расположения указанного ресурса. Вы можете выбрать любое из доступных расположений. Но обычно имеет смысл выбрать расположение, которое находится недалеко от пользователей. Кроме того, целесообразно разместить взаимодействующие ресурсы в одном регионе. Большинству типов ресурсов нужно расположение, но некоторым типам (например, назначению роли) оно не требуется. См. раздел [Задание расположения ресурса](resource-location.md) . |
| теги |Нет |Теги, связанные с ресурсом. Примените теги, чтобы логически организовать ресурсы в подписке. |
| комментарии |Нет |Заметки по ресурсам в шаблоне. Дополнительные сведения см. в разделе [комментариев в шаблонах](resource-group-authoring-templates.md#comments). |
| копировать |Нет |Количество создаваемых ресурсов (если нужно несколько экземпляров). Параллельный режим используется по умолчанию. Используйте последовательный режим, если вы не хотите развертывать все ресурсы одновременно. Дополнительные сведения см. в статье [Создание нескольких экземпляров ресурсов в Azure Resource Manager](resource-group-create-multiple.md). |
| dependsOn |Нет |Ресурсы, которые должны быть развернуты перед развертыванием этого ресурса. Resource Manager оценивает зависимости между ресурсами и развертывает эти ресурсы в правильном порядке. Если ресурсы не зависят друг от друга, они развертываются параллельно. Значение может представлять собой разделенный запятыми список имен ресурсов или уникальных идентификаторов ресурсов. Выводится только список ресурсов, развертываемых в этом шаблоне. Ресурсы, которые не определены в этом шаблоне, уже должны существовать. Избегайте добавления ненужных зависимостей, так как это может замедлить развертывание и привести к созданию циклических зависимостей. Рекомендации по настройке зависимостей см. в статье [Определение зависимостей в шаблонах диспетчера ресурсов Azure](resource-group-define-dependencies.md). |
| свойства |Нет |Параметры конфигурации ресурса. Значения свойств совпадают со значениями, указываемыми в тексте запроса для операции REST API (метод PUT) для создания ресурса. Кроме того, можно указать массив copy для создания нескольких экземпляров свойства. Чтобы определить доступные значения, см. раздел [Справочник по шаблону](/azure/templates/). |
| sku | Нет | В некоторых ресурсах допускается использовать значения, определяющие номер SKU для развертывания. Например, можно указать тип избыточности для учетной записи хранения. |
| kind | Нет | В некоторых ресурсах допускается использовать значение, которое определяет тип развертываемого ресурса. Например, можно указать в качестве типа создаваемой базы данных Cosmos DB. |
| План | Нет | В некоторых ресурсах допускается использовать значения, определяющие номер плана для развертывания. Например, можно указать образ Marketplace для виртуальной машины. | 
| ресурсы |Нет |Дочерние ресурсы, которые зависят от определяемого ресурса. Следует указать только те типы ресурсов, которые разрешены в схеме родительского ресурса. Зависимость от родительского ресурса не подразумевается. Ее необходимо определить явным образом. См. о [настройке имени и типа дочернего ресурса](child-resource-name-type.md). |

### <a name="resource-names"></a>Имена ресурсов

Обычно в Resource Manager вы работаете с именами ресурсов трех типов:

* имена ресурсов, которые должны быть уникальными;
* имена ресурсов, которые могут не быть уникальными, но должны помогать в определении ресурса;
* имена ресурсов, которые могут быть универсальными.

Укажите **уникальное имя ресурса** для любого типа ресурса, имеющего конечную точку доступа к данным. Ниже приведено несколько распространенных типов ресурсов, для которых требуются уникальные имена:

* служба хранилища Azure<sup>1</sup>; 
* веб-приложения службы приложений Azure;
* SQL Server
* Хранилище Azure Key Vault
* Кэш Azure для Redis
* Пакетная служба Azure
* Диспетчер трафика Azure
* Поиск Azure
* Azure HDInsight

<sup>1</sup> Имена учетных записей хранения также должны содержать до 24 знаков в нижнем регистре без дефисов.

При задании имени можно вручную создать уникальное имя или использовать для этого функцию [uniqueString()](resource-group-template-functions-string.md#uniquestring). Вам также может потребоваться добавить префикс или суффикс к результату **uniqueString**. Измените уникальное имя, чтобы было проще определить тип ресурса по его имени. Например, можно создать уникальное имя учетной записи хранения с помощью следующей переменной:

```json
"variables": {
  "storageAccountName": "[concat(uniqueString(resourceGroup().id),'storage')]"
}
```

Для некоторых типов ресурсов может потребоваться указать **имя для идентификации**, но имя не обязательно должно быть уникальным. Для этих типов ресурсов укажите имя, описывающее его использование или характеристики.

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

Для типов ресурсов, доступ к которым осуществляется в основном через другой ресурс, можно использовать **универсальное имя** , жестко запрограммированное в шаблоне. Например, можно задать стандартное универсальное имя для правил брандмауэра на SQL Server:

```json
{
  "type": "firewallrules",
  "name": "AllowAllWindowsAzureIps",
  ...
}
```

## <a name="outputs"></a>Выходные данные

В разделе выходных данных следует указать значения, которые возвращаются после развертывания. Как правило, значения возвращаются из ресурсов, которые были развернуты.

### <a name="available-properties"></a>Доступные свойства

В следующем примере показана структура определения выходных данных:

```json
"outputs": {
  "<outputName>" : {
    "condition": "<boolean-value-whether-to-output-value>",
    "type" : "<type-of-output-value>",
    "value": "<output-value-expression>"
  }
}
```

| Имя элемента | Обязательное значение | Описание |
|:--- |:--- |:--- |
| outputName |Да |Имя выходного значения. Должно быть допустимым идентификатором JavaScript. |
| условие |Нет | Логическое значение, которое указывает, возвращается ли выходное значение. Если установлено значение `true`, то при развертывании значение является частью выходных данных. Если установлено значение `false`, при развертывании выходное значение не создается. Когда не задано, по умолчанию используется значение `true`. |
| type |Да |Тип выходного значения. Выходные значения поддерживает те же типы, что и входные параметры шаблона. Если для типа выходных данных указано значение **SecureString** , оно не отображается в журнале развертывания и не может быть извлечено из другого шаблона. Чтобы использовать значение секрета в нескольких шаблонах, сохраните секрет в Key Vault и сослаться на секрет в файле параметров. Дополнительные сведения см. в статье [Использование Azure Key Vault для передачи защищенного значения параметра во время развертывания](resource-manager-keyvault-parameter.md). |
| value |Да |Выражение на языке шаблона, которое вычисляется и возвращается в качестве выходного значения. |

### <a name="define-and-use-output-values"></a>Определение и использование выходных значений

В следующем примере показано, как вернуть идентификатор ресурса для общедоступного IP-адреса.

```json
"outputs": {
  "resourceID": {
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

В следующем примере показано, как условно вернуть идентификатор ресурса для общедоступного IP-адреса на основе развертывания нового.

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

В следующем примере показано, как задать IP-адрес в подсистеме балансировки нагрузки, извлекая значение из связанного шаблона.

```json
"publicIPAddress": {
  "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

Вы не можете использовать функцию `reference` в разделе выходных данных [вложенного шаблона](resource-group-linked-templates.md#link-or-nest-a-template). Чтобы извлечь значения для развернутого ресурса во вложенном шаблоне, преобразуйте этот шаблон в связанный.

### <a name="output-example-templates"></a>Примеры шаблонов выходных данных

|Шаблон  |Описание  |
|---------|---------|
|[Копирование переменных](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) | Позволяет создать сложные переменные и вывести эти значения. Не используется для развертывания ресурсов. |
|[Общедоступный IP-адрес](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) | Позволяет создать общедоступный IP-адрес и вывести идентификатор ресурса. |
|[Подсистема балансировки нагрузки](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) | Позволяет создать ссылку на предыдущий шаблон. При создании подсистемы балансировки нагрузки использует идентификатор ресурса в выходных данных. |


<a id="comments" />

## <a name="comments-and-metadata"></a>Комментарии и метаданные

Есть несколько вариантов добавления комментариев и метаданных к шаблону.

Вы можете добавить объект `metadata` практически в любом месте шаблона. Resource Manager игнорирует объект, но ваш редактор JSON может предупредить вас, что свойство недопустимо. Определите необходимые свойства в объекте.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "metadata": {
    "comments": "This template was developed for demonstration purposes.",
    "author": "Example Name"
  },
```

В разделе **параметров** добавьте объект `metadata` со свойством `description`.

```json
"parameters": {
  "adminUsername": {
    "type": "string",
    "metadata": {
      "description": "User name for the Virtual Machine."
    }
  },
```

При развертывании шаблона через портал текст, который вы указываете в описании, автоматически используется в качестве подсказки для этого параметра.

![Отображается подсказка для параметра](./media/resource-group-authoring-templates/show-parameter-tip.png)

В разделе **ресурсов** добавьте элемент `comments` или объект метаданных. В следующем примере показан и элемент комментариев, и объект метаданных.

```json
"resources": [
  {
    "comments": "Storage account used to store VM disks",
    "apiVersion": "2018-07-01",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[concat('storage', uniqueString(resourceGroup().id))]",
    "location": "[parameters('location')]",
    "metadata": {
      "comments": "These tags are needed for policy compliance."
    },
    "tags": {
      "Dept": "[parameters('deptName')]",
      "Environment": "[parameters('environment')]"
    },
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {}
  }
]
```

В разделе **выходных данных** добавьте объект метаданных в выходное значение.

```json
"outputs": {
  "hostname": {
    "type": "string",
    "value": "[reference(variables('publicIPAddressName')).dnsSettings.fqdn]",
    "metadata": {
      "comments": "Return the fully qualified domain name"
    }
  },
```

Добавить объект метаданных в определяемые пользователем функции невозможно.

Для встроенных комментариев можно использовать `//`, но этот синтаксис поддерживается не во всех инструментах. Вы не можете использовать интерфейс командной строки Azure для развертывания шаблона со встроенными комментариями, а также применять редактор шаблонов на портале для работы с шаблонами со встроенными комментариями. Если вы добавляете этот стиль комментариев, убедитесь, что используемые инструменты поддерживают встроенные комментарии JSON.

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[parameters('location')]", //defaults to resource group location
  "apiVersion": "2018-10-01",
  "dependsOn": [ // storage account and network interface must be deployed first
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

В Visual Studio Code вы можете установить языковой режим, поддерживающий JSON-файл с комментариями. В таком случае встроенные комментарии больше не будут помечены как недопустимые. Чтобы изменить режим:

1. Откройте раздел выбора языкового режима, нажав клавиши CTRL+K+M.

1. Выберите **JSON with Comments** (JSON с комментариями).

   ![Выбор языкового режима](./media/resource-group-authoring-templates/select-json-comments.png)

[!INCLUDE [arm-tutorials-quickstarts](../../includes/resource-manager-tutorials-quickstarts.md)]

## <a name="next-steps"></a>Следующие шаги
* Полные шаблоны для различных типов решений доступны на странице [Шаблоны быстрого запуска Azure](https://azure.microsoft.com/documentation/templates/).
* Дополнительные сведения о функциях, которые можно использовать в шаблонах, см. в статье [Функции шаблонов Azure Resource Manager](resource-group-template-functions.md).
* Инструкции по объединению нескольких шаблонов при развертывании см. в статье [Использование связанных шаблонов в Azure Resource Manager](resource-group-linked-templates.md).
* Дополнительные рекомендации по созданию шаблонов см. в статье [Рекомендации по работе с шаблонами Azure Resource Manager](template-best-practices.md).
* Рекомендации по созданию шаблонов Resource Manager, которые можно использовать для Azure Stack и всех окружений Azure, см. в статье [Разработка шаблонов Azure Resource Manager для обеспечения согласованности с облаком](templates-cloud-consistency.md).
