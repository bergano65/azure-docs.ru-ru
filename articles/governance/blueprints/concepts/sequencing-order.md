---
title: Сведения о порядке последовательности развертывания
description: Дополнительные сведения о жизненный цикл, который проходит определение схемы и сведения о каждом этапе.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 8451b858717e1a3e66214f66db624ee41f6da375
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58434812"
---
# <a name="understand-the-deployment-sequence-in-azure-blueprints"></a>Сведения о последовательности развертывания в Azure Blueprint

Azure использует чертежей **порядок последовательности** для определения порядка создания ресурсов, при обработке назначение определения схемы. В этой статье объясняются следующие концепции:

- используемый порядок последовательности по умолчанию;
- настройка порядка;
- обработка настроенного порядка.

В примерах кода JSON есть переменные, значения которых необходимо заменить на ваши собственные значения:

- `{YourMG}` — замените это значение именем своей группы управления

## <a name="default-sequencing-order"></a>Порядок последовательности по умолчанию

Если определение схемы содержит директивы, не для заказа для развертывания артефактов или директива имеет значение null, используется следующий порядок:

- Артефакты **назначения ролей** уровня подписки сортируются по имени артефакта
- Артефакты **назначения политик** уровня подписки сортируются по имени артефакта
- Артефакты **шаблонов Azure Resource Manager** уровня подписки сортируются по имени артефакта
- Артефакты **группы ресурсов** (включая дочерние артефакты) сортируются по имени заполнителя

Для каждого артефакта **группы ресурсов** при создании артефактов в этой группе ресурсов используется следующий порядок последовательности:

- Дочерние артефакты **назначения ролей** группы ресурсов сортируются по имени артефакта
- Дочерние артефакты **назначения политик** группы ресурсов сортируются по имени артефакта
- Дочерние артефакты **шаблонов Azure Resource Manager** группы ресурсов сортируются по имени артефакта

## <a name="customizing-the-sequencing-order"></a>Изменение порядка последовательности

При создании определений больших схем, может потребоваться для ресурсов, создаваемых в определенном порядке. Наиболее распространенным шаблоном использования этого сценария при определение схемы содержит несколько шаблонов Azure Resource Manager. Для поддержки этого в службе Blueprints предусмотрена возможность определить порядок последовательности.

Для определения порядка в коде JSON определяется свойство `dependsOn`. Определение схемы, для групп ресурсов и объекты артефакт поддерживает это свойство. `dependsOn` представляет собой массив строк имен артефактов, которые необходимо создать перед созданием указанного артефакта.

### <a name="example---ordered-resource-group"></a>Пример — упорядоченная группа ресурсов

Определение схемы этот пример содержит группу ресурсов, который определен порядок пользовательских виртуализации, объявив значение `dependsOn`, а также группу ресурсов, стандартные. В этом случае артефакт **assignPolicyTags** будет обрабатываться до группы ресурсов **ordered-rg**.
**standard-rg** будет обрабатываться в соответствии с порядком последовательности по умолчанию.

```json
{
    "properties": {
        "description": "Example blueprint with custom sequencing order",
        "resourceGroups": {
            "ordered-rg": {
                "dependsOn": [
                    "assignPolicyTags"
                ],
                "metadata": {
                    "description": "Resource Group that waits for 'assignPolicyTags' creation"
                }
            },
            "standard-rg": {
                "metadata": {
                    "description": "Resource Group that follows the standard sequence ordering"
                }
            }
        },
        "targetScope": "subscription"
    },
    "id": "/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/mySequencedBlueprint",
    "type": "Microsoft.Blueprint/blueprints",
    "name": "mySequencedBlueprint"
}
```

### <a name="example---artifact-with-custom-order"></a>Пример: артефакт с пользовательским порядком последовательности

Этот пример содержит артефакт политики, который зависит от шаблона Azure Resource Manager. При использовании порядка сортировки по умолчанию артефакт политики был бы создан перед шаблоном Azure Resource Manager. Это упорядочение позволяет артефакту политики ожидать, пока будет создан шаблон Azure Resource Manager.

```json
{
    "properties": {
        "displayName": "Assigns an identifying tag",
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
        "resourceGroup": "standard-rg",
        "dependsOn": [
            "customTemplate"
        ]
    },
    "kind": "policyAssignment",
    "id": "/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/mySequencedBlueprint/artifacts/assignPolicyTags",
    "type": "Microsoft.Blueprint/artifacts",
    "name": "assignPolicyTags"
}
```

### <a name="example---subscription-level-template-artifact-depending-on-a-resource-group"></a>Пример — артефакта шаблона уровня подписки, в зависимости от группы ресурсов

Этот пример предназначен для шаблона Resource Manager, развернутых на уровне подписки, чтобы он зависел от группы ресурсов. По умолчанию порядка сортировки, артефакты уровня подписки приведет к созданию перед любой группы ресурсов и дочерних артефактов в эти группы ресурсов. Группа ресурсов определяется в определение схемы следующим образом:

```json
"resourceGroups": {
    "wait-for-me": {
        "metadata": {
            "description": "Resource Group that is deployed prior to the subscription level template artifact"
        }
    }
}
```

Артефакта шаблона уровня подписки, в зависимости от **ожидания для me** группы ресурсов определяется следующим образом:

```json
{
    "properties": {
        "template": {
            ...
        },
        "parameters": {
            ...
        },
        "dependsOn": ["wait-for-me"],
        "displayName": "SubLevelTemplate",
        "description": ""
    },
    "kind": "template",
    "id": "/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/mySequencedBlueprint/artifacts/subtemplateWaitForRG",
    "type": "Microsoft.Blueprint/blueprints/artifacts",
    "name": "subtemplateWaitForRG"
}
```

## <a name="processing-the-customized-sequence"></a>Обработка пользовательской последовательности

В процессе создания используется топологическая сортировка для создания графа зависимостей схемы и ее артефактов. Выполняется проверка того, что поддерживается каждый уровень зависимостей между группами ресурсов и артефактами.

Если объявляется зависимость артефактов, которая не изменяет порядок последовательности по умолчанию, никакие изменения не вносятся. Например, это относится к группе ресурсов, которая зависит от политики уровня подписки. Другой пример — назначение дочерней политики standart-rg для группы ресурсов, которая зависит от назначения дочерней роли standard-rg для группы ресурсов. В обоих случаях `dependsOn` не изменяет порядок последовательности по умолчанию, и изменения не производятся.

## <a name="next-steps"></a>Дальнейшие действия

- Ознакомьтесь с [жизненным циклом схемы](lifecycle.md).
- Узнайте, как использовать [статические и динамические параметры](parameters.md).
- Узнайте, как применять [блокировку ресурсов схемы](resource-locking.md).
- Узнайте, как [обновлять существующие назначения](../how-to/update-existing-assignments.md).
- Устраняйте проблемы, возникающие во время назначения схемы, с помощью [общих инструкций по устранению неполадок](../troubleshoot/general.md).