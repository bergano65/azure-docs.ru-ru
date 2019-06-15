---
title: Удаление группы ресурсов и ее содержимого в Azure Resource Manager
description: Здесь описывается, как Azure Resource Manager определяет порядок удаления ресурсов при удалении группы ресурсов. В этой статье описываются коды отклика и объясняется, как Resource Manager обрабатывает их для определения, успешно ли выполнено удаление.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/09/2018
ms.author: tomfitz
ms.custom: seodec18
ms.openlocfilehash: c38b1ccf7f7ccfe57e2b29f236f642238c4706a7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "61363671"
---
# <a name="azure-resource-manager-resource-group-deletion"></a>Удаление группы ресурсов в Azure Resource Manager

В этой статье описано, как Azure Resource Manager определяет порядок удаления ресурсов при удалении группы ресурсов.

## <a name="determine-order-of-deletion"></a>Определение порядка удаления

При удалении группы ресурсов Resource Manager определяет порядок удаления ресурсов. Он использует следующий порядок:

1. Удаляются все дочерние (вложенные) элементы.

2. Затем удаляются ресурсы, которые управляют другими ресурсами. Для ресурса может быть задано свойство `managedBy`, указывающее, что этим ресурсов управляет другой ресурс. Если это свойство задано, ресурс, который управляет каким-либо ресурсом, удаляется перед другими ресурсами.

3. Остальные ресурсы будут удалены после предыдущих двух категорий.

## <a name="resource-deletion"></a>Удаление ресурса

После определения порядка Resource Manager вызывает операции удаления для каждого ресурса. Он ожидает выполнения всех зависимостей, прежде чем продолжить.

Для синхронных операций ожидаемыми успешными кодами отклика являются:

* 200
* 204
* 404

Для асинхронных операций ожидаемый успешный ответ — 202. Resource Manager отслеживает заголовок location или заголовок асинхронной операции azure-async, чтобы определить состояние асинхронной операции удаления.
  
### <a name="errors"></a>Errors

Когда операция удаления возвращает ошибку, Resource Manager повторяет попытки вызова команды DELETE. Повторные попытки происходят для кодов состояния 5xx, 429 и 408. По умолчанию интервал времени между повторными попытками составляет 15 минут.

## <a name="after-deletion"></a>После удаления

Resource Manager отправляет вызов GET для каждого ресурса, который требовалось удалить. Ожидаемый ответ на вызов GET — 404. Когда Resource Manager получает ответ 404, он считает удаление успешно выполненным. Resource Manager также удаляет ресурс из кэша.

Однако, если вызов GET для ресурса возвращает ответ 200 или 201, Resource Manager повторно создает ресурс.

### <a name="errors"></a>Errors

Если операция GET возвращает ошибку, Resource Manager повторяет операцию GET для следующего кода ошибки:

* меньше 100;
* 408
* 429
* больше 500.

Для других кодов ошибок Resource Manager не выполняет повторных попыток, и удаление ресурса завершается сбоем.

## <a name="next-steps"></a>Дальнейшие действия

* Основные понятия Azure Resource Manager см. в [этой статье](resource-group-overview.md).
* Команды удаления см. в разделах [PowerShell](/powershell/module/az.resources/Remove-AzResourceGroup), [Azure CLI](/cli/azure/group?view=azure-cli-latest#az-group-delete) и [REST API](/rest/api/resources/resourcegroups/delete).
