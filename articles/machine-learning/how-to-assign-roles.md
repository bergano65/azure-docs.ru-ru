---
title: Управление ролями в рабочем пространстве
titleSuffix: Azure Machine Learning
description: Узнайте, как получить доступ к рабочей области Azure Machine Learning с помощью элемента управления доступом на основе ролей (RBAC).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: larryfr
author: Blackmist
ms.date: 03/06/2020
ms.custom: seodec18
ms.openlocfilehash: 127a0a2b7f7573db91df9347169e90de3e14c4c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270099"
---
# <a name="manage-access-to-an-azure-machine-learning-workspace"></a>Управление доступом к рабочему пространству машинного обучения Azure
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

В этой статье вы узнаете, как управлять доступом к рабочему пространству машинного обучения Azure. Для управления доступом к ресурсам Azure используется [элемент управления доступом на основе ролей (RBAC).](/azure/role-based-access-control/overview) Пользователям в вашем каталоге Azure Active каталогу присваиваются определенные роли, которые предоставляют доступ к ресурсам. Azure предоставляет как встроенные роли, так и возможность создавать пользовательские роли.

## <a name="default-roles"></a>Роли по умолчанию

Рабочее пространство Azure Machine Learning — это ресурс Azure. Как и другие ресурсы Azure, при создании нового рабочего пространства Azure Machine Learning имеется три роли по умолчанию. Можно добавить пользователей в рабочее пространство и назначить им одну из этих встроенных ролей.

| Роль | Уровень доступа |
| --- | --- |
| **Читатель** | Действия только для чтения в рабочей области. Читатели могут перечислять и просматривать ресурсы в рабочей области, но не могут создавать или обновлять эти ресурсы. |
| **Участник** | Просматривайте, создавайте, отсваивайте или удаляйте (если это применимо) активы в рабочей области. Например, авторы могут создать эксперимент, создать или прикрепить вычислительный кластер, отправить запуск и развернуть веб-службу. |
| **Владелец** | Полный доступ к рабочему пространству, включая возможность просмотра, создания, отсвазания или удаления (где это применимо) ресурсов в рабочей области. Кроме того, можно изменить назначения ролей. |

> [!IMPORTANT]
> Доступ к роли может быть доступен на нескольких уровнях azure. Например, человек с доступом владельца к рабочей области может не иметь доступа владельца к группе ресурсов, содержащей рабочее пространство. Для получения дополнительной информации [смотрите, как работает RBAC](/azure/role-based-access-control/overview#how-rbac-works).

Для получения дополнительной информации о [Built-in roles for Azure](/azure/role-based-access-control/built-in-roles)конкретных встроенных ролях см.

## <a name="manage-workspace-access"></a>Управление доступом к рабочей области

Если вы являетесь владельцем рабочего пространства, можно добавить и удалить роли для рабочего пространства. Можно также назначить роли пользователям. Используйте следующие ссылки, чтобы узнать, как управлять доступом:
- [UI портал Azure](/azure/role-based-access-control/role-assignments-portal)
- [PowerShell](/azure/role-based-access-control/role-assignments-powershell)
- [Лазурный CLI](/azure/role-based-access-control/role-assignments-cli)
- [REST API](/azure/role-based-access-control/role-assignments-rest)
- [Шаблоны диспетчера ресурсов Azure](/azure/role-based-access-control/role-assignments-template)

Если вы установили [CLI машинного обучения Azure,](reference-azure-machine-learning-cli.md)вы также можете использовать команду CLI для присвоения ролей пользователям.

```azurecli-interactive 
az ml workspace share -w <workspace_name> -g <resource_group_name> --role <role_name> --user <user_corp_email_address>
```

Поле `user` — это адрес электронной почты существующего пользователя в экземпляре Active Directory, где живет родительская подписка рабочего пространства. Вот пример того, как использовать эту команду:

```azurecli-interactive 
az ml workspace share -w my_workspace -g my_resource_group --role Contributor --user jdoe@contoson.com
```

## <a name="create-custom-role"></a>Создание настраиваемой роли

Если встроенные роли недостаточны, можно создать пользовательские роли. Пользовательские роли могли читать, писать, удалять и вычислять разрешения ресурсов в этом рабочем пространстве. Вы можете сделать роль доступной на определенном уровне рабочей области, на определенном уровне группы ресурсов или на определенном уровне подписки.

> [!NOTE]
> Вы должны быть владельцем ресурса на этом уровне для создания пользовательских ролей в этом ресурсе.

Чтобы создать пользовательскую роль, сначала создайте определение роли файла JSON, которое определяет разрешение и область для роли. Следующий пример определяет пользовательскую роль под названием "Data Scientist", приравненный к определенному рабочему уровню:

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

Можно изменить `AssignableScopes` поле, чтобы установить область этой пользовательской роли на уровне подписки, на уровне группы ресурсов или на определенном уровне рабочей области.

Эта пользовательская роль может делать все в рабочей области, за исключением следующих действий:

- Он не может создать или обновить вычислительный ресурс.
- Он не может удалить вычислительный ресурс.
- Он не может добавлять, удалять или изменять назначения ролей.
- Он не может удалить рабочее пространство.

Чтобы развернуть эту пользовательскую роль, используйте следующую команду Azure CLI:

```azurecli-interactive 
az role definition create --role-definition data_scientist_role.json
```

После развертывания эта роль становится доступной в указанном рабочем пространстве. Теперь вы можете добавить и назначить эту роль на портале Azure. Или вы можете назначить эту роль пользователю с помощью команды `az ml workspace share` CLI:

```azurecli-interactive
az ml workspace share -w my_workspace -g my_resource_group --role "Data Scientist" --user jdoe@contoson.com
```

Для получения дополнительной информации [Custom roles for Azure resources](/azure/role-based-access-control/custom-roles)о пользовательских ролях см.

Для получения дополнительной информации об операциях (действиях), пригодных для использования пользовательскими ролями, [см.](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices)


## <a name="frequently-asked-questions"></a>Часто задаваемые вопросы


### <a name="q-what-are-the-permissions-needed-to-perform-various-actions-in-the-azure-machine-learning-service"></a>У. Какие разрешения необходимы для выполнения различных действий в службе машинного обучения Azure?

Ниже приводится сводка действий по машинному обучению Azure и разрешений, необходимых для их выполнения по крайней мере области. В качестве примера, если действие может быть выполнено с помощью области рабочего пространства (Column 4), то все вышестоящие области с этим разрешением также будут работать автоматически. Все пути в этой таблице являются `Microsoft.MachineLearningServices/` **относительными путями** к .

| Действие | Сфера действия на уровне подписки | Область ресурсов группы ресурсов | Сфера деятельности на уровне рабочего пространства |
|---|---|---|---|
| Создать рабочую область | Не требуется | Владелец или вкладчик | N/A (становится Владельцем или наследует более высокую роль сферы после создания) |
| Создание нового вычислительного кластера | Не требуется | Не требуется | Владелец, участник или пользовательская роль, позволяющая:`workspaces/computes/write` |
| Создание нового ноутбука VM | Не требуется | Владелец или вкладчик | Невозможно |
| Создание нового экземпляра вычислений | Не требуется | Не требуется | Владелец, участник или пользовательская роль, позволяющая:`workspaces/computes/write` |
| Активность плоскости данных, например отправка запуска, доступ к данным, развертывание модели или публикация конвейера | Не требуется | Не требуется | Владелец, участник или пользовательская роль, позволяющая:`workspaces/*/write` <br/> Обратите внимание, что для доступа msI к данным в учетной записи хранилища также необходим магазин данных, зарегистрированный в рабочей области. |


### <a name="q-how-do-i-list-all-the-custom-roles-in-my-subscription"></a>У. Как перечислить все пользовательские роли в моей подписке?

В Azure CLI запустите следующую команду.

```azurecli-interactive
az role definition list --subscription <sub-id> --custom-role-only true
```

### <a name="q-how-do-i-find-the-role-definition-for-a-role-in-my-subscription"></a>У. Как найти определение роли роли в моей подписке?

В Azure CLI запустите следующую команду. Обратите `<role-name>` внимание, что должно быть в том же формате, возвращенном командой выше.

```azurecli-interactive
az role definition list -n <role-name> --subscription <sub-id>
```

### <a name="q-how-do-i-update-a-role-definition"></a>У. Как обновить определение роли?

В Azure CLI запустите следующую команду.

```azurecli-interactive
az role definition update --role-definition update_def.json --subscription <sub-id>
```

Обратите внимание, что необходимо иметь разрешения на всю область определения новой роли. Например, если эта новая роль имеет область действия трех подписок, необходимо иметь разрешения на все три подписки. 

> [!NOTE]
> Обновление ролей может занять от 15 минут до часа, чтобы применить во всех назначениях ролей в этой области.
### <a name="q-can-i-define-a-role-that-prevents-updating-the-workspace-edition"></a>У. Могу ли я определить роль, которая препятствует обновлению рабочего пространства Edition? 

Да, можно определить роль, которая предотвращает обновление рабочего пространства Edition. Поскольку обновление рабочей области является вызовом PATCH на объекте рабочего `"NotActions"` пространства, вы делаете это, помещая следующее действие в массив в определение JSON: 

`"Microsoft.MachineLearningServices/workspaces/write"`

### <a name="q-what-permissions-are-needed-to-perform-quota-operations-in-a-workspace"></a>У. Какие разрешения необходимы для выполнения операций квот в рабочей области? 

Для выполнения любых операций, связанных с квотами в рабочем пространстве, необходимы разрешения уровня подписки. Это означает, что установление квоты уровня подписки или рабочего уровня для управляемых вычислительных ресурсов может произойти только в том случае, если у вас есть разрешения на написание в области подписки. 


## <a name="next-steps"></a>Дальнейшие действия

- [Общие сведения об обеспечении безопасности на уровне предприятия](concept-enterprise-security.md)
- [Безопасно запустить эксперименты и выводы / оценка внутри виртуальной сети](how-to-enable-virtual-network.md)
- [Руководство. Обучение моделей](tutorial-train-models-with-aml.md)
- [Операции с поставщиками ресурсов](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices)
