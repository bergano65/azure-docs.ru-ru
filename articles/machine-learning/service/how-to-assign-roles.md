---
title: Управление ролями в рабочей области машинного обучения Azure
titleSuffix: Azure Machine Learning service
description: Узнайте, как получить доступ к рабочей области машинного обучения Azure службы с помощью управления доступом на основе ролей (RBAC).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: larryfr
author: Blackmist
ms.date: 02/20/2019
ms.custom: seodec18
ms.openlocfilehash: f5cc86c40f56cee28ff6a6912acd67961285bffc
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/02/2019
ms.locfileid: "65025311"
---
# <a name="manage-access-to-an-azure-machine-learning-workspace"></a>Управление доступом к рабочей области машинного обучения Azure

В этой статье вы узнаете, как управлять доступом к рабочей области машинного обучения Azure. [Управление доступом на основе ролей (RBAC)](/azure/role-based-access-control/overview) используется для управления доступом к ресурсам Azure. Пользователи в Azure Active Directory, назначенных определенной роли, которые предоставляют доступ к ресурсам. Azure предоставляет встроенные роли и возможность создавать пользовательские роли.

## <a name="default-roles"></a>Роли по умолчанию

В рабочей области машинного обучения Azure — это ресурс Azure. Как и другие ресурсы Azure при создании новой рабочей области машинного обучения Azure, он поставляется с тремя ролями по умолчанию. Можно добавлять пользователей в рабочую область и назначить их в одну из этих встроенных ролей.

| Роль | Уровень доступа |
| --- | --- |
| **Читатель** | Действия только для чтения в рабочей области. Читателей может списка и просматривать ресурсы в рабочей области, но невозможно создать или обновить эти ресурсы. |
| **Участник** | Просмотреть, создать, изменить или удалить (если применимо) ресурсов в рабочей области. Например участников можно создать эксперимент, создать или присоединить вычислительного кластера, запуске и развертывание веб-службы. |
| **Владелец** | Полный доступ к рабочей области, включая возможность просмотреть, создать, изменить или удалить (если применимо) ресурсов в рабочей области. Кроме того вы можете изменить назначения ролей. |

> [!IMPORTANT]
> Доступ роли могут быть распространены на нескольких уровнях в Azure. Например пользователь с владельца для доступа к рабочей области отсутствуют владельца на доступ к группе ресурсов, которая содержит рабочую область. Дополнительные сведения см. в разделе [работает как RBAC](/azure/role-based-access-control/overview#how-rbac-works).

Дополнительные сведения о конкретных встроенных ролей, см. в разделе [встроенные роли Azure](/azure/role-based-access-control/built-in-roles).

## <a name="manage-workspace-access"></a>Управление доступом к рабочей области

Если вы являетесь владельцем этой рабочей области, можно добавлять и удалять роли для рабочей области. Также можно назначать роли пользователям. Воспользуйтесь следующими ссылками, чтобы узнать об управлении доступом.
- [Пользовательский Интерфейс портала Azure](/azure/role-based-access-control/role-assignments-portal)
- [PowerShell](/azure/role-based-access-control/role-assignments-powershell)
- [Интерфейс командной строки Azure](/azure/role-based-access-control/role-assignments-cli)
- [REST API](/azure/role-based-access-control/role-assignments-rest)
- [Шаблоны диспетчера ресурсов Azure](/azure/role-based-access-control/role-assignments-template)

Если вы установили [Azure Machine Learning CLI](reference-azure-machine-learning-cli.md), также можно использовать команды CLI для назначения ролей пользователям.

```azurecli-interactive 
az ml workspace share -n <workspace_name> -g <resource_group_name> --role <role_name> --user <user_corp_email_address>
```

`user` Поле — это адрес электронной почты существующего пользователя в экземпляре Azure Active Directory, где находится рабочая область родительской подписки. Вот пример того, как использовать эту команду:

```azurecli-interactive 
az ml workspace share -n my_workspace -g my_resource_group --role Contributor --user jdoe@contoson.com
```

## <a name="create-custom-role"></a>Создание настраиваемой роли

Если встроенные роли недостаточно, можно создать пользовательские роли. Пользовательские роли может быть чтение, запись, удаление и вычислительных ресурсов разрешения в этой рабочей области. Вы можете предоставить роли на уровне определенную рабочую область, конкретный ресурс группы или уровнем конкретной подписки.

> [!NOTE]
> Вы должны быть владельцем ресурса на этом уровне Создание настраиваемых ролей в рамках этого ресурса.

Чтобы создать пользовательскую роль, необходимо сначала создайте файл JSON для определения роли, указывающее разрешение и область для роли. В следующем примере определяется пользовательской роли с именем «Специалист по обработке данных» на уровне конкретной рабочей области:

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

Вы можете изменить `AssignableScopes` поле, чтобы задать область этой роли на уровне подписки, на уровне группы ресурсов или уровне определенной рабочей области.

Эту настраиваемую роль можно выполнять любые действия в рабочей области, за исключением следующих действий:

- Его невозможно создать или обновить вычислительного ресурса.
- Его невозможно удалить вычислительного ресурса.
- Не удается добавить его, удалите или измените назначения ролей.
- Он не может удалить рабочую область.

Чтобы развернуть эту настраиваемую роль, используйте следующую команду Azure CLI:

```azurecli-interactive 
az role definition create --role-definition data_scientist_role.json
```

После развертывания эта роль становится доступной в указанной рабочей области. Теперь можно добавить и назначить эту роль на портале Azure. Или можно назначить эту роль пользователю с помощью `az ml workspace share` команду интерфейса командной строки:

```azurecli-interactive
az ml workspace share -n my_workspace -g my_resource_group --role "Data Scientist" --user jdoe@contoson.com
```


Дополнительные сведения см. в разделе [пользовательские роли для ресурсов Azure](/azure/role-based-access-control/custom-roles).

## <a name="next-steps"></a>Дальнейшие действия

- [Общие сведения о безопасности предприятия](concept-enterprise-security.md)
- [Безопасно выполнять эксперименты и выводов внутри виртуальной сети](how-to-enable-virtual-network.md)
- [Руководство Обучение моделей](tutorial-train-models-with-aml.md)
