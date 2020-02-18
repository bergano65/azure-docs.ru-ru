---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 5a9d60cd019e044fd19a8670c0843a8d155f5433
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77166594"
---
|Имя |Описание |Действие |Версия |
|---|---|---|---|
|[Добавление тегов в группы ресурсов](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Tags/AddTag_ResourceGroup_Modify.json) |Добавляет указанный тег и значение при создании или обновлении любой группы ресурсов, пропустившей этот тег. Существующие группы ресурсов можно исправить, активировав задачу исправления. Если тег существует с другим значением, он не изменится. |modify |1.0.0 |
|[Добавление тегов к ресурсам](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Tags/AddTag_Modify.json) |Добавляет указанный тег и значение при создании или обновлении любого ресурса, пропустившего этот тег. Существующие ресурсы можно исправить, активировав задачу исправления. Если тег существует с другим значением, он не изменится. Не изменяет теги в группах ресурсов. |modify |1.0.0 |
|[Добавление или замена тегов в группах ресурсов](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Tags/AddOrReplaceTag_ResourceGroup_Modify.json) |Добавляет или заменяет указанный тег и значение при создании или обновлении любой группы ресурсов. Существующие группы ресурсов можно исправить, активировав задачу исправления. |modify |1.0.0 |
|[Добавление или замена тегов в ресурсах](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Tags/AddOrReplaceTag_Modify.json) |Добавляет или заменяет указанный тег и значение при создании или обновлении любого ресурса. Существующие ресурсы можно исправить, активировав задачу исправления. Не изменяет теги в группах ресурсов. |modify |1.0.0 |
|[Добавить тег и его значение по умолчанию](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Tags/ApplyTag_Append.json) |Добавляет указанный тег и значение при создании или обновлении любого ресурса, пропустившего этот тег. Не изменяет теги ресурсов, созданных до применения этой политики, пока эти ресурсы не будут изменены. Не применяется к группам ресурсов. Доступны новые политики с действием modify, поддерживающие исправление тегов на существующих ресурсах (см. https://aka.ms/modifydoc). |append |1.0.0 |
|[Добавить тег и его значение по умолчанию для групп ресурсов](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Tags/ResourceGroupApplyTag_Append.json) |Добавляет указанный тег и значение при создании или обновлении любой группы ресурсов, пропустившей этот тег. Не изменяет теги групп ресурсов, созданных до применения этой политики, пока эти группы ресурсов не будут изменены. Доступны новые политики с действием modify, поддерживающие исправление тегов на существующих ресурсах (см. https://aka.ms/modifydoc). |append |1.0.0 |
|[Добавить тег и его значение из группы ресурсов](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Tags/InheritTag_Append.json) |Добавляет указанный тег и его значение из группы ресурсов при создании или обновлении любого ресурса, пропустившего этот тег. Не изменяет теги ресурсов, созданных до применения этой политики, пока эти ресурсы не будут изменены. Доступны новые политики с действием modify, поддерживающие исправление тегов на существующих ресурсах (см. https://aka.ms/modifydoc). |append |1.0.0 |
|[Наследовать тег из группы ресурсов](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Tags/InheritTag_AddOrReplace_Modify.json) |Добавляет или заменяет указанный тег и значение из родительской группы ресурсов при создании или обновлении любого ресурса. Существующие ресурсы можно исправить, активировав задачу исправления. |modify |1.0.0 |
|[Наследовать тег из группы ресурсов, если он отсутствует](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Tags/InheritTag_Add_Modify.json) |Добавляет указанный тег и его значение из родительской группы ресурсов при создании или обновлении любого ресурса, пропустившего этот тег. Существующие ресурсы можно исправить, активировав задачу исправления. Если тег существует с другим значением, он не изменится. |modify |1.0.0 |
|[Требовать указанный тег](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Tags/RequireTag_Deny.json) |Принудительно применяет тег. Не применяется к группам ресурсов. |deny |1.0.0 |
|[Требовать указанный тег для групп ресурсов](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Tags/ResourceGroupRequireTag_Deny.json) |Принудительное задание тегов в группах ресурсов. |deny |1.0.0 |
|[Требовать тег и его значение](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Tags/RequireTagAndValue_Deny.json) |Принудительно применяет обязательный тег и его значение. Не применяется к группам ресурсов. |deny |1.0.0 |
|[Требовать тег и его значение в группах ресурсов](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Tags/ResourceGroupRequireTagAndValue_Deny.json) |Принудительно задает нужный тег и его значение в группах ресурсов. |deny |1.0.0 |
