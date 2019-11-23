---
title: Configure Blockchain Data Manager using Azure CLI - Azure Blockchain Service
description: Create and manage a Blockchain Data Manager for Azure Blockchain Service using Azure CLI
ms.date: 11/04/2019
ms.topic: article
ms.reviewer: chroyal
ms.openlocfilehash: 88abea691219a78ee16702e231337de055dbf5e4
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326235"
---
# <a name="configure-blockchain-data-manager-using-azure-cli"></a>Настройка диспетчера данных блокчейна с помощью Azure CLI

Configure Blockchain Data Manager for Azure Blockchain Service to capture blockchain data send it to an Azure Event Grid Topic.

To configure a Blockchain Data Manager instance, you:

* Create a Blockchain Manager instance
* Create an input to an Azure Blockchain Service transaction node
* Create an output to an Azure Event Grid Topic
* Добавление приложения блокчейна
* Start an instance

## <a name="prerequisites"></a>Технические условия

* Install the latest [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) and signed in using `az login`.
* Complete [Quickstart: Use Visual Studio Code to connect to a Azure Blockchain Service consortium network](connect-vscode.md)
* Создайте [раздел Сетки событий](../../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic).
* Подробные сведения см. в статье об [обработчиках событий в службе "Сетка событий Azure"](../../event-grid/event-handlers.md).

## <a name="launch-azure-cloud-shell"></a>Запуск Azure Cloud Shell

Azure Cloud Shell — это бесплатная интерактивная оболочка, с помощью которой можно выполнять действия, описанные в этой статье. Она включает предварительно установленные общие инструменты Azure и настроена для использования с вашей учетной записью.

Чтобы открыть Cloud Shell, просто выберите **Попробовать** в правом верхнем углу блока кода. Cloud Shell можно также запустить в отдельной вкладке браузера, перейдя на страницу [https://shell.azure.com/bash](https://shell.azure.com/bash). Нажмите кнопку **Копировать**, чтобы скопировать блок кода. Вставьте код в Cloud Shell и нажмите клавишу ВВОД, чтобы выполнить его.

Если вы решили установить и использовать CLI локально, для выполнения инструкций, приведенных в этом руководстве вам потребуется Azure CLI 2.0.51 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте группу ресурсов с помощью команды [az group create](https://docs.microsoft.com/cli/azure/group). Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими. В следующем примере создается группа ресурсов с именем *myResourceGroup* в расположении *eastus*.

```azurecli-interactive
az group create --name myRG --location eastus
```

## <a name="create-instance"></a>Создание экземпляра

A Blockchain Data Manager instance monitors an Azure Blockchain Service transaction node. Экземпляр регистрирует все необработанные данные о блоках и транзакциях из узла транзакций.

``` azurecli
az resource create \
                   --resource-group <Resource group> \
                   --name <Blockchain Data Manager instance name> \
                   --resource-type Microsoft.blockchain/watchers \
                   --is-full-object \
                   --properties <watcher resource properties>
```

| Параметр | Описание |
|-----------|-------------|
| resource-group | Resource group name where to create the Blockchain Data Manager instance. |
| name | Name of the Blockchain Data Manager instance. |
| resource-type | The resource type for a Blockchain Data Manager instance is **Microsoft.blockchain/watchers**. |
| full-object | Indicates properties contain options for the watcher resource. |
| properties | JSON-formatted string containing properties for the watcher resource. Can be passed as a string or a file.  |

### <a name="create-instance-examples"></a>Create instance examples

JSON configuration example to create a Blockchain Manager instance in the **East US** region.

``` json
{
    "location": "eastus",
    "properties": {
    }
}
```

| Элемент | Описание |
|---------|-------------|
| location | Region where to create the watcher resource |
| properties | Properties to set when creating the watcher resource |

Create a Blockchain Data Manager instance named *mywatcher* using a JSON string for configuration.

``` azurecli-interactive
az resource create \
                    --resource-group myRG \
                    --name mywatcher \
                    --resource-type Microsoft.blockchain/watchers \
                     --is-full-object \
                     --properties '{"location":"eastus"}'
```

Create a Blockchain Data Manager instance named *mywatcher* using a JSON configuration file.

``` azurecli
az resource create \
                    --resource-group myRG \
                    --name mywatcher \
                    --resource-type Microsoft.blockchain/watchers \
                    --is-full-object \
                    --properties @watcher.json
```

## <a name="create-input"></a>Создание входных данных

An input connects Blockchain Data Manager to an Azure Blockchain Service transaction node. Only users with access to the transaction node can create a connection.

``` azurecli
az resource create \
                   --resource-group <Resource group> \
                   --name <Input name> \
                   --namespace Microsoft.Blockchain \
                   --resource-type inputs \
                   --parent watchers/<Watcher name> \
                   --is-full-object \
                   --properties <input resource properties>
```

| Параметр | Описание |
|-----------|-------------|
| resource-group | Resource group name where to create the input resource. |
| name | Name of the input. |
| пространство_имен | Use the **Microsoft.Blockchain** provider namespace. |
| resource-type | The resource type for a Blockchain Data Manager input is **inputs**. |
| parent | The path to the watcher to which the input is associated. For example, **watchers/mywatcher**. |
| full-object | Indicates properties contain options for the input resource. |
| properties | JSON-formatted string containing properties for the input resource. Can be passed as a string or a file. |

### <a name="input-examples"></a>Input examples

Configuration JSON example to create an input resource in the *East US* region that is connected to \<Blockchain member\>.

``` json
{
    "location": "eastus",
    "properties": {
        "inputType": "Ethereum",
        "dataSource": {
            "resourceId": "/subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/blockchainMembers/<Blockchain member>/transactionNodes/transaction-node"
        }
    }
}
```

| Элемент | Описание |
|---------|-------------|
| location | Region where to create the input resource. |
| inputType | Ledger type of the Azure Blockchain Service member. Currently, **Ethereum** is supported. |
| ResourceId | Transaction node to which the input is connected. Replace \<Subscription ID\>, \<Resource group\>, and \<Blockchain member\> with the values for the transaction node resource. The input connects to the default transaction node for the Azure Blockchain Service member. |

Create an input named *myInput* for *mywatcher* using a JSON string for configuration.

``` azurecli-interactive
az resource create \
                   --resource-group myRG \
                   --name myInput \
                   --namespace Microsoft.Blockchain \
                   --resource-type inputs \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties '{"location":"eastus", "properties":{"inputType":"Ethereum","dataSource":{"resourceId":"/subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/BlockchainMembers/<Blockchain member>/transactionNodes/transaction-node"}}}'
```

Create an input named *myInput* for *mywatcher* using a JSON configuration file.

``` azurecli
az resource create \
                   --resource-group myRG \
                   --name input \
                   --namespace Microsoft.Blockchain \ --resource-type inputs \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties @input.json
```

## <a name="create-output"></a>Create output

Исходящее подключение отправляет данные блокчейна в службу "Сетка событий Azure". Данные блокчейна можно отправлять в одно или несколько назначений. Диспетчер данных блокчейна для каждого отдельного экземпляра поддерживает несколько исходящих подключений к разделам Сетки событий.

``` azurecli
az resource create \
                   --resource-group <Resource group> \
                   --name <Output name> \
                   --namespace Microsoft.Blockchain \
                   --resource-type outputs \
                   --parent watchers/<Watcher name> \
                   --is-full-object \
                   --properties <output resource properties>
```

| Параметр | Описание |
|-----------|-------------|
| resource-group | Resource group name where to create the output resource. |
| name | Name of the output. |
| пространство_имен | Use the **Microsoft.Blockchain** provider namespace. |
| resource-type | The resource type for a Blockchain Data Manager output is **outputs**. |
| parent | The path to the watcher to which the output is associated. For example, **watchers/mywatcher**. |
| full-object | Indicates properties contain options for the output resource. |
| properties | JSON-formatted string containing properties for the output resource. Can be passed as a string or a file. |

### <a name="output-examples"></a>Output examples

Configuration JSON example to create an output resource in the *East US* region that is connected to an event grid topic named \<event grid topic\>.

``` json
{
    "location": "eastus",
    "properties": {
        "outputType": "EventGrid",
        "dataSource": {
            "resourceId": "/subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.EventGrid/topics/<event grid topic>"
        }
    }
}
```

| Элемент | Описание |
|---------|-------------|
| location | Region where to create the output resource. |
| outputType | Type of output. Currently, **EventGrid** is supported. |
| ResourceId | Resource to which the output is connected. Replace \<Subscription ID\>, \<Resource group\>, and \<Blockchain member\> with the values for the event grid resource. |

Create an output named *myoutput* for *mywatcher* that connects to an event grid topic using a JSON configuration string.

``` azurecli-interactive
az resource create \
                   --resource-group myRG \
                   --name myoutput \
                   --namespace Microsoft.Blockchain \
                   --resource-type outputs \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties '{"location":"eastus","properties":{"outputType":"EventGrid","dataSource":{"resourceId":"/subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.EventGrid/topics/<event grid topic>"}}}'
```

Create an output named *myoutput* for *mywatcher* that connects to an event grid topic using a JSON configuration file.

``` azurecli
az resource create \
                   --resource-group myRG \
                   --name myoutput \
                   --namespace Microsoft.Blockchain \
                   --resource-type outputs \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties @output.json
```

## <a name="add-blockchain-application"></a>Add blockchain application

If you add a blockchain application, Blockchain Data Manager decodes event and property state for the application. Otherwise, only raw block and raw transaction data is sent. Blockchain Data Manager also discovers contract addresses when the contract is deployed. You can add multiple blockchain applications to a Blockchain Data Manager instance.


> [!IMPORTANT]
> Currently, blockchain applications that declare Solidity [array types](https://solidity.readthedocs.io/en/v0.5.12/types.html#arrays) or [mapping types](https://solidity.readthedocs.io/en/v0.5.12/types.html#mapping-types) are not fully supported. Properties declared as array or mapping types will not be decoded in *ContractPropertiesMsg* or *DecodedContractEventsMsg* messages.

``` azurecli
az resource create \
                   --resource-group <Resource group> \
                   --name <Application name> \
                   --namespace Microsoft.Blockchain \
                   --resource-type artifacts \
                   --parent watchers/<Watcher name> \
                   --is-full-object \
                   --properties <Application resource properties>
```

| Параметр | Описание |
|-----------|-------------|
| resource-group | Resource group name where to create the application resource. |
| name | Name of the application. |
| пространство_имен | Use the **Microsoft.Blockchain** provider namespace. |
| resource-type | The resource type for a Blockchain Data Manager application is **artifacts**. |
| parent | The path to the watcher to which the application is associated. For example, **watchers/mywatcher**. |
| full-object | Indicates properties contain options for the application resource. |
| properties | JSON-formatted string containing properties for the application resource. Can be passed as a string or a file. |

### <a name="blockchain-application-examples"></a>Blockchain application examples

Configuration JSON example to create an application resource in the *East US* region that monitors a smart contract defined by the contract ABI and bytecode.

``` json
{
    "location": "eastus",
    "properties": {
        "artifactType": "EthereumSmartContract",
        "content": {
            "abiFileUrl": "<ABI URL>",
            "bytecodeFileUrl": "<Bytecode URL>",
            "queryTargetTypes": [
                "ContractProperties",
                "ContractEvents"
            ]
        }
    }
}
```

| Элемент | Описание |
|---------|-------------|
| location | Region where to create the application resource. |
| artifactType | Тип приложения. Currently, **EthereumSmartContract** is supported. |
| abiFileUrl | URL for smart contract ABI JSON file. For more information on obtaining contract ABI and creating a URL, see [Get Contract ABI and bytecode](data-manager-portal.md#get-contract-abi-and-bytecode) and [Create contract ABI and bytecode URL](data-manager-portal.md#create-contract-abi-and-bytecode-url). |
| bytecodeFileUrl | URL for smart contract deployed bytecode JSON file. For more information on obtaining the smart contract deployed bytecode and creating a URL, see [Get Contract ABI and bytecode](data-manager-portal.md#get-contract-abi-and-bytecode) and [Create contract ABI and bytecode URL](data-manager-portal.md#create-contract-abi-and-bytecode-url). Note: Blockchain Data Manager requires the **deployed bytecode**. |
| queryTargetTypes | Published message types. Specifying **ContractProperties** publishes *ContractPropertiesMsg* message type. Specifying **ContractEvents** publishes *DecodedContractEventsMsg* message type. Note: *RawBlockAndTransactionMsg* and *RawTransactionContractCreationMsg* message types are always published. |

Create an application named *myApplication* for *mywatcher* that monitors a smart contract defined by a JSON string.

``` azurecli-interactive
az resource create \
                   --resource-group myRG \
                   --name myApplication \
                   --namespace Microsoft.Blockchain \
                   --resource-type artifacts \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties '{"location":"eastus","properties":{"artifactType":"EthereumSmartContract","content":{"abiFileUrl":"<ABI URL>","bytecodeFileUrl":"<Bytecode URL>","queryTargetTypes":["ContractProperties","ContractEvents"]}}}'
```

Create an application named *myApplication* for *mywatcher* that watches a smart contract defined using a JSON configuration file.

``` azurecli
az resource create \
                   --resource-group myRG \
                   --name myApplication \
                   --namespace Microsoft.Blockchain \
                   --resource-type artifacts \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties @artifact.json
```

## <a name="start-instance"></a>Start instance

When running, a Blockchain Manager instance monitors blockchain events from the defined inputs and sends data to the defined outputs.

``` azurecli
az resource invoke-action \
                          --action start \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/<Watcher name>
```

| Параметр | Описание |
|-----------|-------------|
| action | Use **start** to run the watcher. |
| ids | Watcher resource ID. Replace \<Subscription ID\>, \<Resource group\>, and \<Watcher name\> with the values for the watcher resource.|

### <a name="start-instance-example"></a>Start instance example

Start a Blockchain Data Manager instance named *mywatcher*.

``` azurecli-interactive
az resource invoke-action \
                          --action start \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/mywatcher
```

## <a name="stop-instance"></a>Stop instance

Stop a Blockchain Data Manager instance.

``` azurecli
az resource invoke-action \
                          --action stop \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/<Watcher name>
```

| Параметр | Описание |
|-----------|-------------|
| action | Use **stop** to stop the watcher. |
| ids | Name of the watcher. Replace \<Subscription ID\>, \<Resource group\>, and \<Watcher name\> with the values for the watcher resource. |

### <a name="stop-watcher-example"></a>Stop watcher example

Stop an instance named *mywatcher*.

``` azurecli-interactive
az resource invoke-action \
                          --action stop \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/mywatcher
```

## <a name="delete-instance"></a>Delete instance

Delete a Blockchain Data Manager instance.

``` azurecli
az resource delete \
                   --resource-group <Resource group> \
                   --name <Watcher name> \
                   --resource-type Microsoft.Blockchain/watchers
```

| Параметр | Описание |
|-----------|-------------|
| resource-group | Resource group name of the watcher to delete. |
| name | Name of the watcher to delete. |
| resource-type | The resource type for a Blockchain Data Manager watcher is **Microsoft.blockchain/watchers**. |

### <a name="delete-instance-example"></a>Delete instance example

Delete an instance named *mywatcher* in the *myRG* resource group.

``` azurecli-interactive
az resource delete \
                   --resource-group myRG \
                   --name mywatcher \
                   --resource-type Microsoft.blockchain/watchers
```

## <a name="next-steps"></a>Дальнейшие действия

Try creating a blockchain transaction message explorer using Blockchain Data Manager and Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Tutorial: Use Blockchain Data Manager to send data to Azure Cosmos DB](data-manager-cosmosdb.md)
