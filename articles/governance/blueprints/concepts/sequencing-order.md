---
title: Сведения о порядке последовательности развертывания
description: Сведения о жизненном цикле схемы и подробные сведения о каждом этапе цикла.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 11/12/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: bd12aabf0ca8f82261e6b3c677d7306ee46c4171
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53308623"
---
# <a name="understand-the-deployment-sequence-in-azure-blueprints"></a>Сведения о последовательности развертывания в Azure Blueprint

Azure Blueprint использует **порядок последовательности**, чтобы определить порядок создания ресурсов при обработке схемы. В этой статье объясняются следующие концепции:

- используемый порядок последовательности по умолчанию;
- настройка порядка;
- обработка настроенного порядка.

В примерах кода JSON есть переменные, значения которых необходимо заменить на ваши собственные значения:

- `{YourMG}` — замените это значение именем своей группы управления

## <a name="default-sequencing-order"></a>Порядок последовательности по умолчанию

Если схема не содержит директивы для порядка развертывания артефактов или директива имеет значение null, используется следующий порядок:

- Артефакты **назначения ролей** уровня подписки сортируются по имени артефакта
- Артефакты **назначения политик** уровня подписки сортируются по имени артефакта
- Артефакты **шаблонов Azure Resource Manager** уровня подписки сортируются по имени артефакта
- Артефакты **группы ресурсов** (включая дочерние артефакты) сортируются по имени заполнителя

Для каждого артефакта **группы ресурсов** при создании артефактов в этой группе ресурсов используется следующий порядок последовательности:

- Дочерние артефакты **назначения ролей** группы ресурсов сортируются по имени артефакта
- Дочерние артефакты **назначения политик** группы ресурсов сортируются по имени артефакта
- Дочерние артефакты **шаблонов Azure Resource Manager** группы ресурсов сортируются по имени артефакта

## <a name="customizing-the-sequencing-order"></a>Изменение порядка последовательности

При создании крупных схем может потребоваться создавать ресурсы в определенном порядке. В этом сценарии чаще всего схема включает несколько шаблонов Azure Resource Manager. Для поддержки этого в службе Blueprints предусмотрена возможность определить порядок последовательности.

Для определения порядка в коде JSON определяется свойство `dependsOn`. Это свойство поддерживается только схемой (для групп ресурсов) и объектами артефактов. `dependsOn` представляет собой массив строк имен артефактов, которые необходимо создать перед созданием указанного артефакта.

> [!NOTE]
> Артефакты **группы ресурсов** поддерживают свойство `dependsOn`, но не могут быть целевым объектом `dependsOn` ни по какому типу артефакта.

### <a name="example---blueprint-with-ordered-resource-group"></a>Пример — схема с упорядоченной группой ресурсов

Это пример схемы с группой ресурсов, в которой определен пользовательский порядок последовательности путем объявления значения `dependsOn` со стандартной группой ресурсов. В этом случае артефакт **assignPolicyTags** будет обрабатываться до группы ресурсов **ordered-rg**. **standard-rg** будет обрабатываться в соответствии с порядком последовательности по умолчанию.

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

## <a name="processing-the-customized-sequence"></a>Обработка пользовательской последовательности

В процессе создания используется топологическая сортировка для создания графа зависимостей схемы и ее артефактов. Выполняется проверка того, что поддерживается каждый уровень зависимостей между группами ресурсов и артефактами.

Если объявляется зависимость артефактов, которая не изменяет порядок последовательности по умолчанию, никакие изменения не вносятся. Например, это относится к группе ресурсов, которая зависит от политики уровня подписки. Другой пример — назначение дочерней политики standart-rg для группы ресурсов, которая зависит от назначения дочерней роли standard-rg для группы ресурсов. В обоих случаях `dependsOn` не изменяет порядок последовательности по умолчанию, и изменения не производятся.

## <a name="next-steps"></a>Дополнительная информация

- Ознакомьтесь с [жизненным циклом схемы](lifecycle.md).
- Узнайте, как использовать [статические и динамические параметры](parameters.md)
- Узнайте, как применять [блокировку ресурсов схемы](resource-locking.md)
- Узнайте, как [обновлять существующие назначения](../how-to/update-existing-assignments.md)
- Устраняйте проблемы, возникающие во время назначения схемы, с помощью [общих инструкций по устранению неполадок](../troubleshoot/general.md)