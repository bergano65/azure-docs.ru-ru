---
title: Краткое руководство. Создание рабочей области Synapse с помощью Azure CLI
description: Создайте рабочую область Azure Synapse с помощью Azure CLI, выполнив действия, описанные в этом руководстве.
services: synapse-analytics
author: alehall
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 08/25/2020
ms.author: alehall
ms.reviewer: jrasnick
ms.openlocfilehash: 065e24c12e0750cd7a1f3ce2d4cad5c7ad4b95df
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91260722"
---
# <a name="quickstart-create-an-azure-synapse-workspace-with-azure-cli"></a>Краткое руководство. Создание рабочей области Azure Synapse с помощью Azure CLI

Azure CLI — это интерфейс командной строки Azure для управления ресурсами Azure. Вы можете использовать его в браузере с Azure Cloud Shell. Его также можно установить в macOS, Linux или Windows и запускать из командной строки.

В этом кратком руководстве вы узнаете, как создать рабочую область Synapse с помощью Azure CLI.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись, прежде чем начинать работу](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Предварительные требования

- Скачайте и установите [jg](https://stedolan.github.io/jq/download/), простой и удобный процессор командной строки JSON
- [Учетная запись хранения Azure Data Lake Storage 2-го поколения](../storage/common/storage-account-create.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

    > [!IMPORTANT]
    > В рабочей области Azure Synapse должна быть возможность считывать данные в выбранной учетной записи ADLS 2-го поколения и выполнять запись в нее. Кроме того, для любой учетной записи хранения, связываемой в качестве основной учетной записи хранения, при создании учетной записи хранения необходимо включить **иерархическое пространство имен**, как описано на странице [Создание учетной записи хранения](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-portal#create-a-storage-account). 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="install-the-azure-cli-locally"></a>Установка Azure CLI в локальной среде

Если вы решили установить и использовать Azure CLI локально, см. статью [Установка Azure CLI](/cli/azure/install-azure-cli).

Если вы используете Azure CLI на локальном компьютере, необходимо войти в систему и пройти проверку подлинности. В этом шаге нет необходимости, если вы используете Azure Cloud Shell. Чтобы войти в Azure CLI, запустите `az login` и пройдите проверку подлинности в окне браузера:

```azurecli
az login
```

Дополнительные сведения о проверке подлинности с помощью Azure CLI см. в статье [Вход с помощью Azure CLI](/cli/azure/authenticate-azure-cli).

## <a name="install-azure-synapse-extension-for-azure-cli"></a>Установка расширения Azure Synapse для Azure CLI

```azurecli
az extension add --name synapse
```

> [!WARNING]
> Расширение Azure Synapse для Azure CLI находится в режиме предварительной версии.

## <a name="create-an-azure-synapse-workspace-using-the-azure-cli"></a>Создание рабочей области Azure Synapse с помощью Azure CLI

1. Задайте необходимые переменные среды, чтобы создать ресурсы для рабочей области Azure Synapse.

    | Имя переменной среды | Описание |
    |---|---|---|
    |Имя_учетной_записи_хранения| Имя существующей учетной записи хранения ADLS 2-го поколения.|
    |StorageAccountResourceGroup| Имя существующей группы ресурсов учетной записи хранения ADLS 2-го поколения. |
    |FileShareName| Имя существующей файловой системы хранилища.|
    |SynapseResourceGroup| Выберите новое имя для группы ресурсов Azure Synapse. |
    |Регион| Выберите один из [регионов Azure](https://azure.microsoft.com/global-infrastructure/geographies/#overview). |
    |SynapseWorkspaceName| Выберите уникальное имя для новой рабочей области Azure Synapse. |
    |SqlUser| Выберите значение для нового имени пользователя.|
    |SqlPassword| Выберите надежный пароль.|
    |||

2. Создайте группу ресурсов в форме контейнера для рабочей области Azure Synapse:
    ```azurecli
    az group create --name $SynapseResourceGroup --location $Region
    ```
3. Выведите на экран ключ учетной записи хранения Azure Data Lake Storage 2-го поколения:
    ```azurecli
    StorageAccountKey=$(az storage account keys list \
      --account-name $StorageAccountName \
      | jq -r '.[0] | .value')
    ```
4. Выведите на экран URL-адрес конечной точки хранилища ADLS 2-го поколения:
    ```azurecli
    StorageEndpointUrl=$(az storage account show \
      --name $StorageAccountName \
      --resource-group $StorageAccountResourceGroup \
      | jq -r '.primaryEndpoints | .dfs')
    ```

5. (Дополнительно) Вы можете проверить значения для ключа и конечной точки учетной записи хранения Azure Data Lake Storage 2-го поколения, выполнив такие команды:
    ```azurecli
    echo "Storage Account Key: $StorageAccountKey"
    echo "Storage Endpoint URL: $StorageEndpointUrl"
    ```

6. Создайте рабочую область Azure Synapse:
    ```azurecli
    az synapse workspace create \
      --name $SynapseWorkspaceName \
      --resource-group $SynapseResourceGroup \
      --storage-account $StorageAccountName \
      --file-system $FileShareName \
      --sql-admin-login-user $SqlUser \
      --sql-admin-login-password $SqlPassword \
      --location $Region
    ```

7. Получите URL-адрес Web и Dev для рабочей области Azure Synapse:
    ```azurecli
    WorkspaceWeb=$(az synapse workspace show --name $SynapseWorkspaceName --resource-group $SynapseResourceGroup | jq -r '.connectivityEndpoints | .web')

    WorkspaceDev=$(az synapse workspace show --name $SynapseWorkspaceName --resource-group $SynapseResourceGroup | jq -r '.connectivityEndpoints | .dev')
    ```

8. Создайте правило брандмауэра, которое разрешит доступ к рабочей области Azure Synapse с компьютера:

    ```azurecli
    ClientIP=$(curl -sb -H "Accept: application/json" "$WorkspaceDev" | jq -r '.message')
    ClientIP=${ClientIP##'Client Ip address : '}
    echo "Creating a firewall rule to enable access for IP address: $ClientIP"

    az synapse workspace firewall-rule create --end-ip-address $ClientIP --start-ip-address $ClientIP --name "Allow Client IP" --resource-group $SynapseResourceGroup --workspace-name $SynapseWorkspaceName
    ```

9. Чтобы получить доступ к рабочей области, откройте URL-адрес Web рабочей области Azure Synapse, который хранится в переменной среды`WorkspaceWeb`:

    ```azurecli
    echo "Open your Azure Synapse Workspace Web URL in the browser: $WorkspaceWeb"
    ```
    
    [ ![web для рабочей области Azure Synapse](media/quickstart-create-synapse-workspace-cli/create-workspace-cli-1.png) ](media/quickstart-create-synapse-workspace-cli/create-workspace-cli-1.png#lightbox)


## <a name="clean-up-resources"></a>Очистка ресурсов

Выполните действия ниже, чтобы удалить рабочую область Azure Synapse.
> [!WARNING]
> Удаление рабочей области Azure Synapse приведет к удалению модулей аналитики и данных, хранящихся в базе данных автономных пулов SQL и метаданных рабочей области. В этом случае вы больше не сможете подключиться к конечным точкам SQL и Apache Spark. Все артефакты кода будут удалены (запросы, записные книжки, определения заданий и конвейеры).
>
> Удаление рабочей области **не** влияет на данные в Azure Data Lake Store 2-го поколения, связанные с рабочей областью.

Если вы хотите удалить рабочую область Azure Synapse, выполните следующую команду:

```azurecli
az synapse workspace delete --name $SynapseWorkspaceName --resource-group $SynapseResourceGroup
```

## <a name="next-steps"></a>Дальнейшие действия

Теперь можно создать пулы [SQL](quickstart-create-sql-pool-studio.md) или [Apache Spark](quickstart-create-apache-spark-pool-studio.md), чтобы начать анализ и изучение данных.
