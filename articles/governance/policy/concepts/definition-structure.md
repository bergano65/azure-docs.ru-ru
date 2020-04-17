---
title: Сведения о структуре определения политики
description: Описывает, как определения политик используются для создания конвенций для ресурсов Azure в вашей организации.
ms.date: 04/03/2020
ms.topic: conceptual
ms.openlocfilehash: 0a7c4e05270ff242fa97b253b27a5de92895368a
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461010"
---
# <a name="azure-policy-definition-structure"></a>Структура определения службы "Политика Azure"

Политика Azure устанавливает конвенции для ресурсов. Определения политики описывают [условия](#conditions) соответствия ресурсам и эффект, который следует принять при выполнении того или иного условия. Условие сравнивает [поле](#fields) свойств ресурсов с требуемым значением. Поля свойств ресурсов доступны с помощью [псевдонимов.](#aliases) Поле свойств ресурсов — это либо одноценное поле, либо [множество](#understanding-the--alias) нескольких значений. Оценка состояния отличается от массивов.
Узнайте больше об [условиях](#conditions).

Это соглашения помогут вам контролировать расходы и управлять ресурсами. Например, можно указать, что разрешены только определенные типы виртуальных машин. Кроме того, можно требовать наличие определенного тега для каждого ресурса. Политики наследуются всеми дочерними ресурсами. Политика, применяемая к группе ресурсов, применяется также ко всем ресурсам в этой группе.

Схема определения политики приведена здесь:[https://schema.management.azure.com/schemas/2019-06-01/policyDefinition.json](https://schema.management.azure.com/schemas/2019-06-01/policyDefinition.json)

Для создания определения политики используется JSON. Определение политики содержит следующие элементы:

- mode
- параметры
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

Все образцы политики Azure находятся в [образцах политики Azure.](../samples/index.md)

## <a name="mode"></a>Режим

**Режим** настраивается в зависимости от того, ориентирована ли политика на свойство менеджера ресурсов Azure или свойство поставщика ресурсов.

### <a name="resource-manager-modes"></a>Режимы управления ресурсами

**Режим** определяет типы ресурсов, которые будут оцениваться для политики. Ниже приведены поддерживаемые режимы.

- `all`: оценка групп ресурсов и всех типов ресурсов.
- `indexed`: оцениваются только типы ресурсов, которые поддерживают теги и расположение.

Например, `Microsoft.Network/routeTables` ресурс поддерживает теги и местоположение и оценивается в обоих режимах. Однако `Microsoft.Network/routeTables/routes` ресурс не может быть помечен и `Indexed` не оценивается в режиме.

В большинстве случаев рекомендуется задать для параметра **mode** значение `all`. Во всех определениях политик, создаваемых на портале, используется режим `all`. Если используется PowerShell или Azure CLI, необходимо указать параметр **mode** вручную. Если определение политики не включает значение **mode**, в Azure PowerShell используется значение по умолчанию `all`, а в Azure CLI — `null`. Режим `null` является тем же, что и при использовании `indexed` для поддержки обратной совместимости.

`indexed` следует использовать при создании политик, которые будут принудительно применять теги или расположения. Это не обязательно, но помешает отображению ресурсов, которые не поддерживают теги и расположения, в качестве несоответствующих в результатах проверки соответствия. Исключением являются **группы ресурсов**. В политиках, которые принудительно применяют расположение или теги к группе ресурсов, следует задать для параметра **mode** значение `all` и явно указать целевой тип `Microsoft.Resources/subscriptions/resourceGroups`. Пример см. в статье о [принудительном применении тегов к группам ресурсов](../samples/enforce-tag-rg.md). Для списка ресурсов, поддерживающих теги, [см.](../../../azure-resource-manager/management/tag-support.md)

### <a name="resource-provider-modes-preview"></a><a name="resource-provider-modes" />Режимы поставщика ресурсов (предварительный просмотр)

Следующие режимы поставщика ресурсов в настоящее время поддерживаются во время предварительного просмотра:

- `Microsoft.ContainerService.Data`для управления правилами приема контроллера на [службе Azure Kubernetes](../../../aks/intro-kubernetes.md). Политики, использующие этот режим поставщика ресурсов, **должны** использовать эффект [EnforceRegoPolicy.](./effects.md#enforceregopolicy)
- `Microsoft.Kubernetes.Data`для управления самоуправляемыми кластерами AKS Engine Kubernetes на Azure.
  Политики, использующие этот режим поставщика ресурсов, **должны** использовать эффект [EnforceOPAConstraint.](./effects.md#enforceopaconstraint)
- `Microsoft.KeyVault.Data`для управления хранилищами и сертификатами в [Azure Key Vault](../../../key-vault/general/overview.md).

> [!NOTE]
> Режимы поставщика ресурсов поддерживают только встроенные определения политики и не поддерживают инициативы во время предварительного просмотра.

## <a name="parameters"></a>Параметры

Параметры помогают упростить управление политиками за счет сокращения числа определений политик. Параметры можно рассматривать как поля в форме: `name`, `address`, `city`, `state`. Эти параметры никогда не меняются, однако их значения изменяются в зависимости от того, как пользователь заполняет форму.
Точно так же параметры работают при создании политик. Добавив параметры в определение политики, вы сможете повторно использовать ее в различных сценариях, указывая разные значения.

> [!NOTE]
> Параметры могут быть добавлены к имеющемуся и назначенному определению. Новый параметр должен включать в себя свойство **defaultValue**. Это предотвращает косвенное использование существующих назначений политики или инициативы.

### <a name="parameter-properties"></a>Свойства параметра

Параметр имеет следующие свойства, которые используются в определении политики:

- **имя**: Имя вашего параметра. Используется функцией развертывания `parameters` в правиле политики. Дополнительные сведения см. в разделе [об использовании значения параметра](#using-a-parameter-value).
- `type`: Определяет, если параметр **строки,** **массив**, **объект**, **булейт**, **целый**ряд, **плавать,** или **дата**.
- `metadata`: Определяет субсвойства, в основном используемые порталом Azure для отображения удобной для пользователя информации:
  - `description`: Объяснение того, для чего используется параметр. Можно использовать для примеров допустимых значений.
  - `displayName`: Дружелюбное имя, указанное на портале для параметра.
  - `version`: (Необязательно) Отслеживает подробную информацию о версии содержания определения политики.

    > [!NOTE]
    > Служба политики Azure использует, `version` `preview`и `deprecated` свойства для передачи уровня изменений в встроенной политики определения или инициативы и состояния. `version` Формат: `{Major}.{Minor}.{Patch}`. Конкретные состояния, такие как _deprecated_ или `version` _предварительного просмотра_, прилагаются к собственности или в другом свойстве, как **boolean**.

  - `category`: (Необязательно) Определяет, в какой категории на портале Azure отображается определение политики.
  - `strongType`: (Необязательно) Используется при назначении определения политики через портал. Предоставляет список с учетом контекста. См. дополнительные сведения о [вложенном свойстве strongType](#strongtype).
  - `assignPermissions`: (Необязательно) Установить как _верно,_ чтобы портал Azure создавал назначения ролей во время назначения политики. Это свойство полезно в случае, если вы хотите назначить разрешения за пределами области назначения. В политике имеется одно назначение ролей на определенную роль (или определение роли во всех политиках инициативы). Значение параметра должно быть допустимым ресурсом или областью охвата.
- `defaultValue`: (Необязательно) Устанавливает значение параметра в назначении, если значение не дается.
  Это свойство необходимо при обновлении существующего назначенного определения политики.
- `allowedValues`: (Необязательно) предоставляет массив значений, которые параметр принимает во время назначения.

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

В правиле политики вы ссылаетесь на параметры со следующим `parameters` синтаксисом функции:

```json
{
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

В этом примере используется параметр **allowedLocations**, который рассматривался в разделе о [свойствах параметра](#parameter-properties).

### <a name="strongtype"></a>strongType

В свойстве `metadata` можно использовать вложенное свойство **strongType**, чтобы предоставить список для выбора параметров на портале Azure. **strongType** может быть _поддерживаемым типом ресурса_ или допустимым значением. Чтобы определить, действителен ли _тип ресурса_ для **strongType,** используйте [Get-AzResourceProvider.](/powershell/module/az.resources/get-azresourceprovider)

Некоторые _типы ресурсов,_ не возвращенные **Get-AzResourceProvider,** поддерживаются. К числу них относятся:

- `Microsoft.RecoveryServices/vaults/backupPolicies`

Разрешенные значения типа _ресурсов_ для **strongType:**

- `location`
- `resourceTypes`
- `storageSkus`
- `vmSKUs`
- `existingResourceGroups`

## <a name="definition-location"></a>Расположение определения

При создании инициативы или политики необходимо указывать расположение определения. Расположение определения должно быть задано как группа управления или подписка. Расположение указывает область, которой можно назначить инициативу или политику. Ресурсы должны быть непосредственными членами или дочерними элементами в иерархии расположения определения для назначения.

Если расположение определения является

- **подпиской**, политика может быть назначена только ресурсам в этой подписке;
- **группой управления**, политика может быть назначена только ресурсам в дочерних группах управления и дочерних подписках. Если вы планируете применять определение политики к нескольким подпискам, расположение должно быть группой управления, содержащей эти подписки.

## <a name="display-name-and-description"></a>Отображаемое имя и описание

Параметры **displayName** и **description** позволяют идентифицировать определение политики и описать контекст для ее использования. Максимальная длина **displayName** составляет _128_ символов, а **description** — _512_ символов.

> [!NOTE]
> При создании или обновлении определения политики **id,** **type,** и **имя** определяются свойствами, внешними для JSON, и не являются необходимыми в файле JSON. Получение определения политики через SDK возвращает **id,** **type**и **имя** свойства как часть JSON, но каждый из них только для чтения информации, связанной с определением политики.

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
- `"less": "dateValue"` | `"less": "stringValue"` | `"less": intValue`
- `"lessOrEquals": "dateValue"` | `"lessOrEquals": "stringValue"` | `"lessOrEquals": intValue`
- `"greater": "dateValue"` | `"greater": "stringValue"` | `"greater": intValue`
- `"greaterOrEquals": "dateValue"` | `"greaterOrEquals": "stringValue"` | `"greaterOrEquals": intValue`
- `"exists": "bool"`

Для **менее,** **lessOrEquals,** **больше,** и **большеOrEquals**, если тип свойства не соответствует типу состояния, ошибка брошена. Строка сравнения сделаны с помощью `InvariantCultureIgnoreCase`.

При использовании условий **like** и **notLike** можно указать в значении подстановочный знак `*`.
Значение не должно содержать более одного подстановочного знака `*`.

При использовании **совпадения,** `#` а не условия `?` **матча,** `.` обеспечить, чтобы соответствовать цифре, для письма, чтобы соответствовать любому символу, и любой другой символ, чтобы соответствовать этому фактическому характеру. В то время как **матч** и **notMatch** чувствительны к случаям, все другие условия, которые оценивают _stringValue,_ являются нечувствительными к случаям. Другие варианты без учета регистра доступны для условий **matchInsensitively** и **notMatchInsensitively**.

В значении поля ** \] массива псевдонима каждый элемент в массиве оценивается индивидуально логическии и между элементами. \[ \* ** **and** Для получения дополнительной [информации \[ \* \] см.](../how-to/author-policies-for-arrays.md#evaluating-the--alias)

### <a name="fields"></a>Поля

Условия создаются на основе полей. Поле сопоставляет свойства в полезных данных запроса ресурса и описывает состояния ресурса.

Поддерживаются следующие поля.

- `name`
- `fullName`
  - Возвращает полное имя ресурса. Полное имя ресурса — это имя ресурса, к которому добавлены любые имена родительских ресурсов (например, myServer/myDatabase).
- `kind`
- `type`
- `location`
  - Используйте **global** для ресурсов, которые зависят от расположения.
- `identity.type`
  - Возвращает тип [управляемого удостоверения](../../../active-directory/managed-identities-azure-resources/overview.md), включенный в ресурс.
- `tags`
- `tags['<tagName>']`
  - Этот синтаксис в скобках поддерживает имена тегов, которые содержат такие знаки препинания, как дефис, точку или пробел.
  - Где ** \<tagName\> ** это имя тега для проверки условия.
  - Пример: `tags['Acct.CostCenter']`. **Acct.CostCenter** — это имя тега.
- `tags['''<tagName>''']`
  - Этот синтаксис в скобках поддерживает имена тегов, которые содержат апострофы благодаря использованию двойного апострофа.
  - Где **\<'\>tagName '** это имя тега для проверки условия.
  - Пример: `tags['''My.Apostrophe.Tag''']` где **"My.Apostrophe.Tag"** это название тега.
- Список псевдонимов свойств указан в разделе [Псевдонимы](#aliases).

> [!NOTE]
> `tags.<tagName>`, `tags[tagName]` и `tags[tag.with.dots]` все еще являются приемлемыми способами объявления поля тегов. Однако предпочтительными являются выражения, указанные выше.

#### <a name="use-tags-with-parameters"></a>Использование тегов с параметрами

Значение параметра передается полю тега. Передача параметра полю тега повышает гибкость определения политики во время ее назначения.

В указанном ниже примере `concat` используется для создания поля подстановки тега для тега со значением параметра **tagName**. Если этот тег не существует, эффект **изменения** используется для добавления тега, используя значение того же имени `resourcegroup()` тега, установленного на группе родительских ресурсов проверенных ресурсов, используя функцию поиска.

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

### <a name="value"></a>Значение

Условия также можно настраивать с помощью **value**. **value** проверяет условия на соответствие [параметрам](#parameters), [поддерживаемым функциям шаблонов](#policy-functions) или литералам.
**value** используется в паре с любым поддерживаемым [условием](#conditions).

> [!WARNING]
> Если результатом _функции шаблона_ является ошибка, оценка политики завершится сбоем. Завершившаяся сбоем оценка — это неявный **запрет**. Дополнительные сведения см. в разделе [Аvoiding template failures](#avoiding-template-failures) (Предотвращение сбоев шаблонов). Используйте [enforcementMode](./assignment-structure.md#enforcement-mode) **of DoNotEnforce** для предотвращения влияния неудачной оценки на новые или обновленные ресурсы при тестировании и проверке нового определения политики.

#### <a name="value-examples"></a>Примеры значений

В этом примере правила политики используют **value** для сравнения результата функции `resourceGroup()` и возвращенного свойства **name** с условием **like** для `*netrg`. Это правило запрещает любой ресурс, с  **типом**, отличающимся от `Microsoft.Network/*`, в любой группе ресурсов, имя которой заканчивается на `*netrg`.

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

В этом примере правила политики используют **value** для проверки того, **равен ли результат нескольких вложенных функций ** `true`. Это правило запрещает любой ресурс, в котором нет хотя бы трех тегов.

```json
{
    "mode": "indexed",
    "policyRule": {
        "if": {
            "value": "[less(length(field('tags')), 3)]",
            "equals": "true"
        },
        "then": {
            "effect": "deny"
        }
    }
}
```

#### <a name="avoiding-template-failures"></a>Предотвращение сбоев шаблона

Использование _функций шаблона_ в **стоимости** позволяет для многих сложных вложенных функций. Если результатом _функции шаблона_ является ошибка, оценка политики завершится сбоем. Завершившаяся сбоем оценка — это неявный **запрет**. Пример **значения,** которое не удается в некоторых сценариях:

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

Приведенное выше правило политики использует [подстроку()](../../../azure-resource-manager/templates/template-functions-string.md#substring) для сравнения первых трех символов **имени** с **abc.** Если **имя** короче трех символов, `substring()` функция приводит к ошибке. Эта ошибка приводит к тому, что политика становится **эффектом отрицания.**

Вместо этого используйте функцию [if()](../../../azure-resource-manager/templates/template-functions-logical.md#if) для проверки того, являются ли первые три символа **имени** равны **abc,** не позволяя **имени** короче трех символов вызвать ошибку:

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

С помощью пересмотренного `if()` правила политики проверите `substring()` длину **имени,** прежде чем пытаться получить значение с менее чем тремя символами. Если **имя** слишком короткое, значение "не начиная с abc" возвращается вместо этого и по сравнению с **abc**. Ресурс с коротким именем, который не начинается с **abc,** по-прежнему не выполняет правило политики, но больше не вызывает ошибки при оценке.

### <a name="count"></a>Count

Условия, которые подсчитывают количество членов массива в полезной нагрузке ресурса, удовлетворяющих выражению состояния, могут быть сформированы с помощью выражения **подсчета.** Общие сценарии проверяют, удовлетворяют ли «по крайней мере один из», «точно один из», «все» или «ни один из» членов массива. **подсчет** оценивает каждого [ \[ \* \] участника аголюбоума](#understanding-the--alias) для выражения состояния и суммирует _истинные_ результаты, которые затем сравнивается с оператором выражения.

Структура выражения **графа:**

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

Следующие свойства используются с **подсчетом:**

- **count.field** (требуется): Содержит путь к массиву и должен быть псевдонимом массива. Если массив отсутствует, выражение оценивается как _ложное_ без учета выражения состояния.
- **count.where** (необязательно): Выражение состояния для [ \[ \* \] ](#understanding-the--alias) индивидуальной оценки каждого члена массива alias **count.field**. Если это свойство не предусмотрено, все участники массива с траекторией "поля" оцениваются по _истине._ Любое [условие](../concepts/definition-structure.md#conditions) может быть использовано внутри этого свойства.
  [Логические операторы](#logical-operators) могут быть использованы внутри этого свойства для создания сложных требований к оценке.
- состояние (требуется): Значение сравнивается с числом элементов, которые соответствовали **count.where** выражение состояния. ** \<\> ** Следует использовать численное [состояние.](../concepts/definition-structure.md#conditions)

#### <a name="count-examples"></a>Подсчитайте примеры

Пример 1: Проверьте, пуст ли массив

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]"
    },
    "equals": 0
}
```

Пример 2: Проверьте только один член массива для удовлетворения выражения состояния

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

Пример 3: Проверка хотя бы одного участника массива для удовлетворения выражения состояния

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

Пример 4: Проверка того, что все участники массива объектов соответствуют выражению состояния

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].description",
            "equals": "description"
        }
    },
    "equals": "[length(field('Microsoft.Network/networkSecurityGroups/securityRules[*]'))]"
}
```

Пример 5: Проверьте, что все участники массива строк отвечают выражению состояния

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

Пример 6: Используйте **поле** внутри **значения,** чтобы проверить, что все члены массива соответствуют выражению состояния

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

Пример 7: Проверьте, что по крайней мере один из членов массива соответствует нескольким свойствам в выражении состояния

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

### <a name="effect"></a>Действие

Политика Azure поддерживает следующие типы эффекта:

- **Append** добавляет в запрос некоторый набор полей;
- **Audit** записывает предупреждение в журнал действий, но не отвергает запрос;
- **AuditIfNotExists**: генерирует событие предупреждения в журнале активности, если связанного ресурса не существует
- **Deny** записывает событие в журнал действий и отвергает запрос;
- **DeployIfNotExists:** развертывает связанный ресурс, если он еще не существует
- **Disabled** не оценивает ресурсы на соответствие правилу политики.
- **EnforceOPAConstraint** (предварительный просмотр): настраивает контроллер приема Open Policy Agent с помощью Gatekeeper v3 для самоуправляемых кластеров Kubernetes на Azure (предварительный просмотр)
- **EnforceRegoPolicy** (предварительный просмотр): настраивает контроллер приема open Policy Agent с помощью Gatekeeper v2 в azure Kubernetes Service
- **Изменение:** добавляет, обновляет или удаляет определенные теги с ресурса

Для получения подробной информации о каждом эффекте, порядке оценки, свойствах и примерах [см.](effects.md)

### <a name="policy-functions"></a>Функции политики

Все [функции шаблона resource Manager](../../../azure-resource-manager/templates/template-functions.md) доступны для использования в рамках правила политики, за исключением следующих функций и функций, определенных пользователем:

- copyIndex()
- deployment()
- list*
- newGuid()
- pickзон()
- providers()
- reference()
- resourceId()
- variables()

> [!NOTE]
> Эти функции по-прежнему доступны в `details.deployment.properties.template` части развертывания шаблона в определении политики **deployIfNotExists.**

Следующая функция доступна для использования в правиле политики, но отличается от использования в шаблоне менеджера ресурсов Azure:

- `utcNow()`- В отличие от шаблона Resource Manager, это может быть использовано вне значения defaultValue.
  - Возвращает строку, установленную на текущую дату и время в формате Universal ISO 8601 DateTime 'yyy-MM-ddTHH:mm:ss.ffffff'

Следующие функции доступны только в правилах политики:

- `addDays(dateTime, numberOfDaysToAdd)`
  - **dateTime**: строка «Требуемая» строка - Строка в универсальном формате ISO 8601 DateTime 'yyy-MM-ddTHH:mm:ss.fffff'
  - **numberOfDaysToAdd**: «Требуемо» целый ряд - Количество дней, чтобы добавить
- `field(fieldName)`
  - **поле:** строка «Требуемая» строка - Название [поля](#fields) для извлечения
  - Возвращает значение этого поля из ресурса, который оценивается условием If
  - `field` предназначена главным образом для использования с **AuditIfNotExists** и **DeployIfNotExists**, чтобы ссылаться на поля в ресурсе, который оценивается. Это можно увидеть на [примере DeployIfNotExists](effects.md#deployifnotexists-example).
- `requestContext().apiVersion`
  - Возвращает версию API запроса, который вызвал `2019-09-01`оценку политики (пример: ).
    Это будет версия API, которая использовалась в запросе PUT/PATCH для оценки создания/обновления ресурсов. Последняя версия API всегда используется при оценке соответствия существующим ресурсам.
  
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

## <a name="aliases"></a>Aliases

Псевдонимы свойств позволяют обращаться к определенным свойствам для типа ресурса. Псевдонимы позволяют ограничить значения или условия, разрешенные для свойства ресурса. Каждый псевдоним сопоставляется с путями в разных версиях API для заданного типа ресурса. Во время оценки политики модуль политики получает путь свойства для этой версии API.

Список псевдонимов постоянно пополняется. Чтобы узнать, какие псевдонимы в настоящее время поддерживаются службой "Политика Azure", используйте один из следующих методов:

- Расширение политики Azure для кода Visual Studio (рекомендуется)

  Используйте [расширение политики Azure для Visual Studio Code](../how-to/extension-for-vscode.md) для просмотра и обнаружения псевдонимов свойств ресурсов.

  ![Расширение политики Azure для визуального кода студии](../media/extension-for-vscode/extension-hover-shows-property-alias.png)

- Azure Resource Graph

  Используйте `project` оператора для отображения **псевдонима** ресурса.

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

- Azure CLI

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

Некоторые из псевдонимов, которые доступны есть версия, которая появляется как "нормальное" имя, а другой, который ** \[ \* ** прилагается к нему. Пример:

- `Microsoft.Storage/storageAccounts/networkAcls.ipRules`
- `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]`

«Нормальный» псевдоним представляет поле как единое значение. Это поле предназначено для точных сценариев сравнения совпадений, когда весь набор значений должен быть точно так, как определено, не больше и не меньше.

** \[ Псевдоним \* ** позволяет сравнивать со значением каждого элемента в массиве и конкретными свойствами каждого элемента. Такой подход позволяет сравнивать свойства элементов для сценариев "если ни один из", "если какой-либо из" или "если все" сценарии. Для более сложных сценариев используйте выражение состояния [подсчета.](#count) Использование **\[\*ipRules,** примером будет подтверждение того, что каждое _действие_ _является Отрицание_, но не беспокоясь о том, сколько правил существует или что _значение_ IP.
Этот образец правила проверяет для любых совпадений **\[\*\]ipRules .value** до **10.0.4.1** и применяет **effectType** только если он не находит по крайней мере одного совпадения:

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

Для получения более [подробной\*информации, см.](../how-to/author-policies-for-arrays.md#evaluating-the--alias)

## <a name="initiatives"></a>Инициативы

Инициативы дают возможность сгруппировать несколько связанных определений политик, чтобы упростить их назначение и управление ими, так как это позволяет работать с группой как с единым элементом. Например, можно сгруппировать связанные определения политик тегов в одной инициативе. Вместо назначения каждой политики по отдельности вы сможете применить инициативу.

> [!NOTE]
> После того, как инициатива назначена, параметры уровня инициативы не могут быть изменены. В связи с этим, рекомендация заключается в установлении **значения по умолчанию** при определении параметра.

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
                },
                "defaultValue": "DefaultCostCenter"
            },
            "productNameValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for product Name tag"
                },
                "defaultValue": "DefaultProduct"
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
    }
}
```

## <a name="next-steps"></a>Следующие шаги

- Просмотрите [примеры на примерах политики Azure](../samples/index.md).
- Изучите [сведения о действии политик](effects.md).
- Понять, как [программно создавать политики.](../how-to/programmatically-create.md)
- Узнайте, как [получить данные о соответствии.](../how-to/get-compliance-data.md)
- Узнайте, как [исправления несовместимых ресурсов.](../how-to/remediate-resources.md)
- Просмотрите, что представляет собой группа управления с [организацией ресурсов с группами управления Azure.](../../management-groups/overview.md)
