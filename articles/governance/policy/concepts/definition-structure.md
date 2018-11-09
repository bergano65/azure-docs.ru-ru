---
title: Структура определения службы "Политика Azure"
description: Описывается, как определение политики ресурсов, описывающее условия применения данной политики и соответствующий эффект, используется службой "Политика Azure", чтобы установить соглашения о ресурсах в вашей организации.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: 0ff56b86243956d1fa6b51a6dfd14af9e00d8367
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/29/2018
ms.locfileid: "50212783"
---
# <a name="azure-policy-definition-structure"></a>Структура определения службы "Политика Azure"

Определение политики ресурсов, используемое службой "Политика Azure" и описывающее условия применения данной политики и соответствующий эффект, позволяет установить соглашения о ресурсах в вашей организации. Это соглашения помогут вам контролировать расходы и управлять ресурсами. Например, можно указать, что разрешены только определенные типы виртуальных машин. Кроме того, можно требовать наличие определенного тега для каждого ресурса. Политики наследуются всеми дочерними ресурсами. Это значит, что политики, применяемые к группе ресурсов, применяются также ко всем ресурсам в этой группе.

Схему, используемую службой "Политика Azure", можно найти здесь: [https://schema.management.azure.com/schemas/2018-05-01/policyDefinition.json](https://schema.management.azure.com/schemas/2018-05-01/policyDefinition.json)

Для создания определения политики используется JSON. Определение политики содержит следующие элементы:

- mode;
- parameters
- display name
- description
- policy rule
  - logical evaluation
  - effect

В следующем примере JSON показана политика, которая налагает ограничения на расположения для развертывания ресурсов.

```json
{
    "properties": {
        "mode": "all",
        "parameters": {
            "allowedLocations": {
                "type": "array",
                "metadata": {
                    "description": "The list of locations that can be specified when deploying resources",
                    "strongType": "location",
                    "displayName": "Allowed locations"
                }
            }
        },
        "displayName": "Allowed locations",
        "description": "This policy enables you to restrict the locations your organization can specify when deploying resources.",
        "policyRule": {
            "if": {
                "not": {
                    "field": "location",
                    "in": "[parameters('allowedLocations')]"
                }
            },
            "then": {
                "effect": "deny"
            }
        }
    }
}
```

Все примеры политик Azure см. [здесь](../samples/index.md).

## <a name="mode"></a>Mode

**Режим** определяет типы ресурсов, которые будут оцениваться для политики. Ниже приведены поддерживаемые режимы.

- `all`: оценка групп ресурсов и всех типов ресурсов.
- `indexed`: оцениваются только типы ресурсов, которые поддерживают теги и расположение.

В большинстве случаев рекомендуется задать для параметра **mode** значение `all`. Во всех определениях политик, создаваемых на портале, используется режим `all`. Если используется PowerShell или Azure CLI, необходимо указать параметр **mode** вручную. Если в определении политики не задано значение для параметра **mode**, оно считается значением по умолчанию для `all` в Azure PowerShell и для `null` в Azure CLI, что эквивалентно `indexed` для обратной совместимости.

`indexed` следует использовать при создании политик, которые будут принудительно применять теги или расположения. Это не обязательно, но помешает отображению ресурсов, которые не поддерживают теги и расположения, в качестве несоответствующих в результатах проверки соответствия. Единственным исключением являются **группы ресурсов**. В политиках, которые пытаются принудительно применить расположение или теги к группе ресурсов, следует задать для параметра **mode** значение `all` и явно указать целевой тип `Microsoft.Resources/subscriptions/resourceGroup`. Пример см. в статье о [принудительном применении тегов к группам ресурсов](../samples/enforce-tag-rg.md).

## <a name="parameters"></a>Параметры

Параметры помогают упростить управление политиками за счет сокращения числа определений политик. Параметры можно рассматривать как поля в форме: `name`, `address`, `city`, `state`. Эти параметры никогда не меняются, однако их значения изменяются в зависимости от того, как пользователь заполняет форму.
Точно так же параметры работают при создании политик. Добавив параметры в определение политики, вы сможете повторно использовать ее в различных сценариях, указывая разные значения.

> [!NOTE]
> Определение параметров для политики или инициативы может быть настроено только при первоначальном их создании. Определение параметров не может быть изменено позже.
> Это предотвращает косвенное использование существующих назначений политики или инициативы.

Например, можно определить политику для свойства ресурса, чтобы ограничить расположения, в которых могут развертываться ресурсы. В этом случае при создании политики можно объявить следующие параметры.

```json
"parameters": {
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        }
    }
}
```

Типом параметра может быть строка или массив. Свойство метаданных используется в таких инструментах, как портал Azure, для отображения полезных для пользователя сведений.

В свойстве метаданных можно использовать **strongType**, чтобы предоставить список с множественным выбором параметров на портале Azure. К допустимым значениям для **strongType** относится следующее:

- `"location"`
- `"resourceTypes"`
- `"storageSkus"`
- `"vmSKUs"`
- `"existingResourceGroups"`
- `"omsWorkspace"`

В правилах политики полученные параметры используются со следующим синтаксисом функции для значения развертывания `parameters`:

```json
{
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

## <a name="definition-location"></a>Расположение определения

При создании определения инициативы или политики важно указывать определение расположения.

Расположение определения указывает область, которой можно назначить определение инициативы или политики. Расположение может быть задано как группа управления или подписка.

> [!NOTE]
> Если вы планируете применять это определение политики к нескольким подпискам, расположение должно быть группой управления, содержащей подписки, которым вы назначите инициативу или политику.

## <a name="display-name-and-description"></a>Отображаемое имя и описание

Параметры **displayName** и **description** позволяют идентифицировать определение политики и описать контекст для ее использования.

## <a name="policy-rule"></a>Правило политики

Правило политики состоит из блоков **If** и **Then**. В блоке **If** указываются одно или несколько условий. Они определяют, когда применяется эта политика. В этих условиях можно использовать логические операторы, чтобы точно определить сценарии для использования политики.

В блоке **Then** описываются результаты, которые вступают в силу при соблюдении условий из блока **If**.

```json
{
    "if": {
        <condition> | <logical operator>
    },
    "then": {
        "effect": "deny | audit | append | auditIfNotExists | deployIfNotExists"
    }
}
```

### <a name="logical-operators"></a>Логические операторы

Ниже перечислены поддерживаемые логические операторы.

- `"not": {condition  or operator}`
- `"allOf": [{condition or operator},{condition or operator}]`
- `"anyOf": [{condition or operator},{condition or operator}]`

Оператор **not** инвертирует результат условия. Оператор **allOf** действует как логическая операция **And**, то есть требует соблюдения всех входящих в него условий. Оператор **anyOf** действует как логическая операция **Or**, то есть проверяет соблюдение хотя бы одного из входящих в него условий.

Допускается вложение логических операторов. В следующем примере представлена операция **not**, вложенная в операцию **allOf**.

```json
"if": {
    "allOf": [{
            "not": {
                "field": "tags",
                "containsKey": "application"
            }
        },
        {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
        }
    ]
},
```

### <a name="conditions"></a>Условия

Условие определяет, соответствует ли свойство **field** определенным параметрам. Поддерживаются такие условия:

- `"equals": "value"`
- `"notEquals": "value"`
- `"like": "value"`
- `"notLike": "value"`
- `"match": "value"`
- `"notMatch": "value"`
- `"contains": "value"`
- `"notContains": "value"`
- `"in": ["value1","value2"]`
- `"notIn": ["value1","value2"]`
- `"containsKey": "keyName"`
- `"notContainsKey": "keyName"`
- `"exists": "bool"`

При использовании условий **like** и **notLike** можно указать в значении подстановочный знак `*`.
Значение не должно содержать более 1 подстановочного знака `*`.

При использовании условий **match** и **notMatch** укажите `#` для представления цифры, `?` для представления буквы, `.` для сопоставления всех символов и любой другой символ для представления соответствующего фактического символа. Примеры см. в статье [Разрешение на использование нескольких шаблонов имен](../samples/allow-multiple-name-patterns.md).

### <a name="fields"></a>Поля

Условия создаются на основе полей. Поле представляет свойства в полезных данных запроса ресурса, используемые для описания состояния ресурса.

Поддерживаются следующие поля.

- `name`
- `fullName`
  - Возвращает полное имя ресурса. Полное имя ресурса — это имя ресурса, к которому добавлены любые имена родительских ресурсов (например, myServer/myDatabase).
- `kind`
- `type`
- `location`
- `tags`
- `tags.<tagName>`
  - **\<tagName\>** — это имя тега для проверки условия.
  - Пример: `tags.CostCenter`. **CostCenter** — это имя тега.
- `tags[<tagName>]`
  - Этот синтаксис в скобках поддерживает имена тегов, которые содержат точки.
  - **\<tagName\>** — это имя тега для проверки условия.
  - Пример: `tags[Acct.CostCenter]`. **Acct.CostCenter** — это имя тега.
- Список псевдонимов свойств указан в разделе [Псевдонимы](#aliases).

### <a name="effect"></a>Результат

Политика поддерживает следующие типы действий:

- **Deny** записывает событие в журнал действий и отвергает запрос;
- **Audit** записывает предупреждение в журнал действий, но не отвергает запрос;
- **Append** добавляет в запрос некоторый набор полей;
- **AuditIfNotExists** включает аудит, если ресурс не существует;
- **DeployIfNotExists** развертывает ресурс, если он еще не существует.

Для типа **append**необходимо указать следующие сведения:

```json
"effect": "append",
"details": [{
    "field": "field name",
    "value": "value of the field"
}]
```

Значением может быть строка или объект формата JSON.

С помощью параметров **AuditIfNotExists** и **DeployIfNotExists** можно проверить существование связанного ресурса и применить правило и соответствующее действие, если такой ресурс не существует. Например можно потребовать, чтобы наблюдатель за сетями был развернут для всех виртуальных сетей. Пример аудита наличия развернутого расширения для виртуальных машин приведен в разделе [Проверка наличия расширения](../samples/audit-ext-not-exist.md).

Подробные сведения о каждом эффекте, порядке оценки и свойствах, а также примеры см. в статье [Действия политик](effects.md).

### <a name="policy-functions"></a>Функции политики

В рамках правила политики можно использовать подмножество [функций шаблона Resource Manager](../../../azure-resource-manager/resource-group-template-functions.md). В настоящее время поддерживаются следующие функции:

- [parameters](../../../azure-resource-manager/resource-group-template-functions-deployment.md#parameters)
- [concat](../../../azure-resource-manager/resource-group-template-functions-array.md#concat)
- [resourceGroup](../../../azure-resource-manager/resource-group-template-functions-resource.md#resourcegroup)
- [subscription](../../../azure-resource-manager/resource-group-template-functions-resource.md#subscription)

Кроме того, для правил политики доступна функция `field`. Эта функция предназначена главным образом для использования с **AuditIfNotExists** и **DeployIfNotExists**, чтобы ссылаться на поля в ресурсе, который оценивается. Это можно увидеть на примере [DeployIfNotExists](effects.md#deployifnotexists-example).

#### <a name="policy-function-examples"></a>Примеры функций политики

В этом примере правила политики функция ресурса `resourceGroup` используется для получения свойства **name** в сочетании с массивом `concat` и функцией объекта для создания условия `like`, требующего, чтобы имя ресурса начиналось с имени группы ресурсов.

```json
{
    "if": {
        "not": {
            "field": "name",
            "like": "[concat(resourceGroup().name,'*')]"
        }
    },
    "then": {
        "effect": "deny"
    }
}
```

В этом примере правила политики функция ресурса `resourceGroup` используется, чтобы получить значение массива свойств **tags** в теге **CostCenter** в группе ресурсов и добавить его в тег **CostCenter** в новом ресурсе.

```json
{
    "if": {
        "field": "tags.CostCenter",
        "exists": "false"
    },
    "then": {
        "effect": "append",
        "details": [{
            "field": "tags.CostCenter",
            "value": "[resourceGroup().tags.CostCenter]"
        }]
    }
}
```

## <a name="aliases"></a>Псевдонимы

Псевдонимы свойств позволяют обращаться к определенным свойствам для типа ресурса. Псевдонимы позволяют ограничить значения или условия, разрешенные для свойства ресурса. Каждый псевдоним сопоставляется с путями в разных версиях API для заданного типа ресурса. Во время оценки политики модуль политики получает путь свойства для этой версии API.

Список псевдонимов постоянно пополняется. Чтобы определить, какие псевдонимы в настоящее время поддерживаются службой "Политика Azure", используйте один из следующих методов:

- Azure PowerShell

  ```azurepowershell-interactive
  # Login first with Connect-AzureRmAccount if not using Cloud Shell

  # Use Get-AzureRmPolicyAlias to list available providers
  Get-AzureRmPolicyAlias -ListAvailable

  # Use Get-AzureRmPolicyAlias to list aliases for a Namespace (such as Azure Automation -- Microsoft.Automation)
  Get-AzureRmPolicyAlias -NamespaceMatch 'automation'
  ```

- Инфраструктура CLI Azure

  ```azurecli-interactive
  # Login first with az login if not using Cloud Shell

  # List namespaces
  az provider list --query [*].namespace

  # Get Azure Policy aliases for a specific Namespace (such as Azure Automation -- Microsoft.Automation)
  az provider show --namespace Microsoft.Automation --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
  ```

- REST API или ARMClient

  ```http
  GET https://management.azure.com/providers/?api-version=2017-08-01&$expand=resourceTypes/aliases
  ```

### <a name="understanding-the--alias"></a>Общие сведения о псевдониме [*]

Некоторые доступные псевдонимы имеют версию с отображаемым именем "Обычный", а другие — с подключенным к ней **[\*]**. Например: 

- `Microsoft.Storage/storageAccounts/networkAcls.ipRules`
- `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]`

Первый пример используется для оценки всего массива, где псевдоним **[\*]** оценивает каждый элемент массива.

В качестве примера рассмотрим правило политики. Эта политика будет **отклонять** учетную запись хранилища, которая настроена на ipRules, и если **none** ipRules имеют значение "127.0.0.1".

```json
"policyRule": {
    "if": {
        "allOf": [{
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
                "exists": "true"
            },
            {
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value",
                "notEquals": "127.0.0.1"
            }
        ]
    },
    "then": {
        "effect": "deny",
    }
}
```

В следующем примере массив **ipRules** выглядит таким образом:

```json
"ipRules": [{
        "value": "127.0.0.1",
        "action": "Allow"
    },
    {
        "value": "192.168.1.1",
        "action": "Allow"
    }
]
```

Вот как происходит обработка в этом примере:

- `networkAcls.ipRules` — Проверяет, имеет ли массив значение, отличное от null. Он возвращает значение true, поэтому вычисление продолжается.

  ```json
  {
    "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
    "exists": "true"
  }
  ```

- `networkAcls.ipRules[*].value` — Проверяет каждое свойство _значения_ в массиве **ipRules**.

  ```json
  {
    "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value",
    "notEquals": "127.0.0.1"
  }
  ```

  - Каждый элемент будет обрабатываться в качестве массива.

    - Значение "127.0.0.1"! = "127.0.0.1" оценивается как false.
    - Значение "127.0.0.1" != "192.168.1.1" оценивается как true.
    - По меньшей мере одно свойство _значения_ в массиве **ipRules** оценивается как false, поэтому вычисление остановится.

Эффект **Deny** не сработает в качестве условия, оцениваемого как false.

## <a name="initiatives"></a>Инициативы

Инициативы дают возможность сгруппировать несколько связанных определений политик, чтобы упростить их назначение и управление ими, так как это позволяет работать с группой как с единым элементом. Например, можно сгруппировать все связанные определения политик тегов в одной инициативе. Вместо назначения каждой политики по отдельности вы сможете применить инициативу.

В следующем примере показано, как создать инициативу для обработки двух тегов, `costCenter` и `productName`. Для применения значения тега по умолчанию используются две встроенные политики.

```json
{
    "properties": {
        "displayName": "Billing Tags Policy",
        "policyType": "Custom",
        "description": "Specify cost Center tag and product name tag",
        "parameters": {
            "costCenterValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for Cost Center tag"
                }
            },
            "productNameValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for product Name tag"
                }
            }
        },
        "policyDefinitions": [{
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "parameters": {
                    "tagName": {
                        "value": "costCenter"
                    },
                    "tagValue": {
                        "value": "[parameters('costCenterValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
                "parameters": {
                    "tagName": {
                        "value": "costCenter"
                    },
                    "tagValue": {
                        "value": "[parameters('costCenterValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "parameters": {
                    "tagName": {
                        "value": "productName"
                    },
                    "tagValue": {
                        "value": "[parameters('productNameValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
                "parameters": {
                    "tagName": {
                        "value": "productName"
                    },
                    "tagValue": {
                        "value": "[parameters('productNameValue')]"
                    }
                }
            }
        ]
    },
    "id": "/subscriptions/<subscription-id>/providers/Microsoft.Authorization/policySetDefinitions/billingTagsPolicy",
    "type": "Microsoft.Authorization/policySetDefinitions",
    "name": "billingTagsPolicy"
}
```

## <a name="next-steps"></a>Дополнительная информация

- См. другие [примеры шаблонов для службы "Политика Azure"](../samples/index.md).
- См. дополнительные сведения о [действиях политик](effects.md).
- См. сведения о [программном создании политик](../how-to/programmatically-create.md).
- Узнайте о том, как получить [подробные сведения о соответствии](../how-to/getting-compliance-data.md).
- См. сведения об [исправлении несоответствующих ресурсов](../how-to/remediate-resources.md).
- См. дополнительные сведения о группе управления в разделе [Упорядочение ресурсов с помощью групп управления Azure](../../management-groups/overview.md)
