---
title: Создание рабочей области с помощью Azure CLI
titleSuffix: Azure Machine Learning
description: Сведения о том, как применить Azure CLI для создания рабочей области Машинного обучения Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: larryfr
author: Blackmist
ms.date: 06/25/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 1cc280dc12fcb462e11a568910eef053e4bdac50
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87319700"
---
# <a name="create-a-workspace-for-azure-machine-learning-with-azure-cli"></a>Создание рабочей области для Машинного обучения Azure с помощью Azure CLI
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

В этой статье вы узнаете, как создавать рабочие области Машинного обучения Azure с помощью Azure CLI. Azure CLI предоставляет команды для управления ресурсами Azure. Расширение машинного обучения для интерфейса командной строки предоставляет команды для работы с ресурсами Машинного обучения Azure.

## <a name="prerequisites"></a>Предварительные требования

* **Подписка Azure**. Если у вас ее нет, используйте [бесплатную или платную версию Машинного обучения Azure](https://aka.ms/AMLFree).

* Чтобы выполнять приведенные в этом документе команды CLI в **локальной среде**, вам потребуется [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

    Если вы используете [Azure Cloud Shell](https://azure.microsoft.com//features/cloud-shell/), интерфейс командной строки доступен через браузер и находится в облаке.

## <a name="connect-the-cli-to-your-azure-subscription"></a>Подключение интерфейса командной строки к своей подписке Azure

> [!IMPORTANT]
> Если вы используете Azure Cloud Shell, этот раздел можно пропустить. Cloud Shell автоматически выполняет аутентификацию, используя учетную запись, с помощью которой вы вошли в подписку Azure.

Существует несколько способов аутентификации в подписке Azure с помощью CLI. Самый простой — выполнить интерактивную аутентификацию с помощью браузера. Чтобы выполнить аутентификацию в интерактивном режиме, откройте командную строку или терминал и выполните следующую команду:

```azurecli-interactive
az login
```

Если CLI сможет запустить браузер по умолчанию, он откроет в браузере страницу входа. Или откройте браузер и выполните инструкции из командной строки. В инструкции входит переход к [https://aka.ms/devicelogin](https://aka.ms/devicelogin) и ввод кода авторизации.

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

Другие методы аутентификации см. в статье [Вход с помощью Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

## <a name="install-the-machine-learning-extension"></a>Установка расширения машинного обучения

Чтобы установить расширение машинного обучения, выполните следующую команду:

```azurecli-interactive
az extension add -n azure-cli-ml
```

## <a name="create-a-workspace"></a>Создание рабочей области

Рабочая область Машинного обучения Azure использует следующие службы или сущности Azure:

> [!IMPORTANT]
> Если вы не укажете существующую службу Azure, она будет создана автоматически во время создания рабочей области. Обязательно укажите группу ресурсов. При присоединении собственной учетной записи хранения убедитесь, что она соответствует следующим критериям:
>
> * Учетная запись хранения _не_ является учетной записью premium (Premium_LRS и Premium_GRS)
> * Включены как BLOB-объекты Azure, так и возможности файлов Azure.
> * Иерархическое пространство имен (ADLS Gen 2) отключено
>
> Эти требования относятся только к учетной записи хранения _по умолчанию_ , используемой рабочей областью.

| Служба | Параметр для указания существующего экземпляра |
| ---- | ---- |
| **Группа ресурсов Azure** | `-g <resource-group-name>`
| **Учетная запись хранения Azure** | `--storage-account <service-id>` |
| **Azure Application Insights** | `--application-insights <service-id>` |
| **Хранилище ключей Azure** | `--keyvault <service-id>` |
| **Реестр контейнеров Azure** | `--container-registry <service-id>` |

### <a name="create-a-resource-group"></a>Создание группы ресурсов

Рабочая область Машинного обучения Azure должна создаваться внутри группы ресурсов. Вы можете выбрать существующую группу ресурсов или создать новую. Выполните следующую команду, чтобы __создать новую группу ресурсов__. Замените `<resource-group-name>` именем, которое будет использоваться для этой группы ресурсов. Замените `<location>` регионом Azure, который будет использоваться для этой группы ресурсов:

> [!TIP]
> Необходимо выбрать регион, в котором доступно Машинное обучение Azure. Дополнительные сведения см. в статье [Доступность продуктов по регионам](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service).

```azurecli-interactive
az group create --name <resource-group-name> --location <location>
```

Результат этой команды аналогичен следующему коду JSON:

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

Дополнительные сведения о работе с группами ресурсов см. в разделе [az group](https://docs.microsoft.com//cli/azure/group?view=azure-cli-latest).

### <a name="automatically-create-required-resources"></a>Автоматическое создание необходимых ресурсов

Чтобы создать новую рабочую область, в которой __службы создаются автоматически__, используйте следующую команду:

> [!TIP]
> Команды в этом разделе создают рабочую область выпуска "Базовый". Чтобы создать корпоративную рабочую область, используйте параметр `--sku enterprise` с командой `az ml workspace create`. Дополнительные сведения о выпусках Машинного обучения Azure см. в соответствующем разделе статьи [Что такое служба "Машинное обучение Microsoft Azure"?](overview-what-is-azure-ml.md#sku).

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name>
```

> [!NOTE]
> В имени рабочей области не учитывается регистр.

Результат этой команды аналогичен следующему коду JSON:

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

Чтобы создать рабочую область, которая использует существующие ресурсы, необходимо указать идентификатор для ресурсов. Чтобы получить идентификатор служб, используйте следующие команды.

> [!IMPORTANT]
> Вам не нужно указывать все существующие ресурсы. Можно указать один или несколько. Например, можно указать существующую учетную запись хранения, и рабочая область создаст остальные ресурсы.

+ **Учетная запись хранения Azure**: `az storage account show --name <storage-account-name> --query "id"`

    Ответ этой команды аналогичен следующему тексту и является идентификатором вашей учетной записи хранения:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"`

    > [!IMPORTANT]
    > Если вы хотите использовать существующую учетную запись хранения Azure, она не может быть учетной записью Premium (Premium_LRS и Premium_GRS). Он также не может иметь иерархическое пространство имен (используется с Azure Data Lake Storage 2-го поколения). Ни хранилище класса Premium, ни иерархическое пространство имен не поддерживаются в учетной записи хранения _по умолчанию_ рабочей области. Вы можете использовать хранилище класса Premium или иерархическое пространство имен с учетными записями хранения, _отличными от стандартных_ .

+ **Azure Application Insights.**

    1. Установите расширение Application Insights:

        ```azurecli-interactive
        az extension add -n application-insights
        ```

    2. Получите идентификатор службы Application Insights:

        ```azurecli-interactive
        az monitor app-insights component show --app <application-insight-name> -g <resource-group-name> --query "id"
        ```

        Ответ этой команды аналогичен следующему тексту и является идентификатором вашей службы Application Insights:

        `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>"`

+ **Azure Key Vault**: `az keyvault show --name <key-vault-name> --query "ID"`

    Ответ этой команды аналогичен следующему тексту и является идентификатором вашего хранилища ключей:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.KeyVault/vaults/<key-vault-name>"`

+ **Реестр контейнеров Azure**: `az acr show --name <acr-name> -g <resource-group-name> --query "id"`

    Ответ этой команды аналогичен следующему тексту и является идентификатором вашего реестра контейнеров:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<acr-name>"`

    > [!IMPORTANT]
    > В реестре контейнеров должна быть включена [учетная запись администратора](/azure/container-registry/container-registry-authentication#admin-account), прежде чем его можно будет использовать в рабочей области Машинного обучения Azure.

Получив идентификаторы ресурсов, которые вы хотите применить с рабочей областью, используйте базовую команду `az workspace create -w <workspace-name> -g <resource-group-name>` и добавьте параметры и идентификаторы существующих ресурсов. Например, следующая команда создает рабочую область, которая использует существующий реестр контейнеров:

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name> --container-registry "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<acr-name>"
```

Результат этой команды аналогичен следующему коду JSON:

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

## <a name="list-workspaces"></a>Вывод списка рабочих областей

Чтобы получить список всех рабочих областей для подписки Azure, используйте следующую команду:

```azurecli-interactive
az ml workspace list
```

Результат этой команды аналогичен следующему коду JSON:

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

Дополнительные сведения см. в документации по команде [az ml workspace list](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-list).

## <a name="get-workspace-information"></a>Получение сведений о рабочей области

Чтобы получить сведения о рабочей области, используйте следующую команду:

```azurecli-interactive
az ml workspace show -w <workspace-name> -g <resource-group-name>
```

Результат этой команды аналогичен следующему коду JSON:

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

Дополнительные сведения см. в документации по команде [az ml workspace show](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-show).

## <a name="update-a-workspace"></a>Обновление рабочей области

Обновите рабочую область с помощью следующей команды:

```azurecli-interactive
az ml workspace update -w <workspace-name> -g <resource-group-name>
```

Результат этой команды аналогичен следующему коду JSON:

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

Дополнительные сведения см. в документации по команде [az ml workspace update](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-update).

## <a name="share-a-workspace-with-another-user"></a>Использование рабочей области совместно с другим пользователем

Чтобы использовать рабочую область совместно с другим пользователем в вашей подписке, используйте следующую команду:

```azurecli-interactive
az ml workspace share -w <workspace-name> -g <resource-group-name> --user <user> --role <role>
```

Дополнительные сведения об управлении доступом на основе ролей (RBAC) в Машинном обучении Azure см. в разделе [Управление пользователями и ролями](how-to-assign-roles.md).

Дополнительные сведения см. в документации по команде [az ml workspace share](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-share).

## <a name="sync-keys-for-dependent-resources"></a>Ключи синхронизации для зависимых ресурсов

Если изменить ключи доступа для одного из ресурсов, используемых рабочей областью, для синхронизации рабочей области с новым ключом потребуется около часа. Чтобы принудительно синхронизировать новые ключи в рабочей области, используйте следующую команду:

```azurecli-interactive
az ml workspace sync-keys -w <workspace-name> -g <resource-group-name>
```

Дополнительные сведения об изменении ключей см. в разделе [Повторное создание ключей доступа к хранилищу](how-to-change-storage-access-key.md).

Дополнительные сведения см. в документации по команде [az ml workspace sync-keys](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-sync-keys).

## <a name="delete-a-workspace"></a>Удаление рабочей области

Чтобы удалить рабочую область, когда она больше не нужна, используйте следующую команду:

```azurecli-interactive
az ml workspace delete -w <workspace-name> -g <resource-group-name>
```

> [!IMPORTANT]
> При удалении рабочей области не удаляется Application Insights, учетная запись хранения, хранилище ключей или реестр контейнеров, используемые рабочей областью.

Вы можете удалить группу ресурсов. При этом будет удалена рабочая область и все остальные ресурсы Azure из этой группы ресурсов. Чтобы удалить группу ресурсов, используйте следующую команду:

```azurecli-interactive
az group delete -g <resource-group-name>
```

Дополнительные сведения см. в документации по команде [az ml workspace delete](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-delete).

## <a name="troubleshooting"></a>Устранение неполадок

### <a name="resource-provider-errors"></a>Ошибки поставщика ресурсов

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>Перемещение рабочей области

> [!WARNING]
> Перемещение рабочей области Машинного обучения Azure в другую подписку или перемещение главной подписки на новый клиент не поддерживается. Это может привести к ошибкам.

### <a name="deleting-the-azure-container-registry"></a>Удаление Реестра контейнеров Azure

Для некоторых операций в рабочей области Машинного обучения Azure используется реестр контейнеров Azure (ACR) Он автоматически создает экземпляр ACR при необходимости.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о расширении Azure CLI для машинного обучения см. в документации по [az ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml?view=azure-cli-latest).
