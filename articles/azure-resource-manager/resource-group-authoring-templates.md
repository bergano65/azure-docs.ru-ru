---
title: Структура и синтаксис шаблона Azure Resource Manager | Документация Майкрософт
description: Описывается создание структуры и свойств шаблонов Azure Resource Manager с помощью декларативного синтаксиса JSON.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.assetid: 19694cb4-d9ed-499a-a2cc-bcfc4922d7f5
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/14/2019
ms.author: tomfitz
ms.openlocfilehash: 34f34545e4511c4f8bc4af95f906f2871480bd47
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/15/2019
ms.locfileid: "56310169"
---
# <a name="understand-the-structure-and-syntax-of-azure-resource-manager-templates"></a>Описание структуры и синтаксиса шаблонов Azure Resource Manager

В этой статье описана структура шаблона Azure Resource Manager. Статья содержит информацию о разных разделах шаблона и свойствах, которые доступны в этих разделах. Шаблон состоит из JSON и выражений, на основе которых можно создавать значения для развертывания. Пошаговое руководство по созданию шаблона приведено в разделе [Создание первого шаблона Azure Resource Manager](resource-manager-create-first-template.md).

## <a name="template-format"></a>Формат шаблона

Шаблон с самой простой структурой содержит следующие элементы:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "",
    "parameters": {  },
    "variables": {  },
    "functions": [  ],
    "resources": [  ],
    "outputs": {  }
}
```

| Имя элемента | Обязательно | ОПИСАНИЕ |
|:--- |:--- |:--- |
| $schema |Yes |Расположение файла схемы JSON, который описывает версию языка шаблона.<br><br> Для развертывания групп ресурсов используйте `https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#`<br><br>Для развертывания подписки используйте `https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#` |
| contentVersion |Yes |Версия шаблона (например, 1.0.0.0). Для этого элемента можно предоставить любое значение. Это значение позволяет задокументировать важные изменения в шаблоне. При развертывании ресурсов с помощью шаблона это значение позволяет убедиться в том, что используется нужный шаблон. |
| parameters |Нет  |Значения, которые предоставляются при выполнении развертывания для настройки развертывания ресурсов. |
| variables |Нет  |Значения, используемые в виде фрагментов JSON в шаблоне для упрощения выражений на языке шаблона. |
| functions |Нет  |Определяемые пользователем функции, доступные в шаблоне. |
| ресурсов |Yes |Типы ресурсов, которые развертываются или обновляются в группе ресурсов или подписке. |
| outputs |Нет  |Значения, возвращаемые после развертывания. |

Каждый элемент содержит свойства, которые можно задать. В следующем примере приведен полный синтаксис шаблона:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "",
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
    },
    "variables": {
        "<variable-name>": "<variable-value>",
        "<variable-object-name>": {
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
    },
    "functions": [
      {
        "namespace": "<namespace-for-your-function>",
        "members": {
          "<function-name>": {
            "parameters": [
              {
                "name": "<parameter-name>",
                "type": "<type-of-parameter-value>"
              }
            ],
            "output": {
              "type": "<type-of-output-value>",
              "value": "<function-expression>"
            }
          }
        }
      }
    ],
    "resources": [
      {
          "condition": "<boolean-value-whether-to-deploy>",
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
              "count": "<number-of-iterations>",
              "mode": "<serial-or-parallel>",
              "batchSize": "<number-to-deploy-serially>"
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
          "resources": [
              "<array-of-child-resources>"
          ]
      }
    ],
    "outputs": {
        "<outputName>" : {
            "condition": "<boolean-value-whether-to-output-value>",
            "type" : "<type-of-output-value>",
            "value": "<output-value-expression>"
        }
    }
}
```

В этой статье подробнее описаны разделы шаблона.

## <a name="syntax"></a>Синтаксис

Базовый синтаксис шаблона — это JSON. Тем не менее выражения и функции расширяют значения JSON, доступные в шаблоне.  Выражения записываются в строковых литералах JSON, первым и последним знаком которых являются квадратные скобки: `[` и `]` соответственно. Значение выражения вычисляется при развертывании шаблона. Хотя результат вычисления выражения и записывается как строковый литерал, он может иметь другой тип JSON, например массив или целое число, в зависимости от фактического выражения.  Чтобы строковый литерал, начинающийся с квадратной скобки (`[`), не интерпретировался как выражение, добавьте дополнительную скобку, чтобы строка начиналась со знака `[[`.

Как правило, выражения используются с функциями для выполнения операций по настройке развертывания. Как и в языке JavaScript, вызовы функций форматируются так: `functionName(arg1,arg2,arg3)`. Обращение к свойствам производится с помощью точки и операторов [index].

В следующем примере показано, как использовать несколько функций при создании значения:

```json
"variables": {
    "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
}
```

Полный список функций шаблонов см. в статье [Функции шаблонов диспетчера ресурсов Azure](resource-group-template-functions.md). 

## <a name="parameters"></a>Параметры

В разделе параметров шаблона указываются значения, которые вы можете вводить во время развертывания ресурсов. Значения этих параметров позволяют настраивать развертывание путем предоставления значений, предназначенных для конкретной среды (например, для среды разработки, тестирования и рабочей среды). Задавать параметры в шаблоне не обязательно, однако без них шаблон всегда будет развертывать одни и те же ресурсы с одинаковыми именами, расположениями и свойствами.

В следующем примере показано определение простого параметра:

```json
"parameters": {
  "siteNamePrefix": {
    "type": "string",
    "metadata": {
      "description": "The name prefix of the web app that you wish to create."
    }
  },
},
```

Дополнительные сведения об определении параметров см. в описании [шаблонов Azure Resource Manager](resource-manager-templates-parameters.md).

## <a name="variables"></a>Переменные

В разделе переменных вы создаете значения, которые можно использовать в разных частях шаблона. Переменные определять не обязательно, однако они часто упрощают шаблон, снижая число сложных выражений.

В следующем примере показано определение простой переменной:

```json
"variables": {
  "webSiteName": "[concat(parameters('siteNamePrefix'), uniqueString(resourceGroup().id))]",
},
```

Дополнительные сведения об определении переменных см. в описании [шаблонов Azure Resource Manager](resource-manager-templates-variables.md).

## <a name="functions"></a>Функции Azure

В шаблоне можно создать свои собственные функции. Эти функции доступны для использования в шаблоне. Как правило, определяется сложное выражение, которое вы не хотите повторять в шаблоне. Создаются определяемые пользователем функции на основе выражений и [функции](resource-group-template-functions.md), которые поддерживаются в шаблонах.

При определении пользовательской функции есть несколько ограничений:

* Функция не может обращаться к переменным.
* Функция может использовать только параметры, определенные в самой функции. При использовании функции [parameters](resource-group-template-functions-deployment.md#parameters) вместе с функцией, определенной пользователем, вы ограничены параметрами этой функции.
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
В разделе resources определяются ресурсы, которые развертываются или обновляются. Этот раздел может еще больше усложниться, так как вы должны понимать принципы работы развертываемых типов, чтобы предоставить правильные значения.

```json
"resources": [
  {
    "apiVersion": "2016-08-01",
    "name": "[variables('webSiteName')]",
    "type": "Microsoft.Web/sites",
    "location": "[resourceGroup().location]",
    "properties": {
      "serverFarmId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.Web/serverFarms/<plan-name>"
    }
  }
],
```

Для условного включения или исключения ресурса во время развертывания используйте [Условие](resource-manager-templates-resources.md#condition). Дополнительные сведения о ресурсах см. в статье [Раздел Resources в шаблонах Azure Resource Manager](resource-manager-templates-resources.md).

## <a name="outputs"></a>outputs
В разделе выходных данных следует указать значения, которые возвращаются после развертывания. Например, можно возвращать URI для доступа к развернутому ресурсу.

```json
"outputs": {
  "newHostName": {
    "type": "string",
    "value": "[reference(variables('webSiteName')).defaultHostName]"
  }
}
```

Дополнительные сведения о выходных данных см. в описании [шаблонов Azure Resource Manager](resource-manager-templates-outputs.md).

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

## <a name="template-limits"></a>Ограничения шаблонов

Задайте максимальный размер шаблона, равный 1 МБ, а максимальный размер каждого файла параметров — 64 КБ. Ограничение в 1 МБ применяется к конечному состоянию шаблона после расширения с использованием итеративных определений ресурсов, а также значений переменных и параметров. 

Кроме того, ограничение распространяется на:

* 256 параметров;
* 256 переменных;
* 800 ресурсов (включая число копий);
* 64 выходных значения;
* 24 576 знаков в выражении шаблона.

Некоторые ограничения можно превысить, используя вложенные шаблоны. Дополнительные сведения см. разделе [Использование связанных шаблонов в при развертывании ресурсов Azure](resource-group-linked-templates.md). Чтобы уменьшить число параметров, переменных или выходных данных, можно объединить несколько значений в объект. См. дополнительные сведения об [использовании объектов как параметров](resource-manager-objects-as-parameters.md).

[!INCLUDE [arm-tutorials-quickstarts](../../includes/resource-manager-tutorials-quickstarts.md)]

## <a name="next-steps"></a>Дополнительная информация
* Полные шаблоны для различных типов решений доступны на странице [Шаблоны быстрого запуска Azure](https://azure.microsoft.com/documentation/templates/).
* Дополнительные сведения о функциях, которые можно использовать в шаблонах, см. в статье [Функции шаблонов Azure Resource Manager](resource-group-template-functions.md).
* Инструкции по объединению нескольких шаблонов при развертывании см. в статье [Использование связанных шаблонов в Azure Resource Manager](resource-group-linked-templates.md).
* Дополнительные рекомендации по созданию шаблонов см. в статье [Рекомендации по работе с шаблонами Azure Resource Manager](template-best-practices.md).
* Рекомендации по созданию шаблонов Resource Manager, которые можно использовать для Azure Stack и всех окружений Azure, см. в статье [Разработка шаблонов Azure Resource Manager для обеспечения согласованности с облаком](templates-cloud-consistency.md).
