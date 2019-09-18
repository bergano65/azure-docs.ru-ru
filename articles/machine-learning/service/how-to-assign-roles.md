---
title: Управление ролями в рабочей области Машинное обучение Azure
titleSuffix: Azure Machine Learning
description: Узнайте, как получить доступ к рабочей области Машинное обучение Azure с помощью управления доступом на основе ролей (RBAC).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: larryfr
author: Blackmist
ms.date: 07/10/2019
ms.custom: seodec18
ms.openlocfilehash: 22ce9ea44dde6da4d1194463fe266ed00c5a3f96
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71067704"
---
# <a name="manage-access-to-an-azure-machine-learning-workspace"></a>Управление доступом к рабочей области Машинное обучение Azure

Из этой статьи вы узнаете, как управлять доступом к рабочей области Машинное обучение Azure. [Управление доступом на основе ролей (RBAC)](/azure/role-based-access-control/overview) используется для управления доступом к ресурсам Azure. Пользователям в Azure Active Directory назначаются определенные роли, которые предоставляют доступ к ресурсам. Azure предоставляет встроенные роли и возможность создания пользовательских ролей.

## <a name="default-roles"></a>Роли по умолчанию

Машинное обучение Azure рабочей областью является ресурс Azure. Как и другие ресурсы Azure, при создании новой Машинное обучение Azure рабочей области она поставляется с тремя ролями по умолчанию. Вы можете добавить пользователей в рабочую область и назначить их одной из этих встроенных ролей.

| Role | Уровень доступа |
| --- | --- |
| **Читатель** | Действия только для чтения в рабочей области. Читатели могут перечислять и просматривать ресурсы в рабочей области, но не могут создавать или обновлять эти ресурсы. |
| **Участник** | Просмотр, создание, изменение или удаление (если применимо) ресурсов в рабочей области. Например, участники могут создать эксперимент, создать или присоединить кластер, отправить запуск и развернуть веб-службу. |
| **Владелец** | Полный доступ к рабочей области, включая возможность просмотра, создания, изменения или удаления (если применимо) ресурсов в рабочей области. Кроме того, можно изменить назначения ролей. |

> [!IMPORTANT]
> Доступ к роли можно ограничить несколькими уровнями в Azure. Например, пользователь с доступом владельца к рабочей области может не иметь права владельца на группу ресурсов, содержащую рабочую область. Дополнительные сведения см. в разделе принцип [работы RBAC](/azure/role-based-access-control/overview#how-rbac-works).

Дополнительные сведения о конкретных встроенных ролях см. в статье [встроенные роли для Azure](/azure/role-based-access-control/built-in-roles).

## <a name="manage-workspace-access"></a>Управление доступом к рабочей области

Если вы являетесь владельцем рабочей области, вы можете добавлять и удалять роли для рабочей области. Можно также назначать роли пользователям. Воспользуйтесь следующими ссылками, чтобы узнать, как управлять доступом:
- [портал Azure пользовательский интерфейс](/azure/role-based-access-control/role-assignments-portal)
- [PowerShell](/azure/role-based-access-control/role-assignments-powershell)
- [Интерфейс командной строки Azure](/azure/role-based-access-control/role-assignments-cli)
- [REST API](/azure/role-based-access-control/role-assignments-rest)
- [Шаблоны диспетчера ресурсов Azure](/azure/role-based-access-control/role-assignments-template)

Если вы установили [машинное обучение Azure CLI](reference-azure-machine-learning-cli.md), можно также использовать команду CLI для назначения ролей пользователям.

```azurecli-interactive 
az ml workspace share -w <workspace_name> -g <resource_group_name> --role <role_name> --user <user_corp_email_address>
```

`user` Поле является адресом электронной почты существующего пользователя в экземпляре Azure Active Directory, где находится родительская подписка рабочей области. Ниже приведен пример использования этой команды.

```azurecli-interactive 
az ml workspace share -w my_workspace -g my_resource_group --role Contributor --user jdoe@contoson.com
```

## <a name="create-custom-role"></a>Создание настраиваемой роли

Если встроенные роли недостаточны, можно создать пользовательские роли. Пользовательские роли могут иметь разрешения на чтение, запись, удаление и вычисление ресурсов в этой рабочей области. Роль можно сделать доступной на определенном уровне рабочей области, определенном уровне группы ресурсов или определенном уровне подписки.

> [!NOTE]
> Для создания пользовательских ролей в этом ресурсе необходимо быть владельцем ресурса на этом уровне.

Чтобы создать пользовательскую роль, сначала создайте JSON-файл определения роли, указывающий разрешение и область действия для роли. В следующем примере определяется пользовательская роль с именем "Data анализу", областью действия которой является конкретный уровень рабочей области:

`data_scientist_role.json` :
```json
{
    "Name": "Data Scientist",
    "IsCustom": true,
    "Description": "Can run experiment but can't create or delete compute.",
    "Actions": ["*"],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/*/delete",
        "Microsoft.MachineLearningServices/workspaces/computes/*/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/delete", 
        "Microsoft.Authorization/*/write"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>/resourceGroups/<resource_group_name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace_name>"
    ]
}
```

Вы можете изменить `AssignableScopes` это поле, чтобы задать область этой пользовательской роли на уровне подписки, на уровне группы ресурсов или на определенном уровне рабочей области.

Эта пользовательская роль может выполнять все действия в рабочей области, за исключением следующих действий.

- Он не может создавать или обновлять ресурсы вычислений.
- Он не может удалить ресурс вычислений.
- Он не может добавлять, удалять или изменять назначения ролей.
- Она не может удалить рабочую область.

Чтобы развернуть эту настраиваемую роль, используйте следующую команду Azure CLI.

```azurecli-interactive 
az role definition create --role-definition data_scientist_role.json
```

После развертывания эта роль станет доступной в указанной рабочей области. Теперь можно добавить и назначить эту роль в портал Azure. Вы также можете назначить эту роль пользователю с помощью `az ml workspace share` команды CLI:

```azurecli-interactive
az ml workspace share -w my_workspace -g my_resource_group --role "Data Scientist" --user jdoe@contoson.com
```

Дополнительные сведения о пользовательских ролях см. в статье [пользовательские роли для ресурсов Azure](/azure/role-based-access-control/custom-roles).

Дополнительные сведения об операциях (действиях), которые можно использовать с пользовательскими ролями, см. в разделе [операции с поставщиками ресурсов](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices).

## <a name="next-steps"></a>Следующие шаги

- [Обзор корпоративной безопасности](concept-enterprise-security.md)
- [Безопасный запуск экспериментов и выведение/Оценка в виртуальной сети](how-to-enable-virtual-network.md)
- [Учебник. Обучение моделей](tutorial-train-models-with-aml.md)
- [Операции с поставщиками ресурсов](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices)