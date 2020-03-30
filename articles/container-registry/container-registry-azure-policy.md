---
title: Соответствие требованиям с использованием политики Azure
description: Назначить встроенные политики в политике Azure для аудита соответствия реестров контейнеров Azure
ms.topic: article
ms.date: 02/26/2020
ms.openlocfilehash: 012cd013de1c60fddcfb28e4bca96d761ada41ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78330742"
---
# <a name="audit-compliance-of-azure-container-registries-using-azure-policy"></a>Аудит соответствия реестрам контейнеров Azure с использованием политики Azure

[Политика Azure](../governance/policy/overview.md) — это служба в Azure, используемая для создания, присвоения и управления политиками. Эти политики гарантируют соблюдение различных правил и действий с ресурсами, что обеспечивает соответствие этих ресурсов корпоративным стандартам и соглашениям об уровне обслуживания.

В этой статье представлены встроенные политики (предварительный просмотр) для реестра контейнеров Azure. Используйте эти политики для аудита новых и существующих реестров на соответствие требованиям.

Плата за использование политики Azure не взимается.

> [!IMPORTANT]
> Эта функция в настоящее время находится на стадии предварительной версии. Предварительные версии предоставляются при условии, что вы принимаете [дополнительные условия использования][terms-of-use]. Некоторые аспекты этой функции могут быть изменены до выхода общедоступной версии.

## <a name="built-in-policy-definitions"></a>Встроенные определения политики

Следующие встроенные определения политики специфичны для реестра контейнеров Azure:

[!INCLUDE [azure-policy-samples-policies-container-registry](../../includes/azure-policy-samples-policies-container-registry.md)]

Смотрите также встроенное определение сетевой политики: [«Предварительный» контейнерный реестр должен использовать виртуальную конечную точку сетевого обслуживания.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78)

## <a name="assign-policies"></a>Назначение политик

* Назначаем политики с помощью [портала Azure,](../governance/policy/assign-policy-portal.md) [Azure CLI,](../governance/policy/assign-policy-azurecli.md) [шаблона менеджера ресурсов](../governance/policy/assign-policy-template.md)или SDK политики Azure.
* Область назначения политики для группы ресурсов, подписки или [группы управления Azure.](../governance/management-groups/overview.md) Назначения политики контейнерного реестра применяются к существующим и новым контейнерным реестрам в рамках этой области.
* Включить или отключить [правоприменение политики](../governance/policy/concepts/assignment-structure.md#enforcement-mode) в любое время.

> [!NOTE]
> После назначения или обновления политики требуется некоторое время для того, чтобы назначение было применено к ресурсам в определенной области. Смотрите информацию о [триггерах оценки политики.](../governance/policy/how-to/get-compliance-data.md#evaluation-triggers)

## <a name="review-policy-compliance"></a>Обзор соответствия политике

Доступ к информации о соответствии требованиям, генерируемой назначениями политики, с помощью портала Azure, инструментов командной строки Azure или SDK политики Azure. Для получения подробной информации [см.](../governance/policy/how-to/get-compliance-data.md)

Когда ресурс не соответствует требованиям, существует множество возможных причин. Чтобы определить причину или найти ответственное изменение, [см.](../governance/policy/how-to/determine-non-compliance.md)

### <a name="policy-compliance-in-the-portal"></a>Соответствие политике на портале:

1. Выберите **все службы**и ищите **политику.**
1. Выберите **соответствие**.
1. Используйте фильтры для ограничения состояния соответствия ![требованиям или](./media/container-registry-azure-policy/azure-policy-compliance.png)для поиска соответствия политики политике на портале.
1. Выберите политику для проверки агрегированных деталей соответствия и событий. При желании выберите определенный реестр для соответствия ресурсам.

### <a name="policy-compliance-in-the-azure-cli"></a>Соответствие требованиям политики в Azure CLI

Вы также можете использовать Azure CLI для получения данных о соответствии. Например, используйте команду [списка назначений аз](/cli/azure/policy/assignment#az-policy-assignment-list) в CLI для получения идонов политики применяемых политик реестра контейнеров Azure:

```azurecli
az policy assignment list --query "[?contains(displayName,'Container Registries')].{name:displayName, ID:id}" --output table
```

Образец вывода:

```
Name                                                                                   ID
-------------------------------------------------------------------------------------  --------------------------------------------------------------------------------------------------------------------------------
[Preview]: Container Registries should not allow unrestricted network access           /subscriptions/<subscriptionID>/providers/Microsoft.Authorization/policyAssignments/b4faf132dc344b84ba68a441
[Preview]: Container Registries should be encrypted with a Customer-Managed Key (CMK)  /subscriptions/<subscriptionID>/providers/Microsoft.Authorization/policyAssignments/cce1ed4f38a147ad994ab60a
```

Затем запустите [список состояния политики az,](/cli/azure/policy/state#az-policy-state-list) чтобы вернуть состояние соответствия JSON для всех ресурсов в определенном идентификаторе политики:

```azurecli
az policy state list \
  --resource <policyID>
```

Или запустите [список состояния политики az,](/cli/azure/policy/state#az-policy-state-list) чтобы вернуть состояние соответствия JSON определенного ресурса реестра, например *myregistry:*

```azurecli
az policy state list \
 --resource myregistry \
 --namespace Microsoft.ContainerRegistry \
 --resource-type registries \
 --resource-group myresourcegroup
```

## <a name="next-steps"></a>Дальнейшие действия

* Подробнее об определениях и [эффектах](../governance/policy/concepts/effects.md) [политики](../governance/policy/concepts/definition-structure.md) Azure

* Создание [определения пользовательской политики](../governance/policy/tutorials/create-custom-policy-definition.md)

* Узнайте больше о [возможностях управления](../governance/index.yml) в Azure


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/