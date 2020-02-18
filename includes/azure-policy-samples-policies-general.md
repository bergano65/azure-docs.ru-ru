---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 8319ec2bf3965ee30db3e7d4ba1346bd7a3dd7d4
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77169907"
---
|Имя |Описание |Действие |Версия |
|---|---|---|---|
|[Допустимые расположения](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/General/AllowedLocations_Deny.json) |Эта политика позволяет ограничить расположения, которые ваша организация может указать при развертывании ресурсов. Используется для соблюдения географических требований. Исключает группы ресурсов, каталоги Microsoft.AzureActiveDirectory/b2cDirectories и ресурсы, в которых используется "глобальный" регион. |deny |1.0.0 |
|[Разрешенные расположения для групп ресурсов](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/General/ResourceGroupAllowedLocations_Deny.json) |Эта политика позволяет ограничить расположения, которые ваша организация может указать при создании групп ресурсов. Используется для соблюдения географических требований. |deny |1.0.0 |
|[Допустимые типы ресурсов](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/General/AllowedResourceTypes_Deny.json) |Эта политика позволяет указать типы ресурсов, которые не может развертывать ваша организация. Эта политика затрагивает только типы ресурсов, поддерживающие использование тегов и настройку расположения. Чтобы ограничить все ресурсы, продублируйте эту политику и измените значение режима на All (Все). |deny |1.0.0 |
|[Аудит соответствия расположения группы и ресурса](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/General/ResourcesInResourceGroupLocation_Audit.json) |Аудит, в ходе которого проверяется, соответствует ли расположение ресурса расположению его группы |аудит |1.0.0 |
|[Аудит использования настраиваемых правил RBAC](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |Аудит встроенных ролей, таких как "Владелец", "Участник", "Читатель", вместо настраиваемых ролей RBAC, использование которых сопряжено с ошибками. Работа с пользовательскими ролями рассматривается как исключение и требует строгой проверки с моделированием угроз. |Audit, Disabled |1.0.0 |
|[Роли владельца пользовательской подписки не должны существовать](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |Эта политика позволяет исключить наличие ролей владельца пользовательской подписки. |Audit, Disabled |1.0.0 |
|[Недопустимые типы ресурсов](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/General/InvalidResourceTypes_Deny.json) |Эта политика позволяет указать типы ресурсов, которые не может развертывать ваша организация. |Запрет |1.0.0 |
