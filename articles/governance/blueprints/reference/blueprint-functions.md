---
title: Функции чертежей Azure
description: Описание функций для использования с определениями и назначениями для схем Azure.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/15/2019
ms.topic: reference
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: dcf073c58a723b8dbd835ac331c0ce9d16187445
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/03/2019
ms.locfileid: "70232849"
---
# <a name="functions-for-use-with-azure-blueprints"></a>Функции для использования с чертежами Azure

Схемы Azure предоставляют функции, которые позволяют сделать определение схемы более динамичным. Эти функции предназначены для использования с определениями схем и артефактами схемы. Артефакт шаблона диспетчер ресурсов поддерживает полное использование функций диспетчер ресурсов, помимо получения динамического значения с помощью параметра схемы.

Поддерживаются следующие функции:

- [artifacts](#artifacts)
- [concat](#concat)
- [parameters](#parameters)
- [resourceGroup](#resourcegroup)
- [resourceGroups](#resourcegroups)
- [subscription](#subscription)

## <a name="artifacts"></a>artifacts

`artifacts(artifactName)`

Возвращает объект свойств, заполненных выходными данными артефактов схемы.

### <a name="parameters"></a>Параметры

| Параметр | Обязательное значение | Тип | Описание |
|:--- |:--- |:--- |:--- |
| artifactName |Да |строка |Имя артефакта схемы. |

### <a name="return-value"></a>Возвращаемое значение

Объект выходных свойств. Свойства **Outputs** зависят от типа артефакта схемы, на который указывает ссылка. Все типы имеют следующий формат:

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

#### <a name="resource-manager-template-artifact"></a>Артефакт шаблона диспетчер ресурсов

Свойства **вывода** возвращаемого объекта определяются в шаблоне диспетчер ресурсов и возвращаются развертыванием.

#### <a name="role-assignment-artifact"></a>Артефакт назначения роли

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

Артефакт шаблона диспетчер ресурсов с ИДЕНТИФИКАТОРом _митемплатеартифакт_ , содержащий следующий пример выходного свойства:

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

Ниже приведены некоторые примеры получения данных из образца _митемплатеартифакт_ .

| Выражение | Тип | Значение |
|:---|:---|:---|
|`[artifacts("myTemplateArtifact").outputs.myArray]` | Array | \["first", "second"\] |
|`[artifacts("myTemplateArtifact").outputs.myArray[0]]` | Строковое | "first" |
|`[artifacts("myTemplateArtifact").outputs.myString]` | Строковое | "my string value" |
|`[artifacts("myTemplateArtifact").outputs.myObject]` | Object | { "myproperty": "my value", "anotherProperty": true } |
|`[artifacts("myTemplateArtifact").outputs.myObject.myProperty]` | Строковое | "my value" |
|`[artifacts("myTemplateArtifact").outputs.myObject.anotherProperty]` | Bool | True |

## <a name="concat"></a>concat

`concat(string1, string2, string3, ...)`

Объединяет несколько строковых значений и возвращает результирующую строку.

### <a name="parameters"></a>Параметры

| Параметр | Обязательное значение | Тип | Описание |
|:--- |:--- |:--- |:--- |
| string1 |Да |строка |Первое значение для сцепки. |
| дополнительные аргументы |Нет |строка |Дополнительные значения в последовательном порядке для объединения |

### <a name="return-value"></a>Возвращаемое значение

Строка сцепленных значений.

### <a name="remarks"></a>Примечания

Функция Azure Blueprint отличается от функции шаблона Azure Resource Manager в том, что она работает только со строками.

### <a name="example"></a>Пример

`concat(parameters('organizationName'), '-vm')`

## <a name="parameters"></a>параметры

`parameters(parameterName)`

Возвращает значение параметра схемы. Указанное имя параметра должно быть определено в определении схемы или в артефактах схемы.

### <a name="parameters"></a>Параметры

| Параметр | Обязательное значение | Тип | Описание |
|:--- |:--- |:--- |:--- |
| parameterName |Да |строка |Имя параметра, который требуется вернуть. |

### <a name="return-value"></a>Возвращаемое значение

Значение указанной схемы или параметра артефакта схемы.

### <a name="remarks"></a>Примечания

Функция Azure Blueprint отличается от функции шаблона Azure Resource Manager в том, что она работает только с параметрами схемы.

### <a name="example"></a>Пример

Определите параметр _принЦипалидс_ в определении схемы:

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

Затем используйте _принЦипалидс_ в качестве аргумента `parameters()` для в артефакте схемы:

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

Функция Azure Blueprint отличается от функции шаблона Azure Resource Manager. `resourceGroup()` Функция не может использоваться в артефакте уровня подписки или в определении схемы. Его можно использовать только в артефактах схемы, которые являются частью артефакта группы ресурсов.

Обычно `resourceGroup()` функция используется для создания ресурсов в том же расположении, что и артефакт группы ресурсов.

### <a name="example"></a>Пример

Чтобы использовать расположение группы ресурсов, задается в определении схемы или во время назначения в качестве расположения для другого артефакта, объявите объект-заполнитель группы ресурсов в определении схемы. В этом примере _нетворкингплацехолдер_ — это имя заполнителя группы ресурсов.

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

Затем используйте `resourceGroup()` функцию в контексте артефакта схемы, предназначенного для объекта-заполнителя группы ресурсов. В этом примере артефакт шаблона развертывается в группе ресурсов _нетворкингплацехолдер_ и предоставляет параметр _ресаурцелокатион_ , динамически заполняемый с расположением группы ресурсов _нетворкингплацехолдер_ в шаблон. Расположение группы ресурсов _нетворкингплацехолдер_ могло быть статически определено в определении схемы или динамически определено во время назначения. В любом случае артефакт шаблона предоставляет сведения в качестве параметра и использует его для развертывания ресурсов в правильном расположении.

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

Возвращает объект, представляющий указанный артефакт группы ресурсов. В отличие `resourceGroup()`от, для которого требуется контекст артефакта, эта функция используется для получения свойств определенного заполнителя группы ресурсов, если он не находится в контексте этой группы ресурсов.

### <a name="parameters"></a>Параметры

| Параметр | Обязательное значение | Тип | Описание |
|:--- |:--- |:--- |:--- |
| плацехолдернаме |Да |строка |Имя заполнителя артефакта группы ресурсов, который необходимо вернуть. |

### <a name="return-value"></a>Возвращаемое значение

Возвращаемый объект имеет следующий формат:

```json
{
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
}
```

### <a name="example"></a>Пример

Чтобы использовать расположение группы ресурсов, задается в определении схемы или во время назначения в качестве расположения для другого артефакта, объявите объект-заполнитель группы ресурсов в определении схемы. В этом примере _нетворкингплацехолдер_ — это имя заполнителя группы ресурсов.

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

Затем используйте `resourceGroups()` функцию из контекста любого артефакта схемы, чтобы получить ссылку на объект заполнителя группы ресурсов. В этом примере артефакт шаблона развертывается за пределами группы ресурсов _нетворкингплацехолдер_ и предоставляет параметру _требуется расположение артефакта_ динамическое заполнение расположением группы ресурсов _нетворкингплацехолдер_ для шаблон. Расположение группы ресурсов _нетворкингплацехолдер_ могло быть статически определено в определении схемы или динамически определено во время назначения. В любом случае артефакт шаблона предоставляет сведения в качестве параметра и использует его для развертывания ресурсов в правильном расположении.

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

Возвращает сведения о подписке для текущего назначения схемы.

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

Используйте отображаемое имя подписки и `concat()` функцию, чтобы создать соглашение об именовании, передаваемое в качестве _resourceName_ параметра для артефакта шаблона.

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

## <a name="next-steps"></a>Следующие шаги

- Ознакомьтесь со сведениями о [жизненном цикле схем](../concepts/lifecycle.md).
- Узнайте, как использовать [статические и динамические параметры](../concepts/parameters.md).
- Научитесь настраивать [последовательность схемы](../concepts/sequencing-order.md).
- Узнайте, как применять [блокировку ресурсов схемы](../concepts/resource-locking.md).
- Узнайте, как [обновлять существующие назначения](../how-to/update-existing-assignments.md).
- Устраняйте проблемы, возникающие во время назначения схемы, с помощью [общих инструкций по устранению неполадок](../troubleshoot/general.md).