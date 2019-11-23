---
title: Update an existing assignment from the portal
description: Learn about the mechanism for updating an existing blueprint assignment from the portal in Azure Blueprints.
ms.date: 11/21/2019
ms.topic: conceptual
ms.openlocfilehash: b4cf03d88103b85bc00dbd815816ead2740f2093
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406394"
---
# <a name="how-to-update-an-existing-blueprint-assignment"></a>Обновление существующего назначения схемы

Когда схема назначена, это назначение можно обновить. Существуют разные причины для обновления существующего назначения, в том числе:

- добавление или удаление [блокировки ресурсов](../concepts/resource-locking.md);
- изменение значений [динамических параметров](../concepts/parameters.md#dynamic-parameters);
- обновление назначения до новой **опубликованной** версии схемы.

## <a name="updating-assignments"></a>Обновление назначений

1. Выберите **Все службы** в левой области. Найдите и выберите пункт **Схемы**.

1. Выберите **Назначенные схемы** на странице слева.

1. В списке схем щелкните левой кнопкой мыши назначение схемы. Then click the **Update assignment** button OR right-click the blueprint assignment and select **Update assignment**.

   ![Update an existing blueprint assignment](../media/update-existing-assignments/update-assignment.png)

1. The **Assign blueprint** page will load pre-filled with all values from the original assignment.
   Вы можете изменить **версию определения схемы**, состояние **блокировки назначения** и любые динамические параметры в определении схемы. Внеся необходимые изменения, нажмите кнопку **Назначить**.

1. Просмотрите новое состояние на странице сведений об обновленном назначении. В этом примере мы добавили в назначение **блокировку**.

   ![Updated an existing blueprint assignment - lock mode changed](../media/update-existing-assignments/updated-assignment.png)

1. Explore details about other **Assignment operations** using the drop-down. The table of **Managed resources** updates by selected assignment operation.

   ![Assignment operations of a blueprint assignment](../media/update-existing-assignments/assignment-operations.png)

## <a name="rules-for-updating-assignments"></a>Правила обновления назначений

Развертывание обновленных назначений выполняется в соответствии с несколькими важными правилами. Эти правила определяют, что происходит с уже развернутыми ресурсами. Выполняемые действия зависят от запрошенного изменения и типа ресурса развертываемого или изменяемого артефакта.

- Назначения ролей
  - Если роль или получатель роли (пользователь, группа или приложение) изменяется, создается новое назначение роли. Ранее развернутые назначения ролей сохраняются.
- Назначения политик
  - Если изменяются параметры назначения политики, существующее назначение обновляется.
  - Если изменяется определение назначения политики, создается новое назначение политики.
    Ранее развернутые назначения политик сохраняются.
  - Если артефакт назначения политики удаляется из схемы, развернутое назначение политики сохраняется.
- Шаблоны Azure Resource Manager
  - Шаблон обрабатывается с помощью Resource Manager как **PUT**. Так как каждый тип ресурсов обрабатывает это действие по-разному, просмотрите документацию для каждого включенного ресурса, чтобы определить влияние этого действия при его выполнении схемой.

## <a name="possible-errors-on-updating-assignments"></a>Возможные ошибки при обновлении назначений

При обновлении назначений есть шанс внести изменения, выполнение которых будет неудачным. В качестве примера можно привести изменение расположения группы ресурсов после того, как она уже была развернута. Можно вносить любые изменения, которые поддерживаются [Azure Resource Manager](../../../azure-resource-manager/resource-group-overview.md), но те изменения, которые могут вызвать ошибку в Azure Resource Manager, приведут к сбою назначения.

Назначение можно обновлять сколько угодно раз. Если возникает ошибка, определите ее причину и внесите в назначение новое обновление.  Примеры сценариев, приводящих к ошибкам:

- неправильный параметр;
- объект уже существует;
- Azure Resource Manager не поддерживает запрошенное изменение.

## <a name="next-steps"></a>Дальнейшие действия

- Ознакомьтесь со сведениями о [жизненном цикле схем](../concepts/lifecycle.md).
- Узнайте, как использовать [статические и динамические параметры](../concepts/parameters.md).
- Научитесь настраивать [последовательность схемы](../concepts/sequencing-order.md).
- Узнайте, как применять [блокировку ресурсов схемы](../concepts/resource-locking.md).
- Устраняйте проблемы, возникающие во время назначения схемы, с помощью [общих инструкций по устранению неполадок](../troubleshoot/general.md).