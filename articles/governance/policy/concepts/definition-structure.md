---
title: Сведения о структуре определения политики
description: Описывает, как определения политик используются для установки соглашений о ресурсах Azure в Организации.
ms.date: 11/26/2019
ms.topic: conceptual
ms.openlocfilehash: 2126415c3ae7ecb14a47c79dacd67aee656cd745
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/06/2019
ms.locfileid: "74894307"
---
# <a name="azure-policy-definition-structure"></a>Структура определения службы "Политика Azure"

Определения политики ресурсов используются службой "Политика Azure", чтобы настроить соглашения для ресурсов. Каждое определение описывает соответствие ресурсов и действие, которое необходимо предпринять, если ресурс не соответствует требованиям.
Это соглашения помогут вам контролировать расходы и управлять ресурсами. Например, можно указать, что разрешены только определенные типы виртуальных машин. Кроме того, можно требовать наличие определенного тега для каждого ресурса. Политики наследуются всеми дочерними ресурсами. Политика, применяемая к группе ресурсов, применяется также ко всем ресурсам в этой группе.

Схема определения политики находится здесь: [https://schema.management.azure.com/schemas/2019-06-01/policyDefinition.json](https://schema.management.azure.com/schemas/2019-06-01/policyDefinition.json)

Для создания определения политики используется JSON. Определение политики содержит следующие элементы:

- mode;
- parameters
- display name
- Description (Описание)
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
                "defaultValue": [ "westus2" ]
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

Все примеры политик Azure приведены в [примерах политики Azure](../samples/index.md).

## <a name="mode"></a>Mode

**Режим** настраивается в зависимости от того, нацелена ли политика в свойство Azure Resource Manager или на свойство поставщика ресурсов.

### <a name="resource-manager-modes"></a>Режимы диспетчер ресурсов

**Режим** определяет типы ресурсов, которые будут оцениваться для политики. Ниже приведены поддерживаемые режимы.

- `all`: оценка групп ресурсов и всех типов ресурсов.
- `indexed`: оцениваются только типы ресурсов, которые поддерживают теги и расположение.

В большинстве случаев рекомендуется задать для параметра **mode** значение `all`. Во всех определениях политик, создаваемых на портале, используется режим `all`. Если используется PowerShell или Azure CLI, необходимо указать параметр **mode** вручную. Если определение политики не включает значение **mode**, в Azure PowerShell используется значение по умолчанию `all`, а в Azure CLI — `null`. Режим `null` является тем же, что и при использовании `indexed` для поддержки обратной совместимости.

`indexed` следует использовать при создании политик, которые будут принудительно применять теги или расположения. Это не обязательно, но помешает отображению ресурсов, которые не поддерживают теги и расположения, в качестве несоответствующих в результатах проверки соответствия. Исключением являются **группы ресурсов**. В политиках, которые принудительно применяют расположение или теги к группе ресурсов, следует задать для параметра **mode** значение `all` и явно указать целевой тип `Microsoft.Resources/subscriptions/resourceGroups`. Пример см. в статье о [принудительном применении тегов к группам ресурсов](../samples/enforce-tag-rg.md). Список ресурсов, поддерживающих теги, см. в статье [Поддержка тегов для ресурсов Azure](../../../azure-resource-manager/tag-support.md).

### <a name="a-nameresource-provider-modes-resource-provider-modes-preview"></a>режимы поставщика ресурсов <a name="resource-provider-modes" />(Предварительная версия)

В настоящее время в предварительной версии поддерживаются следующие режимы поставщика ресурсов:

- `Microsoft.ContainerService.Data` для управления правилами контроллера допуска в [службе Azure Kubernetes](../../../aks/intro-kubernetes.md). Политики, использующие этот режим поставщика ресурсов, **должны** использовать [енфорцерегополициный](./effects.md#enforceregopolicy) результат.
- `Microsoft.Kubernetes.Data` для управления кластерами Kubernetes Engine AKS в Azure.
  Политики, использующие этот режим поставщика ресурсов, **должны** использовать [енфорцеопаконстраинтный](./effects.md#enforceopaconstraint) результат.
- `Microsoft.KeyVault.Data` для управления хранилищами и сертификатами в [Azure Key Vault](../../../key-vault/key-vault-overview.md).

> [!NOTE]
> Режимы поставщиков ресурсов поддерживают только встроенные определения политик и не поддерживают инициативы в предварительной версии.

## <a name="parameters"></a>Параметры

Параметры помогают упростить управление политиками за счет сокращения числа определений политик. Параметры можно рассматривать как поля в форме: `name`, `address`, `city`, `state`. Эти параметры никогда не меняются, однако их значения изменяются в зависимости от того, как пользователь заполняет форму.
Точно так же параметры работают при создании политик. Добавив параметры в определение политики, вы сможете повторно использовать ее в различных сценариях, указывая разные значения.

> [!NOTE]
> Параметры могут быть добавлены к имеющемуся и назначенному определению. Новый параметр должен включать в себя свойство **defaultValue**. Это предотвращает косвенное использование существующих назначений политики или инициативы.

### <a name="parameter-properties"></a>Свойства параметра

Параметр имеет следующие свойства, которые используются в определении политики:

- **имя**. имя параметра. Используется функцией развертывания `parameters` в правиле политики. Дополнительные сведения см. в разделе [об использовании значения параметра](#using-a-parameter-value).
- `type`: определяет, является ли параметр **строковым**, **массивом**, **объектом**, **логическим**, **целым**, **float**или **DateTime**.
- `metadata`: определяет подсвойства, которые в основном используются портал Azure для вывода сведений, понятных пользователю:
  - `description`: описание того, для чего используется параметр. Можно использовать для примеров допустимых значений.
  - `displayName`: понятное имя, отображаемое на портале для параметра.
  - `strongType`: (необязательно) используется при назначении определения политики на портале. Предоставляет список с учетом контекста. См. дополнительные сведения о [вложенном свойстве strongType](#strongtype).
  - `assignPermissions`: (необязательно) задайте значение _true_ , чтобы портал Azure создавать назначения ролей во время назначения политики. Это свойство полезно, если вы хотите назначить разрешения за пределами области назначения. Для каждого определения роли в политике (или определении роли во всех политиках в этой инициативе) существует одно назначение роли. Значение параметра должно быть допустимым ресурсом или областью.
- `defaultValue`: (необязательный) задает значение параметра в назначении, если значение не задано.
  Это свойство необходимо при обновлении существующего назначенного определения политики.
- `allowedValues`: (необязательный) предоставляет массив значений, которые параметр принимает во время назначения.

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
        "defaultValue": [ "westus2" ],
        "allowedValues": [
            "eastus2",
            "westus2",
            "westus"
        ]
    }
}
```

### <a name="using-a-parameter-value"></a>Использование значения параметра

В правиле политики вы ссылаетесь на параметры с помощью следующего синтаксиса функции `parameters`:

```json
{
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

В этом примере используется параметр **allowedLocations**, который рассматривался в разделе о [свойствах параметра](#parameter-properties).

### <a name="strongtype"></a>strongType

В свойстве `metadata` можно использовать вложенное свойство **strongType**, чтобы предоставить список для выбора параметров на портале Azure. К допустимым значениям для **strongType** относится следующее:

- `location`
- `resourceTypes`
- `storageSkus`
- `vmSKUs`
- `existingResourceGroups`
- `omsWorkspace`
- `Microsoft.EventHub/Namespaces/EventHubs`
- `Microsoft.EventHub/Namespaces/EventHubs/AuthorizationRules`
- `Microsoft.EventHub/Namespaces/AuthorizationRules`
- `Microsoft.RecoveryServices/vaults`
- `Microsoft.RecoveryServices/vaults/backupPolicies`

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

- `"equals": "stringValue"`
- `"notEquals": "stringValue"`
- `"like": "stringValue"`
- `"notLike": "stringValue"`
- `"match": "stringValue"`
- `"matchInsensitively": "stringValue"`
- `"notMatch": "stringValue"`
- `"notMatchInsensitively": "stringValue"`
- `"contains": "stringValue"`
- `"notContains": "stringValue"`
- `"in": ["stringValue1","stringValue2"]`
- `"notIn": ["stringValue1","stringValue2"]`
- `"containsKey": "keyName"`
- `"notContainsKey": "keyName"`
- `"less": "value"`
- `"lessOrEquals": "value"`
- `"greater": "value"`
- `"greaterOrEquals": "value"`
- `"exists": "bool"`

При использовании условий **like** и **notLike** можно указать в значении подстановочный знак `*`.
Значение не должно содержать более одного подстановочного знака `*`.

При использовании условий **Match** и **notMatch** укажите `#` для сопоставления цифры, `?` для буквы `.`, чтобы сопоставить любой символ, и любой другой символ в соответствии с фактическим символом.
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
  - Пример: `tags['''My.Apostrophe.Tag''']`, где **"My. апостроф. Tag"** — это имя тега.
- Список псевдонимов свойств указан в разделе [Псевдонимы](#aliases).

> [!NOTE]
> `tags.<tagName>`, `tags[tagName]` и `tags[tag.with.dots]` все еще являются приемлемыми способами объявления поля тегов. Однако предпочтительными являются выражения, указанные выше.

#### <a name="use-tags-with-parameters"></a>Использование тегов с параметрами

Значение параметра передается полю тега. Передача параметра полю тега повышает гибкость определения политики во время ее назначения.

В указанном ниже примере `concat` используется для создания поля подстановки тега для тега со значением параметра **tagName**. Если этот тег не существует **, то с** помощью функции поиска `resourcegroup()` можно добавить тег, используя значение того же именованного тега, которое задано в родительской группе ресурсов с аудитом.

```json
{
    "if": {
        "field": "[concat('tags[', parameters('tagName'), ']')]",
        "exists": "false"
    },
    "then": {
        "effect": "modify",
        "details": {
            "operations": [{
                "operation": "add",
                "field": "[concat('tags[', parameters('tagName'), ']')]",
                "value": "[resourcegroup().tags[parameters('tagName')]]"
            }],
            "roleDefinitionIds": [
                "/providers/microsoft.authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
            ]
        }
    }
}
```

### <a name="value"></a>Value

Условия также можно настраивать с помощью **value**. **value** проверяет условия на соответствие [параметрам](#parameters), [поддерживаемым функциям шаблонов](#policy-functions) или литералам.
**value** используется в паре с любым поддерживаемым [условием](#conditions).

> [!WARNING]
> Если результатом _функции шаблона_ является ошибка, то Вычисление политики завершится ошибкой. Неудачная Оценка — это неявный **запрет**. Дополнительные сведения см. в разделе [предотвращение сбоев шаблонов](#avoiding-template-failures).

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

#### <a name="avoiding-template-failures"></a>Предотвращение сбоев шаблонов

Использование _функций шаблона_ в **значении** позволяет использовать множество сложных вложенных функций. Если результатом _функции шаблона_ является ошибка, то Вычисление политики завершится ошибкой. Неудачная Оценка — это неявный **запрет**. Пример **значения** , которое не удается выполнить в определенных сценариях:

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

В примере правила политики выше используется [подстрока ()](../../../azure-resource-manager/resource-group-template-functions-string.md#substring) для сравнения первых трех символов **имени** с **ABC**. Если **имя** короче трех символов, то функция `substring()` приводит к ошибке. Эта ошибка приводит к тому, что политика станет **отклонением** .

Вместо этого используйте функцию [if ()](../../../azure-resource-manager/resource-group-template-functions-logical.md#if) , чтобы проверить, что первые три символа **имени** , равные **ABC** , не допускают возникновения ошибки в **имени** , меньшем трех символов.

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

После изменения правила политики `if()` проверяет длину **имени** , прежде чем пытаться получить `substring()` значения с менее чем тремя символами. Если **имя** слишком короткое, возвращается значение "не начинается с ABC", а затем сравнивается с **ABC**. Ресурс с коротким именем, который не начинается с **ABC** , по-прежнему не проходит правило политики, но больше не вызывает ошибку во время вычисления.

### <a name="count"></a>Количество

Условия, которые подсчитывает, сколько элементов массива в полезных данных ресурса соответствуют условному выражению, могут быть сформированы с помощью выражения **Count** . Распространенными сценариями является проверка того, что по крайней мере один из "," только один из "," все "или" ни одного из "элементов массива не удовлетворяет условию. Функция **Count** вычисляет каждый элемент массива для выражения условия и суммирует _истинные_ результаты, которые затем сравниваются с оператором выражения.

Структура выражения **Count** имеет следующие значения:

```json
{
    "count": {
        "field": "<[*] alias>",
        "where": {
            /* condition expression */
        }
    },
    "<condition>": "<compare the count of true condition expression array members to this value>"
}
```

Для **счетчика**используются следующие свойства:

- **Count. поле** (обязательно): содержит путь к массиву и должен быть псевдонимом массива. Если массив отсутствует, выражение вычисляется как _false_ без учета выражения условия.
- **Count. Where** (необязательно): выражение условия для индивидуального вычисления каждого [\[\*\] член массива псевдонимов](#understanding-the--alias) **Count. Field**. Если это свойство не указано, все элементы массива с путем "Field" оцениваются в _значение true_. В этом свойстве можно использовать любое [условие](../concepts/definition-structure.md#conditions) .
  [Логические операторы](#logical-operators) можно использовать внутри этого свойства для создания сложных требований к оценке.
- **условие\<\>** (обязательно). значение сравнивается с числом элементов, которые соответствуют условному выражению **Count. Where** . Следует использовать числовое [условие](../concepts/definition-structure.md#conditions) .

#### <a name="count-examples"></a>Примеры количества

Пример 1. Проверка пустого массива

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]"
    },
    "equals": 0
}
```

Пример 2. Проверка только одного элемента массива для соответствия условному выражению

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].description",
            "equals": "My unique description"
        }
    },
    "equals": 1
}
```

Пример 3. Проверка по крайней мере одного элемента массива на соответствие условному выражению

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].description",
            "equals": "My common description"
        }
    },
    "greaterOrEquals": 1
}
```

Пример 4. Проверка соответствия всех элементов массива объектов условному выражению

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].description",
            "equals": "description"
        }
    },
    "equals": "[length(field(Microsoft.Network/networkSecurityGroups/securityRules[*]))]"
}
```

Пример 5. Проверка соответствия всех элементов массива строк условному выражению

```json
{
    "count": {
        "field": "Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]",
        "where": {
            "field": "Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]",
            "like": "*@contoso.com"
        }
    },
    "equals": "[length(field('Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]'))]"
}
```

Пример 6. Использование **поля** внутри **значения** для проверки соответствия всех элементов массива условному выражению

```json
{
    "count": {
        "field": "Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]",
        "where": {
            "value": "[last(split(first(field('Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]')), '@'))]",
            "equals": "contoso.com"
        }
    },
    "equals": "[length(field('Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]'))]"
}
```

Пример 7. Проверка того, что хотя бы один элемент массива соответствует нескольким свойствам в выражении условия

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "allOf": [
                {
                    "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].direction",
                    "equals": "Inbound"
                },
                {
                    "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].access",
                    "equals": "Allow"
                },
                {
                    "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].destinationPortRange",
                    "equals": "3389"
                }
            ]
        }
    },
    "greater": 0
}
```

### <a name="effect"></a>Результат

Политика Azure поддерживает следующие типы эффектов:

- **Append** добавляет в запрос некоторый набор полей;
- **Audit** записывает предупреждение в журнал действий, но не отвергает запрос;
- **Помощью параметров auditifnotexists**: создает событие предупреждения в журнале действий, если связанный ресурс не существует.
- **Deny** записывает событие в журнал действий и отвергает запрос;
- **DeployIfNotExists**: развертывает связанный ресурс, если он еще не существует
- **Disabled** не оценивает ресурсы на соответствие правилу политики.
- **Енфорцеопаконстраинт** (Предварительная версия): Настройка контроллера рассоединений агента открытой политики с привратником v3 для самостоятельно управляемых кластеров Kubernetes в Azure (Предварительная версия)
- **Енфорцерегополици** (Предварительная версия): Настройка контроллера допусков агента открытой политики с привратником версии 2 в службе Kubernetes Azure.
- **Изменить**: добавляет, обновляет или удаляет определенные теги из ресурса.

Подробные сведения о каждом действии, порядке оценки, свойствах и примерах см. в разделе [Основные сведения о влиянии политики Azure](effects.md).

### <a name="policy-functions"></a>Функции политики

Все [функции шаблонов диспетчер ресурсов](../../../azure-resource-manager/resource-group-template-functions.md) доступны для использования в правиле политики, за исключением следующих функций и определяемых пользователем функций:

- copyIndex()
- deployment()
- list*
- newGuid ()
- Пиккзонес ()
- providers()
- reference()
- resourceId()
- variables()

Следующие функции можно использовать в правиле политики, но они отличаются от использования в шаблоне Azure Resource Manager:

- addDays (dateTime, Нумберофдайстоадд)
  - **DateTime**: [Required] строка строки в формате ISO 8601 DateTime "гггг-мм-ddTHH: mm: SS. фффффффз"
  - **нумберофдайстоадд**: [обязательный] целое число дней для добавления
- utcNow () — в отличие от шаблона диспетчер ресурсов он может использоваться вне defaultValue.
  - Возвращает строку, которая имеет текущую дату и время в формате универсального ISO 8601 DateTime ' yyyy-MM-ddTHH: mm: SS. Фффффффз '

Кроме того, для правил политики доступна функция `field`. `field` предназначена главным образом для использования с **AuditIfNotExists** и **DeployIfNotExists**, чтобы ссылаться на поля в ресурсе, который оценивается. Это можно увидеть на [примере DeployIfNotExists](effects.md#deployifnotexists-example).

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

- Расширение политики Azure для Visual Studio Code (рекомендуется)

  Используйте [расширение политики Azure для Visual Studio Code](../how-to/extension-for-vscode.md) , чтобы просматривать и находить псевдонимы для свойств ресурсов.

  ![Расширение политики Azure для Visual Studio Code](../media/extension-for-vscode/extension-hover-shows-property-alias.png)

- График ресурсов Azure

  Используйте оператор `project` для вывода **псевдонима** ресурса.

  ```kusto
  Resources
  | where type=~'microsoft.storage/storageaccounts'
  | limit 1
  | project aliases
  ```
  
  ```azurecli-interactive
  az graph query -q "Resources | where type=~'microsoft.storage/storageaccounts' | limit 1 | project aliases"
  ```
  
  ```azurepowershell-interactive
  Search-AzGraph -Query "Resources | where type=~'microsoft.storage/storageaccounts' | limit 1 | project aliases"
  ```

- Azure PowerShell

  ```azurepowershell-interactive
  # Login first with Connect-AzAccount if not using Cloud Shell

  # Use Get-AzPolicyAlias to list available providers
  Get-AzPolicyAlias -ListAvailable

  # Use Get-AzPolicyAlias to list aliases for a Namespace (such as Azure Compute -- Microsoft.Compute)
  (Get-AzPolicyAlias -NamespaceMatch 'compute').Aliases
  ```

- Azure CLI

  ```azurecli-interactive
  # Login first with az login if not using Cloud Shell

  # List namespaces
  az provider list --query [*].namespace

  # Get Azure Policy aliases for a specific Namespace (such as Azure Compute -- Microsoft.Compute)
  az provider show --namespace Microsoft.Compute --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
  ```

- REST API или ARMClient

  ```http
  GET https://management.azure.com/providers/?api-version=2017-08-01&$expand=resourceTypes/aliases
  ```

### <a name="understanding-the--alias"></a>Общие сведения о псевдониме [*]

Несколько доступных псевдонимов имеют версию, которая отображается как "нормальное", а другая имеет **\[\*\]** прикреплена к ней. Пример.

- `Microsoft.Storage/storageAccounts/networkAcls.ipRules`
- `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]`

Псевдоним "Обычная" представляет поле как одно значение. Это поле предназначено для сценариев сравнения точного соответствия, если весь набор значений должен быть в точности определен, но не больше и не меньше.

**\[\*\]** псевдоним позволяет сравнивать со значением каждого элемента массива и конкретными свойствами каждого элемента. Такой подход позволяет сравнивать свойства элементов для сценариев "If None of", "If any of", или "Если все из". Для более сложных сценариев используйте выражение условия [Count](#count) . С помощью **ипрулес\[\*\]** , пример будет проверять, что каждое _действие_ _отклоняется_, но не беспокоясь о том, сколько правил существует или какое _значение_ является IP-адресом.
Этот пример правила проверяет наличие совпадений **ипрулес\[\*\]. Value** to **10.0.4.1** и применяет **еффекттипе** только в том случае, если не найдено хотя бы одно совпадение:

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



Дополнительные сведения см. [в разделе Вычисление псевдонима [\*]](../how-to/author-policies-for-arrays.md#evaluating-the--alias).

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

- Просмотрите примеры в [примерах политики Azure](../samples/index.md).
- Изучите [сведения о действии политик](effects.md).
- Узнайте, как [программно создавать политики](../how-to/programmatically-create.md).
- Узнайте, как [получить данные о соответствии](../how-to/get-compliance-data.md).
- Узнайте, как [исправлять несоответствующие ресурсы](../how-to/remediate-resources.md).
- Дополнительные сведения о группе управления см. в статье [Упорядочивание ресурсов с помощью групп управления Azure](../../management-groups/overview.md).