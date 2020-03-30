---
title: Функции Azure Blueprints
description: Описывает функции, доступные для использования с артефактами чертежа в определениях и назначениях Azure Blueprintblueprints.
ms.date: 12/09/2019
ms.topic: reference
ms.openlocfilehash: 0aab2fe0511ccc11842d0e132a83d6e3f7fac27f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280681"
---
# <a name="functions-for-use-with-azure-blueprints"></a>Функции для использования с помощью чертежей Azure

Azure Blueprints предоставляет функции, делающие определение чертежа более динамичным. Эти функции предназначены для использования с определениями чертежей и артефактами чертежей. Артефакт шаблона менеджера ресурсов поддерживает полное использование функций менеджера ресурсов в дополнение к получению динамического значения через параметр чертежа.

Поддерживаются следующие функции:

- [Артефакты](#artifacts)
- [concat](#concat)
- [Параметры](#parameters)
- [ресурсГруппа](#resourcegroup)
- [ресурсгруппы](#resourcegroups)
- [Подписки](#subscription)

## <a name="artifacts"></a>артефакты

`artifacts(artifactName)`

Возвращает объект свойств, населенный этим испугом артефактов чертежа.

> [!NOTE]
> Функция `artifacts()` не может быть использована изнутри шаблона менеджера ресурсов. Функция может быть использована только в определении чертежа JSON или в артефакте JSON при управлении чертежей с Azure PowerShell или REST API как часть [Blueprints-as-code.](https://github.com/Azure/azure-blueprints/blob/master/README.md)

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| артефактИмя |Да |строка |Название артефакта чертежа. |

### <a name="return-value"></a>Возвращаемое значение

Объект выходных свойств. Свойства **выводов** зависят от типа эталонного артефакта чертежа. Все типы следуют формату:

```json
{
  "outputs": {collectionOfOutputProperties}
}
```

#### <a name="policy-assignment-artifact"></a>Артефакт назначения политики

```json
{
    "outputs": {
        "policyAssignmentId": "{resourceId-of-policy-assignment}",
        "policyAssignmentName": "{name-of-policy-assignment}",
        "policyDefinitionId": "{resourceId-of-policy-definition}",
    }
}
```

#### <a name="resource-manager-template-artifact"></a>Артефакт шаблона менеджера ресурсов

Свойства **выводов** возвращенного объекта определяются в шаблоне диспетчера ресурсов и возвращаются развертыванием.

#### <a name="role-assignment-artifact"></a>Артефакт назначения ролей

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

Артефакт шаблона менеджера ресурсов с идентификатором _myTemplateArtifact,_ содержащий следующее свойство вывода образца:

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

Некоторые примеры получения данных из образца _myTemplateArtifact:_

| Выражение | Тип | Значение |
|:---|:---|:---|
|`[artifacts("myTemplateArtifact").outputs.myArray]` | Array | \["первый", "второй"\] |
|`[artifacts("myTemplateArtifact").outputs.myArray[0]]` | Строка | "первый" |
|`[artifacts("myTemplateArtifact").outputs.myString]` | Строка | "моя строка значение" |
|`[artifacts("myTemplateArtifact").outputs.myObject]` | Объект | "моясобственность": "моя ценность", "другоеимущество": правда |
|`[artifacts("myTemplateArtifact").outputs.myObject.myProperty]` | Строка | "мое значение" |
|`[artifacts("myTemplateArtifact").outputs.myObject.anotherProperty]` | Bool | True |

## <a name="concat"></a>concat

`concat(string1, string2, string3, ...)`

Объединяет несколько строковых значений и возвращает результирующую строку.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| строка1 |Да |строка |Первое значение для сцепки. |
| дополнительные аргументы |нет |строка |Дополнительные значения в последовательном порядке для конкатации |

### <a name="return-value"></a>Возвращаемое значение

Строка concatenated значений.

### <a name="remarks"></a>Remarks

Функция Azure Blueprint отличается от шаблона шаблона управления ресурсами Azure тем, что она работает только со строками.

### <a name="example"></a>Пример

`concat(parameters('organizationName'), '-vm')`

## <a name="parameters"></a>параметры

`parameters(parameterName)`

Возвращает значение параметра чертежа. Указанное имя параметра должно быть определено в определении чертежа или в артефактах чертежа.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| parameterName |Да |строка |Имя параметра, который требуется вернуть. |

### <a name="return-value"></a>Возвращаемое значение

Значение указанного параметра артефакта чертежа или чертежа.

### <a name="remarks"></a>Remarks

Функция Azure Blueprint отличается от шаблона шаблона управления ресурсами Azure тем, что она работает только с параметрами чертежа.

### <a name="example"></a>Пример

Определите _основные параметры_ в определении чертежа:

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

Затем используйте _principalIds_ `parameters()` в качестве аргумента в артефакте чертежа:

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

### <a name="remarks"></a>Remarks

Функция Azure Blueprint отличается от шаблона шаблона управления ресурсами Azure. Функция `resourceGroup()` не может быть использована в артефакте уровня подписки или определении чертежа. Он может быть использован только в артефактах чертежей, которые являются частью артефакта группы ресурсов.

Общее использование `resourceGroup()` функции заключается в создании ресурсов в том же месте, что и артефакт группы ресурсов.

### <a name="example"></a>Пример

Чтобы использовать местоположение группы ресурсов, установленное в определении чертежа или во время назначения, как место для другого артефакта, объявить объект заполнителя группы ресурсов в определении чертежа. В этом примере _NetworkPlaceholder_ — это имя заполнителя группы ресурсов.

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

Затем используйте функцию `resourceGroup()` в контексте артефакта чертежа, ориентированного на объект заполнителя группы ресурсов. В этом примере артефакт шаблона развертывается в группе ресурсов _NetworkPlaceholder_ и обеспечивает расположение _ресурса_ параметров, динамически населенного местом группы ресурсов _NetworkPlaceholder._ Местоположение группы ресурсов _NetworkPlaceholder_ можно было бы статически определить по определению чертежа или динамически определить во время назначения. В любом случае артефакт шаблона предоставляется эта информация в качестве параметра и использует ее для развертывания ресурсов в нужном месте.

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

Возвращает объект, представляющий определенный артефакт группы ресурсов. В `resourceGroup()`отличие от , который требует контекста артефакта, эта функция используется для получения свойств определенного заполнителя группы ресурсов, когда не в контексте этой группы ресурсов.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| заполнительИмя |Да |строка |Имя заполнителя артефакта группы ресурсов для возврата. |

### <a name="return-value"></a>Возвращаемое значение

Возвращаемый объект имеет следующий формат:

```json
{
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
}
```

### <a name="example"></a>Пример

Чтобы использовать местоположение группы ресурсов, установленное в определении чертежа или во время назначения, как место для другого артефакта, объявить объект заполнителя группы ресурсов в определении чертежа. В этом примере _NetworkPlaceholder_ — это имя заполнителя группы ресурсов.

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

Затем используйте функцию `resourceGroups()` из контекста любого артефакта чертежа, чтобы получить ссылку на объект заполнителя группы ресурсов. В этом примере артефакт шаблона развертывается за пределами группы ресурсов _NetworkPlaceholder_ и обеспечивает _расположение_ группы ресурсов NetworkPlaceholder с расположением группы ресурсов _NetworkPlaceholder_ в шаблоне. Местоположение группы ресурсов _NetworkPlaceholder_ можно было бы статически определить по определению чертежа или динамически определить во время назначения. В любом случае артефакт шаблона предоставляется эта информация в качестве параметра и использует ее для развертывания ресурсов в нужном месте.

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

## <a name="subscription"></a>Подписка

`subscription()`

Возвращает сведения о подписке для текущего назначения чертежа.

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

Используйте имя отображения подписки и `concat()` функцию для создания конвенции именования, пройденных как _параметр resourceName_ к артефакту шаблона.

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

- Ознакомьтесь со сведениями о [жизненном цикле схем](../concepts/lifecycle.md).
- Узнайте, как использовать [статические и динамические параметры](../concepts/parameters.md).
- Научитесь настраивать [последовательность схемы](../concepts/sequencing-order.md).
- Узнайте, как применять [блокировку ресурсов схемы](../concepts/resource-locking.md).
- Узнайте, как [обновлять существующие назначения](../how-to/update-existing-assignments.md).
- Устраняйте проблемы, возникающие во время назначения схемы, с помощью [общих инструкций по устранению неполадок](../troubleshoot/general.md).