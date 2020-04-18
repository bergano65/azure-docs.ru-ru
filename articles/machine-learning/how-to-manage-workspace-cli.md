---
title: Создание рабочих областей с помощью Azure CLI
titleSuffix: Azure Machine Learning
description: Узнайте, как использовать Azure CLI для создания нового рабочего пространства для машинного обучения Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 03/05/2020
ms.openlocfilehash: 9a7d0b75140c50df61ff63f350e5b312a6a684c7
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617778"
---
# <a name="create-a-workspace-for-azure-machine-learning-with-azure-cli"></a>Создание рабочего пространства для машинного обучения Azure с помощью Azure CLI
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

В этой статье вы узнаете, как создать рабочее пространство для машин Azure Machine Learning с помощью Azure CLI. Azure CLI предоставляет команды для управления ресурсами Azure. Расширение машинного обучения в CLI предоставляет команды для работы с ресурсами машинного обучения Azure.

## <a name="prerequisites"></a>Предварительные требования

* **Подписка Azure**. Если у вас его нет, попробуйте [бесплатную или платную версию Azure Machine Learning.](https://aka.ms/AMLFree)

* Для использования команд CLI в этом документе из **локальной среды**необходим [Azure CLI.](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

    Если вы используете [облачную оболочку Azure,](https://azure.microsoft.com//features/cloud-shell/)то доступ к CLI осуществляется через браузер и живет в облаке.

## <a name="connect-the-cli-to-your-azure-subscription"></a>Подключение интерфейса командной строки к своей подписке Azure

> [!IMPORTANT]
> Если вы используете оболочку облака Azure, вы можете пропустить этот раздел. Облачная оболочка автоматически проверяет подлинность с помощью учетной записи, вскакиваемых в подписку Azure.

Существует несколько способов проверки подлинности подписки Azure с CLI. Наиболее основным является интерактивная аутентификативная проверка с помощью браузера. Чтобы проверить подлинность в интерактивном режиме, откройте командную строку или терминал и используйте следующую команду:

```azurecli-interactive
az login
```

Если CLI сможет запустить браузер по умолчанию, он откроет в браузере страницу входа. В противном случае вам нужно открыть браузер и следовать инструкциям по командной строке. Инструкции включают просмотр [https://aka.ms/devicelogin](https://aka.ms/devicelogin) и ввод кода авторизации.

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

Для других методов проверки подлинности, [перепишитесь с Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

## <a name="install-the-machine-learning-extension"></a>Установка расширения машинного обучения

Для установки расширения машинного обучения используйте следующую команду:

```azurecli-interactive
az extension add -n azure-cli-ml
```

## <a name="create-a-workspace"></a>Создание рабочей области

Рабочее пространство Azure Machine Learning опирается на следующие службы или объекты Azure:

> [!IMPORTANT]
> Если вы не укажете существующую службу Azure, она будет создана автоматически во время создания рабочего пространства. Необходимо всегда указывать группу ресурсов.

| Служба | Параметр для указания существующего экземпляра |
| ---- | ---- |
| **Группа ресурсов Azure** | `-g <resource-group-name>`
| **Учетная запись хранения Azure** | `--storage-account <service-id>` |
| **Azure Application Insights** | `--application-insights <service-id>` |
| **Azure Key Vault**; | `--keyvault <service-id>` |
| **Реестр контейнеров Azure** | `--container-registry <service-id>` |

### <a name="create-a-resource-group"></a>Создание группы ресурсов

Рабочее пространство Azure Machine Learning должно быть создано внутри группы ресурсов. Вы можете выбрать существующую группу ресурсов или создать новую. Для __создания новой группы ресурсов__используйте следующую команду. Замените `<resource-group-name>` имя для использования для этой группы ресурсов. Заменить `<location>` область Azure для использования в этой группе ресурсов:

> [!TIP]
> Следует выбрать регион, в котором доступно машинное обучение Azure. Для получения информации [см.](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service)

```azurecli-interactive
az group create --name <resource-group-name> --location <location>
```

Ответ от этой команды аналогичен следующему JSON:

```json
{
  "id": "/subscriptions/<subscription-GUID>/resourceGroups/<resourcegroupname>",
  "location": "<location>",
  "managedBy": null,
  "name": "<resource-group-name>",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": null
}
```

Для получения дополнительной информации о работе с группами ресурсов [см.](https://docs.microsoft.com//cli/azure/group?view=azure-cli-latest)

### <a name="automatically-create-required-resources"></a>Автоматически создавать необходимые ресурсы

Чтобы создать новое рабочее пространство, где __службы автоматически создаются,__ используйте следующую команду:

> [!TIP]
> Команды в этом разделе создают базовое рабочее пространство издания. Для создания рабочего пространства `--sku enterprise` предприятия используйте коммутатор с командой. `az ml workspace create` Для получения дополнительной информации о изданиях машинного обучения Azure [смотрите, что такое машинное обучение Azure.](overview-what-is-azure-ml.md#sku)

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name>
```

> [!NOTE]
> Имя рабочего пространства является нечувствительным.

Выход этой команды аналогичен следующему JSON:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>",
  "containerRegistry": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.containerregistry/registries/<acr-name>",
  "creationTime": "2019-08-30T20:24:19.6984254+00:00",
  "description": "",
  "friendlyName": "<workspace-name>",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

### <a name="use-existing-resources"></a>Использование существующих ресурсов

Для создания рабочего пространства, использующемсуществующие ресурсы, необходимо предоставить идентификатор для ресурсов. Используйте следующие команды, чтобы получить идентификатор для служб:

> [!IMPORTANT]
> Не нужно указывать все существующие ресурсы. Вы можете указать один или несколько. Например, можно указать существующую учетную запись хранения, а рабочее пространство создаст другие ресурсы.

+ **Учетная запись хранения azure**:`az storage account show --name <storage-account-name> --query "id"`

    Ответ от этой команды аналогичен следующему тексту и идентификатор для учетной записи хранения:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"`

+ **Исследования приложений Azure**:

    1. Установите расширение информации о приложениях:

        ```azurecli-interactive
        az extension add -n application-insights
        ```

    2. Получите идентификатор службы анализа приложений:

        ```azurecli-interactive
        az monitor app-insights component show --app <application-insight-name> -g <resource-group-name> --query "id"
        ```

        Ответ от этой команды аналогичен следующему тексту и идентификатор для службы анализа приложений:

        `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>"`

+ **Убежище ключей Azure**:`az keyvault show --name <key-vault-name> --query "ID"`

    Ответ от этой команды аналогичен следующему тексту и является идентификатором для хранилища ключей:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.KeyVault/vaults/<key-vault-name>"`

+ **Реестр контейнеров Azure**:`az acr show --name <acr-name> -g <resource-group-name> --query "id"`

    Ответ от этой команды аналогичен следующему тексту иидентификатор для реестра контейнеров:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<acr-name>"`

    > [!IMPORTANT]
    > Реестр контейнеров должен иметь [учетную запись админ,](/azure/container-registry/container-registry-authentication#admin-account) прежде чем она может быть использована с рабочим пространством Azure Machine Learning.

Если у вас есть идентификаторы для ресурса (ы), которые `az workspace create -w <workspace-name> -g <resource-group-name>` вы хотите использовать в рабочей области, используйте базовую команду и добавьте параметр (ы) и id (ы) для существующих ресурсов. Например, следующая команда создает рабочее пространство, использующему существующий реестр контейнеров:

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name> --container-registry "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<acr-name>"
```

Выход этой команды аналогичен следующему JSON:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>",
  "containerRegistry": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.containerregistry/registries/<acr-name>",
  "creationTime": "2019-08-30T20:24:19.6984254+00:00",
  "description": "",
  "friendlyName": "<workspace-name>",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

## <a name="list-workspaces"></a>Список рабочих областей

Чтобы перечислить все рабочие области для подписки Azure, используйте следующую команду:

```azurecli-interactive
az ml workspace list
```

Выход этой команды аналогичен следующему JSON:

```json
[
  {
    "resourceGroup": "myresourcegroup",
    "subscriptionId": "<subscription-id>",
    "workspaceName": "myml"
  },
  {
    "resourceGroup": "anotherresourcegroup",
    "subscriptionId": "<subscription-id>",
    "workspaceName": "anotherml"
  }
]
```

Для получения дополнительной [az ml workspace list](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-list) информации см.

## <a name="get-workspace-information"></a>Получить информацию о рабочем пространстве

Чтобы получить информацию о рабочем пространстве, используйте следующую команду:

```azurecli-interactive
az ml workspace show -w <workspace-name> -g <resource-group-name>
```

Выход этой команды аналогичен следующему JSON:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/application-insight-name>",
  "creationTime": "2019-08-30T18:55:03.1807976+00:00",
  "description": "",
  "friendlyName": "",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "tags": {},
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

Для получения дополнительной информации, см [az ml рабочее пространство показать](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-show) документацию.

## <a name="update-a-workspace"></a>Обновление рабочего пространства

Для обновления рабочего пространства используйте следующую команду:

```azurecli-interactive
az ml workspace update -w <workspace-name> -g <resource-group-name>
```

Выход этой команды аналогичен следующему JSON:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/application-insight-name>",
  "creationTime": "2019-08-30T18:55:03.1807976+00:00",
  "description": "",
  "friendlyName": "",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "tags": {},
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

Для получения дополнительной [az ml workspace update](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-update) информации см.

## <a name="share-a-workspace-with-another-user"></a>Поделитесь рабочим пространством с другим пользователем

Чтобы поделиться рабочим пространством с другим пользователем в подписке, используйте следующую команду:

```azurecli-interactive
az ml workspace share -w <workspace-name> -g <resource-group-name> --user <user> --role <role>
```

Для получения дополнительной информации о элементах управления доступом на основе ролей (RBAC) с Помощью машинного обучения Azure [см.](how-to-assign-roles.md)

Для получения дополнительной [az ml workspace share](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-share) информации см.

## <a name="sync-keys-for-dependent-resources"></a>Ключи синхронизации для зависимых ресурсов

Если вы измените клавиши доступа для одного из ресурсов, используемых рабочим пространством, используйте следующую команду для синхронизации новых ключей с рабочим пространством:

```azurecli-interactive
az ml workspace sync-keys -w <workspace-name> -g <resource-group-name>
```

Для получения дополнительной информации [Regenerate storage access keys](how-to-change-storage-access-key.md)об изменении ключей см.

Для получения дополнительной информации ознакомьтесь с документацией [синхронизации рабочего пространства az ml.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-sync-keys)

## <a name="delete-a-workspace"></a>Удаление рабочей области

Чтобы удалить рабочее пространство после того, как оно больше не требуется, используйте следующую команду:

```azurecli-interactive
az ml workspace delete -w <workspace-name> -g <resource-group-name>
```

> [!IMPORTANT]
> Удаление рабочего пространства не удаляет информацию о приложении, учетную запись хранения, хранилище ключей или реестр контейнеров, используемых рабочим пространством.

Вы также можете удалить группу ресурсов, которая удаляет рабочее пространство и все другие ресурсы Azure в группе ресурсов. Чтобы удалить группу ресурсов, используйте следующую команду:

```azurecli-interactive
az group delete -g <resource-group-name>
```

Для получения дополнительной [az ml workspace delete](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-delete) информации см.

## <a name="troubleshooting"></a>Устранение неполадок

### <a name="resource-provider-errors"></a>Ошибки поставщика ресурсов

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>Перемещение рабочего пространства

> [!WARNING]
> Перемещение рабочего пространства Azure Machine Learning в другую подписку или перемещение подписки на новый арендатор не поддерживается. Это может привести к ошибкам.

### <a name="deleting-the-azure-container-registry"></a>Удаляние реестра контейнеров Azure

Рабочее пространство Azure Machine Learning использует реестр контейнеров Azure (ACR) для некоторых операций. Он автоматически создаст экземпляр ACR, когда он сначала нужен.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>Дальнейшие действия

Для получения дополнительной информации о расширении Azure [az ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml?view=azure-cli-latest) CLI для машинного обучения см.
