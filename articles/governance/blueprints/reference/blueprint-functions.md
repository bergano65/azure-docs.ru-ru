---
title: Функции Azure чертежей
description: Описывает функции, для использования с Azure чертежей определений и назначений.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/15/2019
ms.topic: reference
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: dc72113a8f5ed978d64d35c43e94dc9e19e4cdb1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65209410"
---
# <a name="functions-for-use-with-azure-blueprints"></a>Функции для использования с Azure чертежей

Проекты Azure предоставляет функции, создание более динамических определение схемы. Эти функции предназначены для использования с определениями схемы и схемы, в которой артефакты. Артефакт шаблона Resource Manager поддерживает полное использование функции Resource Manager, кроме получения динамическое значение в качестве параметра в рамках схемы.

Поддерживаются следующие функции:

- [artifacts](#artifacts)
- [concat](#concat)
- [parameters](#parameters)
- [resourceGroup](#resourcegroup)
- [resourceGroups](#resourcegroups)
- [subscription](#subscription)

## <a name="artifacts"></a>artifacts

`artifacts(artifactName)`

Возвращает объект свойства заполняется, артефактов чертежа выходные данные.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно для заполнения | type | Описание |
|:--- |:--- |:--- |:--- |
| artifactName |Да |string |Имя артефакта в рамках схемы. |

### <a name="return-value"></a>Возвращаемое значение

Объект свойств выходных данных. **Выводит** свойства зависят от типа, на которую ссылается артефакт чертежа. Все типы имеют следующий формат.

```json
{
  "outputs": {collectionOfOutputProperties}
}
```

#### <a name="policy-assignment-artifact"></a>Артефакта назначения политики

```json
{
    "outputs": {
        "policyAssignmentId": "{resourceId-of-policy-assignment}",
        "policyAssignmentName": "{name-of-policy-assignment}",
        "policyDefinitionId": "{resourceId-of-policy-definition}",
    }
}
```

#### <a name="resource-manager-template-artifact"></a>Артефакта шаблона Resource Manager

**Выводит** свойства возвращаемого объекта определяются в шаблоне Resource Manager и возвращаемых при развертывании.

#### <a name="role-assignment-artifact"></a>Артефакта назначения роли

```json
{
    "outputs": {
        "roleAssignmentId": "{resourceId-of-role-assignment}",
        "roleDefinitionId": "{resourceId-of-role-definition}",
        "principalId": "{principalId-role-is-being-assigned-to}",
    }
}
```

### <a name="example"></a>Пример

Артефакт шаблона Resource Manager с Идентификатором _myTemplateArtifact_ свойства, содержащий следующий образец выходных данных:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    ...
    "outputs": {
        "myArray": {
            "type": "array",
            "value": ["first", "second"]
        },
        "myString": {
            "type": "string",
            "value": "my string value"
        },
        "myObject": {
            "type": "object",
            "value": {
                "myProperty": "my value",
                "anotherProperty": true
            }
        }
    }
}
```

Некоторые примеры извлечения данных из _myTemplateArtifact_ пример являются:

| Expression | type | Значение |
|:---|:---|:---|
|`[artifacts("myTemplateArtifact").outputs.myArray]` | Массив, | \[«Первый», «second»\] |
|`[artifacts("myTemplateArtifact").outputs.myArray[0]]` | String | «Первый» |
|`[artifacts("myTemplateArtifact").outputs.myString]` | String | «Мой строковое значение» |
|`[artifacts("myTemplateArtifact").outputs.myObject]` | Object | { "myproperty": "my value", "anotherProperty": true } |
|`[artifacts("myTemplateArtifact").outputs.myObject.myProperty]` | String | «Мой value» |
|`[artifacts("myTemplateArtifact").outputs.myObject.anotherProperty]` | Логический | Истина |

## <a name="concat"></a>concat

`concat(string1, string2, string3, ...)`

Объединяет несколько строковых значений и возвращает результирующую строку.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно для заполнения | type | Описание |
|:--- |:--- |:--- |:--- |
| string1 |Да |string |Первое значение для сцепки. |
| дополнительные аргументы |Нет |string |Дополнительные значения в последовательном порядке для сцепки |

### <a name="return-value"></a>Возвращаемое значение

Строка из сцепленных значений.

### <a name="remarks"></a>Примечания

Azure Blueprint функция отличается от функции шаблона Azure Resource Manager, в том, что он работает только со строками.

### <a name="example"></a>Пример

`concat(parameters('organizationName'), '-vm')`

## <a name="parameters"></a>parameters

`parameters(parameterName)`

Возвращает значение параметра схемы. Указанное имя параметра должны быть определены, в определение схемы, или в артефактов чертежа.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно для заполнения | type | Описание |
|:--- |:--- |:--- |:--- |
| parameterName |Да |string |Имя параметра, который требуется вернуть. |

### <a name="return-value"></a>Возвращаемое значение

Значение указанного плана или параметр артефакт чертежа.

### <a name="remarks"></a>Примечания

Функция Azure Blueprint отличается от функции шаблона Azure Resource Manager, в том, что он работает только с параметрами схемы.

### <a name="example"></a>Пример

Определите параметр _principalIds_ в определение схемы:

```json
{
    "type": "Microsoft.Blueprint/blueprints",
    "properties": {
        ...
        "parameters": {
            "principalIds": {
                "type": "array",
                "metadata": {
                    "displayName": "Principal IDs",
                    "description": "This is a blueprint parameter that any artifact can reference. We'll display these descriptions for you in the info bubble. Supply principal IDs for the users,groups, or service principals for the RBAC assignment.",
                    "strongType": "PrincipalId"
                }
            }
        },
        ...
    }
}
```

Затем с помощью _principalIds_ как аргумент для `parameters()` в артефакт чертежа:

```json
{
    "type": "Microsoft.Blueprint/blueprints/artifacts",
    "kind": "roleAssignment",
    ...
    "properties": {
        "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
        "principalIds": "[parameters('principalIds')]",
        ...
    }
}
```

## <a name="resourcegroup"></a>resourceGroup

`resourceGroup()`

Возвращает объект, который представляет текущую группу ресурсов.

### <a name="return-value"></a>Возвращаемое значение

Возвращаемый объект имеет следующий формат:

```json
{
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
}
```

### <a name="remarks"></a>Примечания

Azure Blueprint функция отличается от функции шаблона Azure Resource Manager. `resourceGroup()` Функция не может использоваться в артефакт уровня подписки или определения схемы. Он может использоваться только в артефактов чертежа, которые являются частью артефакт группы ресурсов.

Обычно используются `resourceGroup()` является функция для создания ресурсов в том же расположении, в качестве артефакта группы ресурсов.

### <a name="example"></a>Пример

Чтобы использовать расположение группы ресурсов, задать в среде определения схемы или во время назначения в качестве расположения для другого артефакта, объявите объект заполнителя группы ресурсов, в определении схемы. В этом примере _NetworkingPlaceholder_ является имя местозаполнителя группы ресурсов.

```json
{
    "type": "Microsoft.Blueprint/blueprints",
    "properties": {
        ...
        "resourceGroups": {
            "NetworkingPlaceholder": {
                "location": "eastus"
            }
        }
    }
}
```

Затем с помощью `resourceGroup()` функцию в контексте артефакт чертежа, которую объект заполнителя группы ресурсов. В этом примере выполняется развертывание шаблона артефакта в _NetworkingPlaceholder_ группу ресурсов и предоставляет параметр _resourceLocation_ динамически заполняемый с  _NetworkingPlaceholder_ расположение группы ресурсов в шаблон. Расположение _NetworkingPlaceholder_ группы ресурсов могли статически определено в определение схемы или динамически определяемую во время назначения. В любом случае артефакта шаблона предоставляется эти сведения как параметр и использует его для развертывания ресурсов на правильное расположение.

```json
{
  "type": "Microsoft.Blueprint/blueprints/artifacts",
  "kind": "template",
  "properties": {
      "template": {
        ...
      },
      "resourceGroup": "NetworkingPlaceholder",
      ...
      "parameters": {
        "resourceLocation": {
          "value": "[resourceGroup().location]"
        }
      }
  }
}
```

## <a name="resourcegroups"></a>resourceGroups

`resourceGroups(placeholderName)`

Возвращает объект, представляющий группу артефакта указанного ресурса. В отличие от `resourceGroup()`, который требует контекста артефакта, эта функция используется для получения свойств заполнителя конкретный ресурс группы не в контексте этой группе ресурсов.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно для заполнения | type | Описание |
|:--- |:--- |:--- |:--- |
| placeholderName |Да |string |Имя заполнителя артефакта группы ресурсов для возврата. |

### <a name="return-value"></a>Возвращаемое значение

Возвращаемый объект имеет следующий формат:

```json
{
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
}
```

### <a name="example"></a>Пример

Чтобы использовать расположение группы ресурсов, задать в среде определения схемы или во время назначения в качестве расположения для другого артефакта, объявите объект заполнителя группы ресурсов, в определении схемы. В этом примере _NetworkingPlaceholder_ является имя местозаполнителя группы ресурсов.

```json
{
    "type": "Microsoft.Blueprint/blueprints",
    "properties": {
        ...
        "resourceGroups": {
            "NetworkingPlaceholder": {
                "location": "eastus"
            }
        }
    }
}
```

Затем с помощью `resourceGroups()` функцию в контексте любой артефакт чертежа, чтобы получить ссылку на объект заполнителя группы ресурсов. В этом примере артефакта шаблона развертывается за пределами _NetworkingPlaceholder_ группу ресурсов и предоставляет параметр _artifactLocation_ динамически заполняемый с  _NetworkingPlaceholder_ расположение группы ресурсов в шаблон. Расположение _NetworkingPlaceholder_ группы ресурсов могли статически определено в определение схемы или динамически определяемую во время назначения. В любом случае артефакта шаблона предоставляется эти сведения как параметр и использует его для развертывания ресурсов на правильное расположение.

```json
{
  "kind": "template",
  "properties": {
      "template": {
          ...
      },
      ...
      "parameters": {
        "artifactLocation": {
          "value": "[resourceGroups('NetworkingPlaceholder').location]"
        }
      }
  },
  "type": "Microsoft.Blueprint/blueprints/artifacts",
  "name": "myTemplate"
}
```

## <a name="subscription"></a>subscription

`subscription()`

Возвращает сведения о подписке для назначения текущей схемы.

### <a name="return-value"></a>Возвращаемое значение

Возвращаемый объект имеет следующий формат:

```json
{
    "id": "/subscriptions/{subscriptionId}",
    "subscriptionId": "{subscriptionId}",
    "tenantId": "{tenantId}",
    "displayName": "{name-of-subscription}"
}
```

### <a name="example"></a>Пример

Использовать отображаемое имя подписки и `concat()` функцию для создания соглашения об именовании, передаваемого в качестве параметра _resourceName_ артефакт шаблона.

```json
{
  "kind": "template",
  "properties": {
      "template": {
          ...
      },
      ...
      "parameters": {
        "resourceName": {
          "value": "[concat(subscription().displayName, '-vm')]"
        }
      }
  },
  "type": "Microsoft.Blueprint/blueprints/artifacts",
  "name": "myTemplate"
}
```

## <a name="next-steps"></a>Дальнейшие действия

- Ознакомьтесь с [жизненным циклом схемы](../concepts/lifecycle.md).
- Узнайте, как использовать [статические и динамические параметры](../concepts/parameters.md).
- Научитесь настраивать [последовательность схемы](../concepts/sequencing-order.md).
- Узнайте, как применять [блокировку ресурсов схемы](../concepts/resource-locking.md).
- Узнайте, как [обновлять существующие назначения](../how-to/update-existing-assignments.md).
- Устраняйте проблемы, возникающие во время назначения схемы, с помощью [общих инструкций по устранению неполадок](../troubleshoot/general.md).