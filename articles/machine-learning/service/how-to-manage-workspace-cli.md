---
title: Создание рабочей области с помощью Azure CLI
titleSuffix: Azure Machine Learning service
description: Узнайте, как использовать Azure CLI для создания новой рабочей области службы Машинное обучение Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 08/30/2019
ms.openlocfilehash: 1213e9bc3b27b8d5f6f6ef93b6eefa5a8c32be57
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/06/2019
ms.locfileid: "70392679"
---
# <a name="use-an-azure-resource-manager-template-to-create-a-workspace-for-azure-machine-learning-service"></a>Создание рабочей области для Машинное обучение Azure службы с помощью шаблона Azure Resource Manager

Из этой статьи вы узнаете, как создать рабочую область службы Машинное обучение Azure с помощью Azure CLI. Azure CLI предоставляет команды для управления ресурсами Azure. Расширение машинного обучения для интерфейса командной строки предоставляет команды для работы с Машинное обучение Azureными ресурсами служб.

## <a name="prerequisites"></a>Предварительные требования

* **Подписка Azure**. Если у вас ее нет, опробуйте [бесплатную или платную версию Службы машинного обучения Azure](https://aka.ms/AMLFree).

* Для использования команд интерфейса командной строки в этом документе из **локальной среды**требуется [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

    Если вы используете [Azure Cloud Shell](https://azure.microsoft.com//features/cloud-shell/), интерфейс командной строки доступен через браузер и находится в облаке.

## <a name="connect-the-cli-to-your-azure-subscription"></a>Подключение интерфейса командной строки к своей подписке Azure

> [!IMPORTANT]
> Если вы используете Azure Cloud Shell, этот раздел можно пропустить. Cloud Shell автоматически выполняет проверку подлинности, используя учетную запись, которую вы входите в подписку Azure.

Существует несколько способов проверки подлинности в подписке Azure с помощью интерфейса командной строки. Самый простой — выполнить интерактивную проверку подлинности с помощью браузера. Чтобы выполнить аутентификацию в интерактивном режиме, откройте командную строку или терминал и используйте следующую команду:

```azurecli
az login
```

Если CLI сможет запустить браузер по умолчанию, он откроет в браузере страницу входа. В противном случае необходимо открыть браузер и выполнить инструкции из командной строки. Инструкции подразумевают Просмотр [https://aka.ms/devicelogin](https://aka.ms/devicelogin) и ввод кода авторизации.

Другие методы проверки подлинности см. [в разделе Вход с помощью Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

## <a name="install-the-machine-learning-extension"></a>Установка расширения машинного обучения

Чтобы установить расширение машинного обучения, используйте следующую команду:

```azurecli-interactive
az extension add -n azure-cli-ml
```

## <a name="create-a-workspace"></a>Создать рабочую область

Рабочая область службы Машинное обучение Azure зависит от следующих служб или сущностей Azure:

> [!IMPORTANT]
> Если вы не укажете существующую службу Azure, она будет создана автоматически во время создания рабочей области. Необходимо всегда указывать группу ресурсов.

| Служба | Параметр для указания существующего экземпляра |
| ---- | ---- |
| **Группа ресурсов Azure** | `-g <resource-group-name>`
| **Учетная запись хранения Azure** | `--storage-account <service-id>` |
| **Azure Application Insights** | `--application-insights <service-id>` |
| **Хранилище ключей Azure** | `--keyvault <service-id>` |
| **Реестр контейнеров Azure** | `--container-registry <service-id>` |

### <a name="create-a-resource-group"></a>Создать группу ресурсов

Рабочая область службы Машинное обучение Azure должна быть создана внутри группы ресурсов. Вы можете выбрать существующую группу ресурсов или создать новую. Чтобы __создать новую группу ресурсов__, используйте следующую команду. Замените `<resource-group-name>` именем, которое будет использоваться для этой группы ресурсов. Замените `<location>` на регион Azure, который будет использоваться для этой группы ресурсов:

> [!TIP]
> Необходимо выбрать регион, в котором доступна служба Машинное обучение Azure. Дополнительные сведения см. в разделе [продукты, доступные по регионам](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service).

```azurecli-interactive
az group create --name <resource-group-name> --location <location>
```

Ответ этой команды аналогичен следующему JSON:

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

Дополнительные сведения о работе с группами ресурсов см. в разделе [AZ Group](https://docs.microsoft.com//cli/azure/group?view=azure-cli-latest).

### <a name="automatically-create-required-resources"></a>Автоматически создавать необходимые ресурсы

Чтобы создать новую рабочую область, в которой __службы создаются автоматически__, используйте следующую команду:

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name>
```

Выходные данные этой команды похожи на следующий код JSON:

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

Чтобы создать рабочую область, которая использует существующие ресурсы, необходимо указать идентификатор для ресурсов. Чтобы получить идентификатор для служб, используйте следующие команды:

> [!IMPORTANT]
> Вам не нужно указывать все существующие ресурсы. Можно указать один или несколько. Например, можно указать существующую учетную запись хранения, и Рабочая область создаст другие ресурсы.

+ **Учетная запись хранения Azure**:`az storage account show --name <storage-account-name> --query "id"`

    Ответ этой команды аналогичен следующему тексту, а — ИДЕНТИФИКАТОРу вашей учетной записи хранения:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"`

+ **Azure Application Insights.**

    1. Установите расширение Application Insights:

        ```bash
        az extension add -n application-insights
        ```

    2. Получите идентификатор службы Application Insights:

        ```bash
        az monitor app-insights component show --app <application-insight-name> -g <resource-group-name> --query "id"
        ```

        Ответ этой команды аналогичен следующему тексту, а — ИДЕНТИФИКАТОРу службы Application Insights:

        `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>"`

+ **Azure Key Vault**: "az keyvault показывать--Name < Key-Vault-name >--Query" ID "

    Ответ этой команды аналогичен следующему тексту, а — ИДЕНТИФИКАТОРу хранилища ключей:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.KeyVault/vaults/<key-vault-name>"`

+ **Реестр контейнеров Azure**:`az acr show --name <acr-name> -g <resource-group-name> --query "id"`

    Ответ этой команды аналогичен следующему тексту, а — ИДЕНТИФИКАТОРу реестра контейнеров:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<acr-name>"`

    > [!IMPORTANT]
    > В реестре контейнеров должна быть включена [учетная запись администратора](/azure/container-registry/container-registry-authentication#admin-account) , прежде чем ее можно будет использовать в рабочей области службы машинное обучение Azure.

Получив идентификаторы ресурсов, которые вы хотите использовать с рабочей областью, используйте команду Base `az workspace create -w <workspace-name> -g <resource-group-name>` и добавьте параметры и идентификаторы для существующих ресурсов. Например, следующая команда создает рабочую область, которая использует существующий реестр контейнеров:

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name> --container-registry "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<acr-name>"
```

Выходные данные этой команды похожи на следующий код JSON:

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

Чтобы получить список всех рабочих областей для подписки Azure, используйте следующую команду:

```azurecli-interactive
az ml workspace list
```

Выходные данные этой команды похожи на следующий код JSON:

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

Дополнительные сведения см. в документации по [списку рабочей области AZ ML](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-list) .

## <a name="get-workspace-information"></a>Получение сведений о рабочей области

Чтобы получить сведения о рабочей области, используйте следующую команду:

```azurecli-interactive
az ml workspace show -w <workspace-name> -g <resource-group-name>
```

Выходные данные этой команды похожи на следующий код JSON:

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

Дополнительные сведения см. в разделе [Рабочая область AZ ML Показать](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-show) документацию.

## <a name="update-a-workspace"></a>Обновление рабочей области

Чтобы обновить рабочую область, используйте следующую команду:

```azurecli-interactive
az ml workspace update -w <workspace-name> -g <resource-group-name>
```

Выходные данные этой команды похожи на следующий код JSON:

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

Дополнительные сведения см. в документации об [обновлении рабочей области AZ ML](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-update) .

## <a name="share-a-workspace-with-another-user"></a>Предоставление общего доступа к рабочей области другому пользователю

Чтобы предоставить общий доступ к рабочей области другому пользователю в вашей подписке, используйте следующую команду:

```azurecli-interactive
az ml workspace share -w <workspace-name> -g <resource-group-name> --user <user> --role <role>
```

Дополнительные сведения об управлении доступом на основе ролей (RBAC) с помощью службы Машинное обучение Azure см. в разделе [Manage Users and Roles](how-to-assign-roles.md).

Дополнительные сведения см. в документации по [общей рабочей области AZ ML](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-share) .

## <a name="sync-keys-for-dependent-resources"></a>Ключи синхронизации для зависимых ресурсов

При изменении ключей доступа для одного из ресурсов, используемых рабочей областью, выполните следующую команду, чтобы синхронизировать новые ключи с рабочей областью:

```azurecli-interactive
az ml workspace sync-keys -w <workspace-name> -g <resource-group-name>
```

Дополнительные сведения об изменении ключей см. в разделе [Повторное создание ключей доступа к хранилищу](how-to-change-storage-access-key.md).

Дополнительные сведения см. в документации по [ключам синхронизации в рабочей области AZ ML](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-sync-keys) .

## <a name="delete-a-workspace"></a>Удаление рабочей области

Чтобы удалить рабочую область после того, как она больше не нужна, используйте следующую команду:

```azurecli-interactive
az ml workspace delete -w <workspace-name> -g <resource-group-name>
```

> [!IMPORTANT]
> При удалении рабочей области не удаляется Application Insights, учетная запись хранения, хранилище ключей или реестр контейнеров, используемые рабочей областью.

Вы также можете удалить группу ресурсов, которая удаляет рабочую область и все остальные ресурсы Azure в группе ресурсов. Чтобы удалить группу ресурсов, используйте следующую команду:

```azurecli-interactive
az group delete -g <resource-group-name>
```

Дополнительные сведения см. в документации об [удалении рабочей области AZ ML](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-delete) .

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о расширении Azure CLI для машинного обучения см. в документации по [AZ ML](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml?view=azure-cli-latest) .
