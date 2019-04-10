---
title: Сведения о структуре определения политики
description: Описывается, как определение политики ресурсов, описывающее условия применения данной политики и соответствующий эффект, используется службой "Политика Azure", чтобы установить соглашения о ресурсах в вашей организации.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/13/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: f554be0803041b12dc49a576e8eb737732ec2a80
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59283109"
---
# <a name="azure-policy-definition-structure"></a>Структура определения службы "Политика Azure"

Определения политики ресурсов используются службой "Политика Azure", чтобы настроить соглашения для ресурсов. Каждое определение описывает соответствие ресурсов и действие, которое необходимо предпринять, если ресурс не соответствует требованиям.
Это соглашения помогут вам контролировать расходы и управлять ресурсами. Например, можно указать, что разрешены только определенные типы виртуальных машин. Кроме того, можно требовать наличие определенного тега для каждого ресурса. Политики наследуются всеми дочерними ресурсами. Политика, применяемая к группе ресурсов, применяется также ко всем ресурсам в этой группе.

Схему, используемую службой "Политика Azure", можно найти здесь: [https://schema.management.azure.com/schemas/2018-05-01/policyDefinition.json](https://schema.management.azure.com/schemas/2018-05-01/policyDefinition.json)

Для создания определения политики используется JSON. Определение политики содержит следующие элементы:

- mode;
- параметры
- display name
- описание
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
                },
                "defaultValue": "westus2"
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

[!INCLUDE [az-powershell-update](../../../../includes/updated-for-az.md)]

## <a name="mode"></a>Режим

**Режим** определяет типы ресурсов, которые будут оцениваться для политики. Ниже приведены поддерживаемые режимы.

- `all`: Оценка групп ресурсов и все типы ресурсов
- `indexed`: оцениваются только типы ресурсов, которые поддерживают теги и расположение

В большинстве случаев рекомендуется задать для параметра **mode** значение `all`. Во всех определениях политик, создаваемых на портале, используется режим `all`. Если используется PowerShell или Azure CLI, необходимо указать параметр **mode** вручную. Если определение политики не включает значение **mode**, в Azure PowerShell используется значение по умолчанию `all`, а в Azure CLI — `null`. Режим `null` является тем же, что и при использовании `indexed` для поддержки обратной совместимости.

`indexed` следует использовать при создании политик, которые принудительно применять теги или расположения. Это не обязательно, но помешает отображению ресурсов, которые не поддерживают теги и расположения, в качестве несоответствующих в результатах проверки соответствия. Исключением являются **группы ресурсов**. В политиках, которые принудительно применяют расположение или теги к группе ресурсов, следует задать для параметра **mode** значение `all` и явно указать целевой тип `Microsoft.Resources/subscriptions/resourceGroups`. Пример см. в статье о [принудительном применении тегов к группам ресурсов](../samples/enforce-tag-rg.md). Список ресурсов, которые поддерживают теги, см. в разделе [поддержка для ресурсов Azure тегов](../../../azure-resource-manager/tag-support.md).

## <a name="parameters"></a>Параметры

Параметры помогают упростить управление политиками за счет сокращения числа определений политик. Параметры можно рассматривать как поля в форме: `name`, `address`, `city`, `state`. Эти параметры никогда не меняются, однако их значения изменяются в зависимости от того, как пользователь заполняет форму.
Точно так же параметры работают при создании политик. Добавив параметры в определение политики, вы сможете повторно использовать ее в различных сценариях, указывая разные значения.

> [!NOTE]
> Параметры могут быть добавлены к имеющемуся и назначенному определению. Новый параметр должен включать в себя свойство **defaultValue**. Это предотвращает косвенное использование существующих назначений политики или инициативы.

### <a name="parameter-properties"></a>Свойства параметра

Параметр имеет следующие свойства, которые используются в определении политики:

- **name**. Имя параметра. Используется функцией развертывания `parameters` в правиле политики. Дополнительные сведения см. в разделе [об использовании значения параметра](#using-a-parameter-value).
- `type`: Определяет, является ли параметр строкой (** string **) или массивом (** array).
- `metadata`: Определяет вложенное свойство, которое в основном используется для отображения понятной пользователям информации на портале Azure:
  - `description`: Объясняет назначение параметра. Можно использовать для примеров допустимых значений.
  - `displayName`: Понятное имя параметра, которое отображается на портале.
  - `strongType`: (Необязательно.) Используется при назначении определения политики через портал. Предоставляет список с учетом контекста. См. дополнительные сведения о [вложенном свойстве strongType](#strongtype).
- `defaultValue`: (Необязательно.) Устанавливает значение параметра в назначении, если значение не указано. Это свойство необходимо при обновлении существующего назначенного определения политики.
- `allowedValues`: (Необязательно) Предоставляет массив значений, которые принимает параметр во время назначения.

Например, можно создать определение политики, чтобы ограничить расположения, в которых могут развертываться ресурсы. Для такого определения политики можно использовать параметр **allowedLocations**. Этот параметр будет использоваться каждым назначением определения политики для ограничения допустимых значений. Использование вложенного свойства **strongType** делает назначение через портал более удобным:

```json
"parameters": {
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": "westus2",
        "allowedValues": [
            "eastus2",
            "westus2",
            "westus"
        ]
    }
}
```

### <a name="using-a-parameter-value"></a>Использование значения параметра

В правилах политики полученные параметры используются со следующим синтаксисом функции для значения развертывания `parameters`:

```json
{
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

В этом примере используется параметр **allowedLocations**, который рассматривался в разделе о [свойствах параметра](#parameter-properties).

### <a name="strongtype"></a>strongType

В свойстве `metadata` можно использовать вложенное свойство **strongType**, чтобы предоставить список для выбора параметров на портале Azure. К допустимым значениям для **strongType** относится следующее:

- `"location"`
- `"resourceTypes"`
- `"storageSkus"`
- `"vmSKUs"`
- `"existingResourceGroups"`
- `"omsWorkspace"`

## <a name="definition-location"></a>Расположение определения

При создании инициативы или политики необходимо указывать расположение определения. Расположение определения должно быть задано как группа управления или подписка. Расположение указывает область, которой можно назначить инициативу или политику. Ресурсы должны быть непосредственными членами или дочерними элементами в иерархии расположения определения для назначения.

Если расположение определения является

- **подпиской**, политика может быть назначена только ресурсам в этой подписке;
- **группой управления**, политика может быть назначена только ресурсам в дочерних группах управления и дочерних подписках. Если вы планируете применять определение политики к нескольким подпискам, расположение должно быть группой управления, содержащей эти подписки.

## <a name="display-name-and-description"></a>Отображаемое имя и описание

Параметры **displayName** и **description** позволяют идентифицировать определение политики и описать контекст для ее использования. Максимальная длина **displayName** составляет _128_ символов, а **description** — _512_ символов.

## <a name="policy-rule"></a>Правило политики

Правило политики состоит из блоков **If** и **Then**. В блоке **If** указываются одно или несколько условий. Они определяют, когда применяется эта политика. В этих условиях можно использовать логические операторы, чтобы точно определить сценарии для использования политики.

В блоке **Then** описываются результаты, которые вступают в силу при соблюдении условий из блока **If**.

```json
{
    "if": {
        <condition> | <logical operator>
    },
    "then": {
        "effect": "deny | audit | append | auditIfNotExists | deployIfNotExists | disabled"
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

Условие определяет, соответствует ли метод доступа **field** или **value** определенным критериям. Поддерживаются такие условия:

- `"equals": "value"`
- `"notEquals": "value"`
- `"like": "value"`
- `"notLike": "value"`
- `"match": "value"`
- `"matchInsensitively": "value"`
- `"notMatch": "value"`
- `"notMatchInsensitively": "value"`
- `"contains": "value"`
- `"notContains": "value"`
- `"in": ["value1","value2"]`
- `"notIn": ["value1","value2"]`
- `"containsKey": "keyName"`
- `"notContainsKey": "keyName"`
- `"exists": "bool"`

При использовании условий **like** и **notLike** можно указать в значении подстановочный знак `*`.
Значение не должно содержать более одного подстановочного знака `*`.

При использовании условий **match** и **notMatch** укажите `#` для сопоставления цифры, `?` для представления буквы, `.` для сопоставления всех знаков и любой другой знак для сопоставления соответствующего фактического знака.
В условиях **match** и **notMatch** учитывается регистр. Другие варианты без учета регистра доступны для условий **matchInsensitively** и **notMatchInsensitively**. Примеры см. в статье [Разрешение на использование нескольких шаблонов имен](../samples/allow-multiple-name-patterns.md).

### <a name="fields"></a>Поля

Условия создаются на основе полей. Поле сопоставляет свойства в полезных данных запроса ресурса и описывает состояния ресурса.

Поддерживаются следующие поля.

- `name`
- `fullName`
  - Возвращает полное имя ресурса. Полное имя ресурса — это имя ресурса, к которому добавлены любые имена родительских ресурсов (например, myServer/myDatabase).
- `kind`
- `type`
- `location`
  - Используйте **global** для ресурсов, которые зависят от расположения. Пример см. в статье [Samples - Allowed locations](../samples/allowed-locations.md) (Примеры — разрешенные расположения).
- `identity.type`
  - Возвращает тип [управляемого удостоверения](../../../active-directory/managed-identities-azure-resources/overview.md), включенный в ресурс.
- `tags`
- `tags['<tagName>']`
  - Этот синтаксис в скобках поддерживает имена тегов, которые содержат такие знаки препинания, как дефис, точку или пробел.
  - **\<tagName\>** — это имя тега для проверки условия.
  - Пример: `tags['Acct.CostCenter']`. **Acct.CostCenter** — это имя тега.
- `tags['''<tagName>''']`
  - Этот синтаксис в скобках поддерживает имена тегов, которые содержат апострофы благодаря использованию двойного апострофа.
  - **'\<tagName\>'**  — это имя тега для проверки условия.
  - Пример: `tags['''My.Apostrophe.Tag''']`.**'\<tagName\>'**  — это имя тега.
- Список псевдонимов свойств указан в разделе [Псевдонимы](#aliases).

> [!NOTE]
> `tags.<tagName>`, `tags[tagName]`, и `tags[tag.with.dots]` будут по-прежнему допустимые способы объявления поле тегов.
> Однако предпочтительными являются выражения, указанные выше.

#### <a name="use-tags-with-parameters"></a>Использование тегов с параметрами

Значение параметра передается полю тега. Передача параметра полю тега повышает гибкость определения политики во время ее назначения.

В указанном ниже примере `concat` используется для создания поля подстановки тега для тега со значением параметра **tagName**. Если такой тег не существует, то с помощью эффекта **append** добавляется тег, использующий значение тега с таким же именем, заданным для проверенной родительской группы ресурсов с помощью функции поиска `resourcegroup()`.

```json
{
    "if": {
        "field": "[concat('tags[', parameters('tagName'), ']')]",
        "exists": "false"
    },
    "then": {
        "effect": "append",
        "details": [{
            "field": "[concat('tags[', parameters('tagName'), ']')]",
            "value": "[resourcegroup().tags[parameters('tagName')]]"
        }]
    }
}
```

### <a name="value"></a>Значение

Условия также можно настраивать с помощью **value**. **value** проверяет условия на соответствие [параметрам](#parameters), [поддерживаемым функциям шаблонов](#policy-functions) или литералам.
**value** используется в паре с любым поддерживаемым [условием](#conditions).

> [!WARNING]
> Если результат _функции шаблона_ является ошибкой, произойдет сбой оценки политики. Сбой оценки определяется неявным **запретить**. Дополнительные сведения см. в разделе [избежать сбоев шаблона](#avoiding-template-failures).

#### <a name="value-examples"></a>Примеры значений

В этом примере правила политики используют **value** для сравнения результата функции `resourceGroup()` и возвращенного свойства **name** с условием **like** для `*netrg`. Это правило запрещает любой ресурс, с **типом**, отличающимся от `Microsoft.Network/*`, в любой группе ресурсов, имя которой заканчивается на `*netrg`.

```json
{
    "if": {
        "allOf": [{
                "value": "[resourceGroup().name]",
                "like": "*netrg"
            },
            {
                "field": "type",
                "notLike": "Microsoft.Network/*"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```

В этом примере правила политики используют **value** для проверки того, **равен** ли результат нескольких вложенных функций `true`. Это правило запрещает любой ресурс, в котором нет хотя бы трех тегов.

```json
{
    "mode": "indexed",
    "policyRule": {
        "if": {
            "value": "[less(length(field('tags')), 3)]",
            "equals": true
        },
        "then": {
            "effect": "deny"
        }
    }
}
```

#### <a name="avoiding-template-failures"></a>Избежать сбоев шаблона

Использование _функции шаблона_ в **значение** позволяет много сложных вложенных функций. Если результат _функции шаблона_ является ошибкой, произойдет сбой оценки политики. Сбой оценки определяется неявным **запретить**. Пример **значение** неудачи в определенных сценариях:

```json
{
    "policyRule": {
        "if": {
            "value": "[substring(field('name'), 0, 3)]",
            "equals": "abc"
        },
        "then": {
            "effect": "audit"
        }
    }
}
```

Правила политики в примере выше используется [substring()](../../../azure-resource-manager/resource-group-template-functions-string.md#substring) для сравнения первые три символа из **имя** для **abc**. Если **имя** короче трех символов, `substring()` функции приводит к ошибке. Эта ошибка приводит к политика станет **запретить** эффект.

Вместо этого используйте [if()](../../../azure-resource-manager/resource-group-template-functions-logical.md#if) функции для проверки, если первые три символа из **имя** равно **abc** не позволяя **имя** короче, чем три символа, чтобы вызвать ошибку:

```json
{
    "policyRule": {
        "if": {
            "value": "[if(greaterOrEquals(length(field('name')), 3), substring(field('name'), 0, 3), 'not starting with abc')]",
            "equals": "abc"
        },
        "then": {
            "effect": "audit"
        }
    }
}
```

Измененная политика правило с `if()` проверяет длину **имя** перед попыткой получения `substring()` на значении с менее чем из трех символов. Если **имя** слишком короткий, значение «не запускается с abc» вместо этого возвращается и по сравнению с **abc**. Ресурс с короткое имя, которое не начинается с **abc** по-прежнему не соответствует правилу политики, но больше не приводит к ошибке во время оценки.

### <a name="effect"></a>Эффект

Политика поддерживает следующие типы действий:

- **Deny** записывает событие в журнал действий и отвергает запрос;
- **Audit** записывает предупреждение в журнал действий, но не отвергает запрос;
- **Append** добавляет в запрос некоторый набор полей;
- **AuditIfNotExists** включает аудит, если ресурс не существует;
- **DeployIfNotExists** развертывает ресурс, если он еще не существует;
- **Disabled** не оценивает ресурсы на соответствие правилу политики.

Для типа **append**необходимо указать следующие сведения:

```json
"effect": "append",
"details": [{
    "field": "field name",
    "value": "value of the field"
}]
```

Значением может быть строка или объект формата JSON.

**AuditIfNotExists** и **DeployIfNotExists** проверяют существование связанного ресурса и применяют правило. Если ресурс не соответствует правилу, реализуется эффект. Например можно потребовать, чтобы наблюдатель за сетями был развернут для всех виртуальных сетей. Дополнительные сведения см. в статье [Проверка наличия расширения](../samples/audit-ext-not-exist.md).

Действие **DeployIfNotExists** требует свойства **roleDefinitionId** в части **details** правила политики. Дополнительные сведения см. в разделе [Настройка определения политики](../how-to/remediate-resources.md#configure-policy-definition).

```json
"details": {
    ...
    "roleDefinitionIds": [
        "/subscription/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
        "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
    ]
}
```

Подробные сведения о каждом эффекте, порядке оценки и свойствах, а также примеры см. в статье [Действия политик](effects.md).

### <a name="policy-functions"></a>Функции политики

В рамках правила политики можно использовать все [функции шаблона Resource Manager](../../../azure-resource-manager/resource-group-template-functions.md), кроме следующих:

- copyIndex()
- deployment()
- list*
- providers()
- reference()
- resourceId()
- variables()

Кроме того, для правил политики доступна функция `field`. `field` Чаще всего используется со **AuditIfNotExists** и **DeployIfNotExists** поля ссылки на ресурс, которые вычисляются. Это можно увидеть на [примере DeployIfNotExists](effects.md#deployifnotexists-example).

#### <a name="policy-function-example"></a>Пример функции политики

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

## <a name="aliases"></a>Псевдонимы

Псевдонимы свойств позволяют обращаться к определенным свойствам для типа ресурса. Псевдонимы позволяют ограничить значения или условия, разрешенные для свойства ресурса. Каждый псевдоним сопоставляется с путями в разных версиях API для заданного типа ресурса. Во время оценки политики модуль политики получает путь свойства для этой версии API.

Список псевдонимов постоянно пополняется. Чтобы узнать, какие псевдонимы в настоящее время поддерживаются службой "Политика Azure", используйте один из следующих методов:

- Azure PowerShell

  ```azurepowershell-interactive
  # Login first with Connect-AzAccount if not using Cloud Shell

  # Use Get-AzPolicyAlias to list available providers
  Get-AzPolicyAlias -ListAvailable

  # Use Get-AzPolicyAlias to list aliases for a Namespace (such as Azure Automation -- Microsoft.Automation)
  Get-AzPolicyAlias -NamespaceMatch 'automation'
  ```

- Azure CLI

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

«Normal» псевдоним представляет поле как одно значение. Данное поле используется для сценариев сравнения точное совпадение, если весь набор значений должен содержать ровно, как определено, не больше и не меньше. С помощью **ipRules**, пример будет проверка существовании точный набор правил, включая количество правил и состав каждого правила. Это правило пример проверяет наличие точно оба **192.168.1.1** и **10.0.4.1** с _действие_ из **Разрешить** в **ipRules** для применения **effectType**:

```json
"policyRule": {
    "if": {
        "allOf": [
            {
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
                "exists": "true"
            },
            {
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
                "Equals": [
                    {
                        "action": "Allow",
                        "value": "192.168.1.1"
                    },
                    {
                        "action": "Allow",
                        "value": "10.0.4.1"
                    }
                ]
            }
        ]
    },
    "then": {
        "effect": "[parameters('effectType')]"
    }
}
```

**[\*]** Псевдоним делает возможным для сравнения с значением каждого элемента в массиве и определенные свойства каждого элемента. Такой подход дает возможность сравнить свойства элемента для «Если ни один из», «если какие-либо», или "Если все из" сценариев. С помощью **ipRules [\*]**, пример может проверка того, каждый _действие_ — _Deny_, но не беспокоясь о том, как много правил существует, или какие IP-адрес _значение_ является. Это правило пример проверяет наличие совпадений из **ipRules [\*] .value** для **10.0.4.1** и применяет **effectType** только в том случае, если она не находит хотя бы одно совпадение:

```json
"policyRule": {
    "if": {
        "allOf": [
            {
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
                "exists": "true"
            },
            {
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value",
                "notEquals": "10.0.4.1"
            }
        ]
    },
    "then": {
        "effect": "[parameters('effectType')]"
    }
}
```

Дополнительные сведения см. в разделе [оценки [\*] псевдоним](../how-to/author-policies-for-arrays.md#evaluating-the--alias).

## <a name="initiatives"></a>Инициативы

Инициативы дают возможность сгруппировать несколько связанных определений политик, чтобы упростить их назначение и управление ими, так как это позволяет работать с группой как с единым элементом. Например, можно сгруппировать связанные определения политик тегов в одной инициативе. Вместо назначения каждой политики по отдельности вы сможете применить инициативу.

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

## <a name="next-steps"></a>Дальнейшие действия

- См. другие [примеры шаблонов для службы "Политика Azure"](../samples/index.md).
- См. дополнительные сведения о [действиях политик](effects.md).
- См. сведения о [программном создании политик](../how-to/programmatically-create.md).
- Узнайте о том, как получить [подробные сведения о соответствии](../how-to/getting-compliance-data.md).
- См. сведения о том, как [исправлять несоответствующие ресурсы](../how-to/remediate-resources.md).
- См. дополнительные сведения о группе управления в разделе [Упорядочение ресурсов с помощью групп управления Azure](../../management-groups/overview.md)
