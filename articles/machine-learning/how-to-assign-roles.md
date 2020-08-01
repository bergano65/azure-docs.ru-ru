---
title: Управление ролями в рабочей области
titleSuffix: Azure Machine Learning
description: Узнайте, как получить доступ к рабочей области Машинное обучение Azure с помощью управления доступом на основе ролей (RBAC).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: Blackmist
ms.author: nigup
author: nishankgu
ms.date: 07/24/2020
ms.custom: how-to, seodec18
ms.openlocfilehash: 6a49497cbe71dddb8ab6e76be9b3679dd62b0cee
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/30/2020
ms.locfileid: "87449026"
---
# <a name="manage-access-to-an-azure-machine-learning-workspace"></a>Управление доступом к рабочей области Машинное обучение Azure
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Из этой статьи вы узнаете, как управлять доступом к рабочей области Машинное обучение Azure. [Управление доступом на основе ролей (RBAC)](/azure/role-based-access-control/overview) используется для управления доступом к ресурсам Azure. Пользователям в Azure Active Directory назначаются определенные роли, которые предоставляют доступ к ресурсам. Azure предоставляет встроенные роли и возможность создания пользовательских ролей.

## <a name="default-roles"></a>Роли по умолчанию

Рабочая область Машинного обучения Azure — это ресурс Azure. Как и в случае других ресурсов Azure, при создании рабочая область Машинного обучения Azure снабжается тремя ролями по умолчанию. Вы можете добавить пользователей в рабочую область и назначить их одной из этих встроенных ролей.

| Роль | Уровень доступа |
| --- | --- |
| **Читатель** | Действия только для чтения в рабочей области. Читатели могут перечислять и просматривать ресурсы, включая учетные данные [хранилища](how-to-access-data.md) данных, в рабочей области. Читатели не могут создавать или обновлять эти ресурсы. |
| **Участник** | Просмотр, создание, изменение или удаление (если применимо) ресурсов в рабочей области. Например, участники могут создать эксперимент, создать или подключить вычислительный кластер, выполнить запуск и развернуть веб-службу. |
| **Владелец** | Полный доступ к рабочей области, включая возможность просмотра, создания, изменения или удаления (если применимо) ресурсов в рабочей области. Кроме того, можно изменить назначения ролей. |
| **Настраиваемая роль** | Позволяет настраивать доступ к конкретным операциям управления или плоскости данных в рабочей области. Например, отправка запуска, создание вычислений, развертывание модели или регистрация набора данных. |

> [!IMPORTANT]
> Доступ к роли можно ограничить несколькими уровнями в Azure. Например, пользователь с доступом владельца к рабочей области может не иметь права владельца на группу ресурсов, содержащую рабочую область. Дополнительные сведения см. в разделе принцип [работы RBAC](/azure/role-based-access-control/overview#how-rbac-works).

Дополнительные сведения о конкретных встроенных ролях см. в статье [встроенные роли для Azure](/azure/role-based-access-control/built-in-roles).

## <a name="manage-workspace-access"></a>Управление доступом к рабочей области

Если вы являетесь владельцем рабочей области, вы можете добавлять и удалять роли для рабочей области. Можно также назначать роли пользователям. Воспользуйтесь следующими ссылками, чтобы узнать, как управлять доступом:
- [Пользовательский интерфейс портала Azure](/azure/role-based-access-control/role-assignments-portal)
- [PowerShell](/azure/role-based-access-control/role-assignments-powershell)
- [Azure CLI](/azure/role-based-access-control/role-assignments-cli)
- [REST API](/azure/role-based-access-control/role-assignments-rest)
- [Шаблоны диспетчера ресурсов Azure](/azure/role-based-access-control/role-assignments-template)

Если вы установили [машинное обучение Azure CLI](reference-azure-machine-learning-cli.md), можно использовать команды интерфейса командной строки для назначения ролей пользователям:

```azurecli-interactive 
az ml workspace share -w <workspace_name> -g <resource_group_name> --role <role_name> --user <user_corp_email_address>
```

`user`Поле является адресом электронной почты существующего пользователя в экземпляре Azure Active Directory, где находится родительская подписка рабочей области. Ниже приведен пример использования этой команды.

```azurecli-interactive 
az ml workspace share -w my_workspace -g my_resource_group --role Contributor --user jdoe@contoson.com
```

> [!NOTE]
> Команда AZ ML Workspace Share не работает для Федеративной учетной записи Azure Active Directory B2B. Вместо команды используйте портал пользовательского интерфейса Azure.


## <a name="azure-machine-learning-operations"></a>Машинное обучение Azure операции

Машинное обучение Azure встроенных действий для многих операций и задач. Полный список см. в статье [операции поставщиков ресурсов Azure](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices).

## <a name="create-custom-role"></a>Создание настраиваемой роли

Если встроенных ролей недостаточно, можно создать пользовательские роли. Пользовательские роли могут иметь разрешения на чтение, запись, удаление и вычисление ресурсов в этой рабочей области. Роль можно сделать доступной на определенном уровне рабочей области, определенном уровне группы ресурсов или определенном уровне подписки.

> [!NOTE]
> Для создания пользовательских ролей в этом ресурсе необходимо быть владельцем ресурса на этом уровне.

Чтобы создать пользовательскую роль, сначала создайте JSON-файл определения роли, указывающий разрешение и область действия для роли. В следующем примере определяется пользовательская роль с именем "Data анализу Custom" с областью действия на определенном уровне рабочей области:

`data_scientist_custom_role.json` :
```json
{
    "Name": "Data Scientist Custom",
    "IsCustom": true,
    "Description": "Can run experiment but can't create or delete compute.",
    "Actions": ["*"],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/*/delete",
        "Microsoft.MachineLearningServices/workspaces/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/delete", 
        "Microsoft.Authorization/*/write"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>/resourceGroups/<resource_group_name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace_name>"
    ]
}
```

> [!TIP]
> Вы можете изменить `AssignableScopes` это поле, чтобы задать область этой пользовательской роли на уровне подписки, на уровне группы ресурсов или на определенном уровне рабочей области.
> Приведенная выше пользовательская роль является примером, см. Некоторые Рекомендуемые [пользовательские роли для службы машинное обучение Azure](#customroles).

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

Дополнительные сведения о пользовательских ролях см. в статье [пользовательские роли Azure](/azure/role-based-access-control/custom-roles). Дополнительные сведения об операциях (действиях и не действиях), которые можно использовать с пользовательскими ролями, см. в разделе [операции с поставщиками ресурсов](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices).

## <a name="frequently-asked-questions"></a>Вопросы и ответы


### <a name="q-what-are-the-permissions-needed-to-perform-some-common-scenarios-in-the-azure-machine-learning-service"></a>У. Каковы разрешения, необходимые для выполнения некоторых распространенных сценариев в службе Машинное обучение Azure?

В следующей таблице приведена сводка действий Машинное обучение Azure и разрешений, необходимых для их выполнения в наименьшей области. Например, если действие может быть выполнено с областью действия рабочей области (столбец 4), то все более высокие области с этим разрешением также будут работать автоматически:

> [!IMPORTANT]
> Все пути в этой таблице, начинающиеся с, `/` являются **относительными путями** к `Microsoft.MachineLearningServices/` :

| Действие | Область действия уровня подписки | Область действия уровня группы ресурсов | Область уровня рабочей области |
| ----- | ----- | ----- | ----- |
| Создать рабочую область | Не требуется | Владелец или участник | Н/д (владелец или наследует роль более высокого уровня после создания) |
| Обновление выпуска рабочей области | Не требуется | Не требуется | Владелец, участник или пользовательская роль, которая позволяет:`/workspaces/write` |
| Запросить квоту на уровне подписки Амлкомпуте или задать квоту на уровне рабочей области | Владелец, участник или пользовательская роль </br>позволяя`/locations/updateQuotas/action`</br> в области действия подписки | Не санкционировано | Не санкционировано |
| Создать новый кластер вычислений | Не требуется | Не требуется | Владелец, участник или пользовательская роль, которая позволяет:`/workspaces/computes/write` |
| Создание нового вычислительного экземпляра | Не требуется | Не требуется | Владелец, участник или пользовательская роль, которая позволяет:`/workspaces/computes/write` |
| Отправка любого типа запуска | Не требуется | Не требуется | Владелец, участник или пользовательская роль, которая позволяет:`"/workspaces/*/read", "/workspaces/environments/write", "/workspaces/experiments/runs/write", "/workspaces/metadata/artifacts/write", "/workspaces/metadata/snapshots/write", "/workspaces/environments/build/action", "/workspaces/experiments/runs/submit/action", "/workspaces/environments/readSecrets/action"` |
| Публикация конечной точки конвейера | Не требуется | Не требуется | Владелец, участник или пользовательская роль, которая позволяет:`"/workspaces/pipelines/write", "/workspaces/endpoints/pipelines/*", "/workspaces/pipelinedrafts/*", "/workspaces/modules/*"` |
| Развертывание зарегистрированной модели в ресурсе AKS/ACI | Не требуется | Не требуется | Владелец, участник или пользовательская роль, которая позволяет:`"/workspaces/services/aks/write", "/workspaces/services/aci/write"` |
| Оценка по развернутой конечной точке AKS | Не требуется | Не требуется | Владелец, участник или настраиваемая роль, позволяющая: `"/workspaces/services/aks/score/action", "/workspaces/services/aks/listkeys/action"` (если не используется проверка подлинности AAD) или `"/workspaces/read"` (при использовании токена проверки подлинности) |
| Доступ к хранилищу с помощью интерактивных записных книжек | Не требуется | Не требуется | Владелец, участник или пользовательская роль, которая позволяет:`"/workspaces/computes/read", "/workspaces/notebooks/samples/read", "/workspaces/notebooks/storage/*"` |
| Создать новую настраиваемую роль | Владелец, участник или пользовательская роль, допускающая`Microsoft.Authorization/roleDefinitions/write` | Не требуется | Владелец, участник или пользовательская роль, которая позволяет:`/workspaces/computes/write` |


### <a name="q-are-we-publishing-azure-built-in-roles-for-the-machine-learning-service"></a>У. Публикуюте ли вы встроенные роли Azure для службы Машинное обучение?

Сейчас мы не публикуем [встроенные роли Azure](/azure/role-based-access-control/built-in-roles) для службы машинное обучение. Встроенная роль после публикации не может быть обновлена, и мы все еще подтверждаем определения ролей на основе пользовательских сценариев и отзывов. 

<a id="customroles"></a>

### <a name="q-are-there-some-custom-role-templates-for-the-most-common-scenarios-in-machine-learning-service"></a>У. Существуют ли шаблоны пользовательских ролей для наиболее распространенных сценариев в Машинное обучениеной службе?

Да, вот некоторые распространенные сценарии с пользовательскими предложенными определениями ролей, которые можно использовать в качестве основы для определения собственных пользовательских ролей.

* __Data анализу Custom__: позволяет анализу данных выполнять все операции в рабочей области, **за исключением**следующих:

    * Создание вычислений
    * Развертывание моделей в рабочем кластере AKS
    * Развертывание конечной точки конвейера в рабочей среде

    `data_scientist_custom_role.json` :
    ```json
    {
        "Name": "Data Scientist Custom",
        "IsCustom": true,
        "Description": "Can run experiment but can't create or delete compute or deploy production endpoints.",
        "Actions": [
            "Microsoft.MachineLearningServices/workspaces/*/read",
            "Microsoft.MachineLearningServices/workspaces/*/action",
            "Microsoft.MachineLearningServices/workspaces/*/delete",
            "Microsoft.MachineLearningServices/workspaces/*/write"
        ],
        "NotActions": [
            "Microsoft.MachineLearningServices/workspaces/delete",
            "Microsoft.MachineLearningServices/workspaces/write",
            "Microsoft.MachineLearningServices/workspaces/computes/*/write",
            "Microsoft.MachineLearningServices/workspaces/computes/*/delete", 
            "Microsoft.Authorization/*",
            "Microsoft.MachineLearningServices/workspaces/computes/listKeys/action",
            "Microsoft.MachineLearningServices/workspaces/listKeys/action",
            "Microsoft.MachineLearningServices/workspaces/services/aks/write",
            "Microsoft.MachineLearningServices/workspaces/services/aks/delete",
            "Microsoft.MachineLearningServices/workspaces/endpoints/pipelines/write"
        ],
        "AssignableScopes": [
            "/subscriptions/<subscription_id>"
        ]
    }
    ```

* __Анализу данных с ограничением Custom__: более ограниченное определение роли без подстановочных знаков в разрешенных действиях. Она может выполнять все операции внутри рабочей области, **за исключением**следующих:

    * Создание вычислений
    * Развертывание моделей в рабочем кластере AKS
    * Развертывание конечной точки конвейера в рабочей среде

    `data_scientist_restricted_custom_role.json` :
    ```json
    {
        "Name": "Data Scientist Restricted Custom",
        "IsCustom": true,
        "Description": "Can run experiment but can't create or delete compute or deploy production endpoints",
        "Actions": [
            "Microsoft.MachineLearningServices/workspaces/*/read",
            "Microsoft.MachineLearningServices/workspaces/computes/start/action",
            "Microsoft.MachineLearningServices/workspaces/computes/stop/action",
            "Microsoft.MachineLearningServices/workspaces/computes/restart/action",
            "Microsoft.MachineLearningServices/workspaces/computes/applicationaccess/action",
            "Microsoft.MachineLearningServices/workspaces/notebooks/storage/read",
            "Microsoft.MachineLearningServices/workspaces/notebooks/storage/write",
            "Microsoft.MachineLearningServices/workspaces/notebooks/storage/delete",
            "Microsoft.MachineLearningServices/workspaces/notebooks/samples/read",
            "Microsoft.MachineLearningServices/workspaces/experiments/runs/write",
            "Microsoft.MachineLearningServices/workspaces/experiments/write",
            "Microsoft.MachineLearningServices/workspaces/experiments/runs/submit/action",
            "Microsoft.MachineLearningServices/workspaces/pipelinedrafts/write",
            "Microsoft.MachineLearningServices/workspaces/metadata/snapshots/write",
            "Microsoft.MachineLearningServices/workspaces/metadata/artifacts/write",
            "Microsoft.MachineLearningServices/workspaces/environments/write",
            "Microsoft.MachineLearningServices/workspaces/models/write",
            "Microsoft.MachineLearningServices/workspaces/modules/write",
            "Microsoft.MachineLearningServices/workspaces/datasets/registered/write", 
            "Microsoft.MachineLearningServices/workspaces/datasets/registered/delete",
            "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/write",
            "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/delete",
            "Microsoft.MachineLearningServices/workspaces/computes/listNodes/action",
            "Microsoft.MachineLearningServices/workspaces/environments/build/action"
        ],
        "NotActions": [
            "Microsoft.MachineLearningServices/workspaces/computes/write",
            "Microsoft.MachineLearningServices/workspaces/write",
            "Microsoft.MachineLearningServices/workspaces/computes/delete",
            "Microsoft.MachineLearningServices/workspaces/delete",
            "Microsoft.MachineLearningServices/workspaces/computes/listKeys/action",
            "Microsoft.MachineLearningServices/workspaces/listKeys/action",
            "Microsoft.Authorization/*",
            "Microsoft.MachineLearningServices/workspaces/datasets/registered/profile/read",
            "Microsoft.MachineLearningServices/workspaces/datasets/registered/preview/read",
            "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/profile/read",
            "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/preview/read",
            "Microsoft.MachineLearningServices/workspaces/datasets/registered/schema/read",    
            "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/schema/read",
            "Microsoft.MachineLearningServices/workspaces/datastores/write",
            "Microsoft.MachineLearningServices/workspaces/datastores/delete"
        ],
        "AssignableScopes": [
            "/subscriptions/<subscription_id>"
        ]
    }
    ```

* __Млопс Custom__: позволяет назначить роль субъекту-службе и использовать ее для автоматизации конвейеров млопс. Например, для отправки запусков в уже опубликованном конвейере:

    `mlops_custom_role.json` :
    ```json
    {
        "Name": "MLOps Custom",
        "IsCustom": true,
        "Description": "Can run pipelines against a published pipeline endpoint",
        "Actions": [
            "Microsoft.MachineLearningServices/workspaces/read",
            "Microsoft.MachineLearningServices/workspaces/endpoints/pipelines/read",
            "Microsoft.MachineLearningServices/workspaces/metadata/artifacts/read",
            "Microsoft.MachineLearningServices/workspaces/metadata/snapshots/read",
            "Microsoft.MachineLearningServices/workspaces/environments/read",    
            "Microsoft.MachineLearningServices/workspaces/metadata/secrets/read",
            "Microsoft.MachineLearningServices/workspaces/modules/read",
            "Microsoft.MachineLearningServices/workspaces/experiments/runs/read",
            "Microsoft.MachineLearningServices/workspaces/datasets/registered/read",
            "Microsoft.MachineLearningServices/workspaces/datastores/read",
            "Microsoft.MachineLearningServices/workspaces/environments/write",
            "Microsoft.MachineLearningServices/workspaces/experiments/runs/write",
            "Microsoft.MachineLearningServices/workspaces/metadata/artifacts/write",
            "Microsoft.MachineLearningServices/workspaces/metadata/snapshots/write",
            "Microsoft.MachineLearningServices/workspaces/environments/build/action",
            "Microsoft.MachineLearningServices/workspaces/experiments/runs/submit/action"
        ],
        "NotActions": [
            "Microsoft.MachineLearningServices/workspaces/computes/write",
            "Microsoft.MachineLearningServices/workspaces/write",
            "Microsoft.MachineLearningServices/workspaces/computes/delete",
            "Microsoft.MachineLearningServices/workspaces/delete",
            "Microsoft.MachineLearningServices/workspaces/computes/listKeys/action",
            "Microsoft.MachineLearningServices/workspaces/listKeys/action",
            "Microsoft.Authorization/*"
        ],
        "AssignableScopes": [
            "/subscriptions/<subscription_id>"
        ]
    }
    ```

* __Администратор рабочей области__. позволяет выполнять все операции в области действия рабочей области, **за исключением**следующих:

    * Создание новой рабочей области
    * Назначение квот уровня подписки или рабочей области
    * Обновление выпуска рабочей области

    Администратор рабочей области также не может создать новую роль. Он может назначать существующие встроенные или пользовательские роли в области действия рабочей области.

    `workspace_admin_custom_role.json` :
    ```json
    {
        "Name": "Workspace Admin Custom",
        "IsCustom": true,
        "Description": "Can perform all operations except quota management and upgrades",
        "Actions": [
            "Microsoft.MachineLearningServices/workspaces/*/read",
            "Microsoft.MachineLearningServices/workspaces/*/action",
            "Microsoft.MachineLearningServices/workspaces/*/write",
            "Microsoft.MachineLearningServices/workspaces/*/delete",
            "Microsoft.Authorization/roleAssignments/*"
        ],
        "NotActions": [
            "Microsoft.MachineLearningServices/workspaces/write"
        ],
        "AssignableScopes": [
            "/subscriptions/<subscription_id>"
        ]
    }
    ```

<a name="labeler"></a>
* __Метка Custom (настраиваемая__): позволяет определить роль, ограниченную только подписями данных:

    `labeler_custom_role.json` :
    ```json
    {
        "Name": "Labeler Custom",
        "IsCustom": true,
        "Description": "Can label data for Labeling",
        "Actions": [
            "Microsoft.MachineLearningServices/workspaces/read",
            "Microsoft.MachineLearningServices/workspaces/labeling/projects/read",
            "Microsoft.MachineLearningServices/workspaces/labeling/labels/write"
        ],
        "NotActions": [
            "Microsoft.MachineLearningServices/workspaces/labeling/projects/summary/read"
        ],
        "AssignableScopes": [
            "/subscriptions/<subscription_id>"
        ]
    }
    ```

### <a name="q-how-do-i-list-all-the-custom-roles-in-my-subscription"></a>У. Разделы справки перечислить все пользовательские роли в моей подписке?

В Azure CLI выполните следующую команду.

```azurecli-interactive
az role definition list --subscription <sub-id> --custom-role-only true
```

### <a name="q-how-do-i-find-the-operations-supported-by-the-machine-learning-service"></a>У. Разделы справки найти операции, поддерживаемые службой Машинное обучение?

В Azure CLI выполните следующую команду.

```azurecli-interactive
az provider operation show –n Microsoft.MachineLearningServices
```

Их также можно найти в списке [операций поставщика ресурсов](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices).


### <a name="q-what-are-some-common-gotchas-when-using-azure-rbac"></a>У. Каковы некоторые распространенные проблемы при использовании Azure RBAC?

Ниже приведены некоторые моменты, которые следует учитывать при использовании управления доступом на основе ролей Azure (Azure RBAC).

- При создании ресурса в Azure, скажем, рабочей области, вы не являетесь владельцем рабочей области напрямую. Роль наследуется от самой высокой роли области, которой вы имеете право в этой подписке. Например, если вы являетесь администратором сети и обладаете разрешениями на создание Машинное обучение рабочей области, вам будет назначена роль администратора сети для этой рабочей области, а не роль владельца.
- При наличии двух назначений ролей для одного и того же пользователя AAD с конфликтующими разделами действий и отсутствия операции, перечисленные в неизменности одной роли, могут не действовать, если они также перечислены как действия в другой роли. Дополнительные сведения о том, как Azure анализирует назначения ролей, см. в статье [Определение того, как Azure RBAC определяет, имеет ли пользователь доступ к ресурсу](/azure/role-based-access-control/overview#how-azure-rbac-determines-if-a-user-has-access-to-a-resource) .
- Чтобы развернуть ресурсы для вычислений в виртуальной сети, необходимо явным образом иметь разрешения для "Microsoft. Network/virtualNetworks/соединение/действие" в этом ресурсе виртуальной сети.
- Для того чтобы новые назначения ролей вступили в силу по кэшированным разрешениям в стеке, иногда может потребоваться 1 час.


### <a name="q-what-permissions-do-i-need-to-use-a-user-assigned-managed-identity-with-my-amlcompute-clusters"></a>У. Какие разрешения требуются для использования назначенного пользователем управляемого удостоверения с моими кластерами Амлкомпуте?

Чтобы назначить удостоверение, назначенное пользователем в кластерах Амлкомпуте, оно должно иметь разрешения на запись для создания вычислений и предоставления [роли управляемого оператора идентификации](/azure/role-based-access-control/built-in-roles#managed-identity-operator). Дополнительные сведения об управляемых удостоверениях RBAC см. [в статье управление назначенными пользователями удостоверениями](/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal) .


### <a name="q-do-we-support-role-based-access-controls-on-the-studio-portal"></a>У. Поддерживаются ли элементы управления доступом на основе ролей на портале Studio?

Машинное обучение Azure Studio поддерживает управление доступом на основе ролей. 

> [!IMPORTANT]
> После назначения пользовательской роли с конкретными разрешениями на анализу данных в рабочей области соответствующие действия (например, Добавление кнопки вычислений) автоматически скрываются от пользователей. Скрытие этих элементов позволяет избежать путаницы при просмотре элементов управления, возвращающих уведомление о несанкционированном доступе из службы при их использовании.

### <a name="q-how-do-i-find-the-role-definition-for-a-role-in-my-subscription"></a>У. Разделы справки найти определение роли для роли в моей подписке?

В Azure CLI выполните следующую команду. Параметр `<role-name>` должен иметь тот же формат, что и команда выше.

```azurecli-interactive
az role definition list -n <role-name> --subscription <sub-id>
```

### <a name="q-how-do-i-update-a-role-definition"></a>У. Разделы справки обновить определение роли?

В Azure CLI выполните следующую команду.

```azurecli-interactive
az role definition update --role-definition update_def.json --subscription <sub-id>
```

Необходимо иметь разрешения на всю область определения новой роли. Например, если эта новая роль имеет область действия в трех подписках, необходимо иметь разрешения для всех трех подписок. 

> [!NOTE]
> Обновление ролей может занять от 15 до часа, чтобы применить их во всех назначениях ролей в этой области.
### <a name="q-can-i-define-a-role-that-prevents-updating-the-workspace-edition"></a>У. Можно ли определить роль, запрещающую обновление выпуска рабочей области? 

Да, можно определить роль, запрещающую обновлять выпуск рабочей области. Так как обновление рабочей области является вызовом исправления для объекта рабочей области, это делается путем размещения следующего действия в `"NotActions"` массиве в определении JSON: 

`"Microsoft.MachineLearningServices/workspaces/write"`

### <a name="q-what-permissions-are-needed-to-perform-quota-operations-in-a-workspace"></a>У. Какие разрешения требуются для выполнения операций с квотами в рабочей области? 

Для выполнения любой операции, связанной с квотой, в рабочей области требуются разрешения уровня подписки. Это означает, что Настройка квоты на уровне подписки или квоты на уровне рабочей области для управляемых ресурсов вычислений может произойти только при наличии разрешений на запись в области подписки. 


## <a name="next-steps"></a>Дальнейшие действия

- [Общие сведения об обеспечении безопасности на уровне предприятия](concept-enterprise-security.md)
- [Безопасный запуск экспериментов и выведение/Оценка в виртуальной сети](how-to-enable-virtual-network.md)
- [Руководство. Обучение моделей](tutorial-train-models-with-aml.md)
- [Операции с поставщиками ресурсов](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices)
