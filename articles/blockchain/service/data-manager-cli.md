---
title: Настройте менеджер блокчейн-данных с помощью Azure CLI - Сервис Azure Blockchain
description: Создание и управление менеджером блокчейн-данных для службы блокчейн Azure с помощью Azure CLI
ms.date: 11/04/2019
ms.topic: article
ms.reviewer: chroyal
ms.openlocfilehash: a8061aad6d6a1513de70e7c2bc57aa109c666611
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74455937"
---
# <a name="configure-blockchain-data-manager-using-azure-cli"></a>Настройка диспетчера данных блокчейна с помощью Azure CLI

Настройте менеджер блокчейн-данных для Azure Blockchain Service для захвата данных блокчейна, отправьте его в тему Сетки событий Azure.

Чтобы настроить экземпляр Blockchain Data Manager:

* Создание экземпляра менеджера блокчейна
* Создание ввода в узел транзакции Azure Blockchain Service
* Создание вывода для темы сетки событий Azure
* Добавление приложения блокчейна
* Запуск экземпляра

## <a name="prerequisites"></a>Предварительные требования

* Установите новейший [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) и восподписаны в использовании `az login`.
* Полный [быстрый запуск: Используйте визуальный код студии для подключения к сети консорциума Azure Blockchain Service](connect-vscode.md)
* Создание [темы сетки событий](../../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic)
* Подробные сведения см. в статье об [обработчиках событий в службе "Сетка событий Azure"](../../event-grid/event-handlers.md).

## <a name="launch-azure-cloud-shell"></a>Запуск Azure Cloud Shell

Azure Cloud Shell — это бесплатная интерактивная оболочка, с помощью которой можно выполнять действия, описанные в этой статье. Она включает предварительно установленные общие инструменты Azure и настроена для использования с вашей учетной записью.

Чтобы открыть Cloud Shell, просто выберите **Попробовать** в правом верхнем углу блока кода. Вы также можете запустить Cloud Shell в [https://shell.azure.com/bash](https://shell.azure.com/bash)отдельной вкладке браузера, перейдя к . Выберите **Copy,** чтобы скопировать блоки кода, вставьте его в облачную оболочку и нажмите введите, чтобы запустить его.

Если вы решили установить и использовать CLI локально, для выполнения инструкций, приведенных в этом руководстве вам потребуется Azure CLI 2.0.51 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам нужно установить [install Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)или обновить, см.

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте группу ресурсов с помощью команды [az group create](https://docs.microsoft.com/cli/azure/group). Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими. Следующий пример создает группу ресурсов под названием *myResourceGroup* в *восточном* месте:

```azurecli-interactive
az group create --name myRG --location eastus
```

## <a name="create-instance"></a>Создание экземпляра

Экземпляр Blockchain Data Manager отслеживает узел транзакций Службы блокчейн Azure. Экземпляр регистрирует все необработанные данные о блоках и транзакциях из узла транзакций.

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
| resource-group | Название группы ресурсов, где можно создать экземпляр Blockchain Data Manager. |
| name | Название экземпляра менеджера данных Blockchain. |
| ресурсного типа | Тип ресурса для экземпляра Blockchain Data Manager — **Это Microsoft.blockchain/watchers.** |
| full-object | Отокажие свойства содержат параметры ресурса наблюдателя. |
| properties | Строка, сформируемые JSON, содержащая свойства ресурса наблюдателя. Может передаваться как строка или файл.  |

### <a name="create-instance-examples"></a>Создание примеров примеров

Пример конфигурации JSON для создания экземпляра Blockchain Manager в восточном регионе **США.**

``` json
{
    "location": "eastus",
    "properties": {
    }
}
```

| Элемент | Описание |
|---------|-------------|
| location | Регион, где создать ресурс наблюдателя |
| properties | Свойства, установленные при создании ресурса наблюдателя |

Создайте экземпляр Blockchain Data Manager под названием *mywatcher,* используя строку JSON для настройки.

``` azurecli-interactive
az resource create \
                    --resource-group myRG \
                    --name mywatcher \
                    --resource-type Microsoft.blockchain/watchers \
                     --is-full-object \
                     --properties '{"location":"eastus"}'
```

Создайте экземпляр Blockchain Data Manager под названием *mywatcher* с помощью файла конфигурации JSON.

``` azurecli
az resource create \
                    --resource-group myRG \
                    --name mywatcher \
                    --resource-type Microsoft.blockchain/watchers \
                    --is-full-object \
                    --properties @watcher.json
```

## <a name="create-input"></a>Создание входных данных

Входный данный соединяет менеджер блокчейн-данных с узлом транзакций Службы блокчейн Azure. Только пользователи, имеющих доступ к узлам транзакции, могут создать соединение.

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
| resource-group | Имя группы ресурсов, где можно создать ресурс ввода. |
| name | Название ввода. |
| namespace | Используйте пространство имен поставщика **Microsoft.Blockchain.** |
| ресурсного типа | Тип ресурса для ввода Blockchain Data Manager — это **входные данные.** |
| родитель | Путь к наблюдателю, к которому связан вход. Например, **наблюдатели/ миходер**. |
| full-object | Отокажие свойства содержат параметры ресурса ввода. |
| properties | Строка, содержащая свойства для ресурса ввода, сформатированным JSON. Может передаваться как строка или файл. |

### <a name="input-examples"></a>Примеры ввода

Конфигурация примера JSON для создания входного ресурса \<в\>восточном регионе *США,* подключенного к участнику Blockchain.

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
| location | Регион, где можно создать ресурс ввода. |
| вхотливыйТип | Тип леджера участника блокчейн-сервиса Azure. В настоящее время **Ethereum** поддерживается. |
| resourceId | Узла транзакций, к которому подключен вход. Замените \<\>идентификатор подписки, \<группу\>ресурсов и \<участника\> Blockchain значениями для ресурса узла транзакции. Ввод подключается к узелу транзакции по умолчанию для члена службы блокчейн Azure. |

Создайте входный вход под названием *myInput* для *mywatcher,* используя строку JSON для конфигурации.

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

Создайте входный вход под названием *myInput* для *mywatcher* с помощью файла конфигурации JSON.

``` azurecli
az resource create \
                   --resource-group myRG \
                   --name input \
                   --namespace Microsoft.Blockchain \ --resource-type inputs \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties @input.json
```

## <a name="create-output"></a>Создание вывода

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
| resource-group | Имя группы ресурсов, где можно создать ресурс вывода. |
| name | Название вывода. |
| namespace | Используйте пространство имен поставщика **Microsoft.Blockchain.** |
| ресурсного типа | Тип ресурса для вывода менеджера данных Blockchain является **выходом.** |
| родитель | Путь к наблюдателю, к которому связан выход. Например, **наблюдатели/ миходер**. |
| full-object | Отокажие свойства содержат параметры для вывода ресурса. |
| properties | Строка формата JSON, содержащая свойства для вывода ресурса. Может передаваться как строка или файл. |

### <a name="output-examples"></a>Примеры вывода

Конфигурация примера JSON для создания ресурса вывода в восточном регионе \< *США,* подключенного к теме сетки событий, названной темой\>сетки событий.

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
| location | Регион, где можно создать ресурс вывода. |
| выходТип | Тип вывода. В настоящее время **EventGrid** поддерживается. |
| resourceId | Ресурс, к которому подключен выход. Замените \<\>идентификатор подписки, \<группу\>ресурсов и \<участника\> Blockchain значениями для ресурса сетки событий. |

Создайте выход, названный *myoutput* er для *mywatcher,* который подключается к теме сетки событий с помощью строки конфигурации JSON.

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

Создайте выход, названный *myoutput* *er,* который подключается к теме сетки событий с помощью файла конфигурации JSON.

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

## <a name="add-blockchain-application"></a>Добавление блокчейн-приложения

Если вы добавляете блокчейн-приложение, менеджер blockchain Data Manager декодирует событие и состояние свойства для приложения. В противном случае отправляются только необработанные данные о блокировке и необработанных транзакциях. Менеджер блокчейн данных также обнаруживает адреса контрактов при развертывании контракта. Вы можете добавить несколько блокчейн-приложений в экземпляр Blockchain Data Manager.


> [!IMPORTANT]
> В настоящее время приложения блокчейн, которые декларируют [типы массивов](https://solidity.readthedocs.io/en/v0.5.12/types.html#arrays) Solidity или [типы отображения,](https://solidity.readthedocs.io/en/v0.5.12/types.html#mapping-types) не поддерживаются полностью. Свойства, объявленные в виде типов массива или отображения, не будут декодированы в сообщениях *ContractPropertiesMsg* или *DecodedContractEventsMsg.*

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
| resource-group | Имя группы ресурсов, где создать ресурс приложения. |
| name | Имя приложения. |
| namespace | Используйте пространство имен поставщика **Microsoft.Blockchain.** |
| ресурсного типа | Тип ресурса для приложения Blockchain Data Manager — это **артефакты.** |
| родитель | Путь к наблюдателю, к которому связано приложение. Например, **наблюдатели/ миходер**. |
| full-object | Указывает свойства содержат параметры ресурса приложения. |
| properties | Строка, содержащая свойства для ресурса приложения, сформатированным JSON. Может передаваться как строка или файл. |

### <a name="blockchain-application-examples"></a>Примеры применения блокчейна

Конфигурация JSON пример для создания ресурса приложений в восточном регионе *США,* который отслеживает смарт-контракт, определенный контрактом ABI и bytecode.

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
| location | Регион, где создать ресурс приложения. |
| артефактТип | Тип приложения. В настоящее время **EthereumSmartContract** поддерживается. |
| abiFileUrl | URL для смарт-контракта ABI JSON файл. Для получения дополнительной информации о получении контракта ABI и создании URL-адреса, [см. Get Contract ABI и байтекод](data-manager-portal.md#get-contract-abi-and-bytecode) и [создание контракта ABI и bytecode URL](data-manager-portal.md#create-contract-abi-and-bytecode-url). |
| bytecodeFileUrl | URL для смарт-контракта, развернутого файлом bytecode JSON. Для получения дополнительной информации о получении смарт-контракта развернуты байтекод и создание URL-адреса, [см. Get Contract ABI и байтекод](data-manager-portal.md#get-contract-abi-and-bytecode) и [создать контракт ABI и bytecode URL](data-manager-portal.md#create-contract-abi-and-bytecode-url). Примечание: Менеджер блокчейн-данных требует **развернутого байткода.** |
| запросTargetTypes | Опубликованные типы сообщений. Указание **ContractProperties** публикует тип сообщения *ContractPropertiesMsg.* Указание **ContractEvents** публикует тип сообщения *DecodedContractEventsMsg.* Примечание: *Типы сообщений RawBlockAndTransactionMsg* и *RawTransactionContractCreationMsg* всегда публикуются. |

Создайте приложение под названием *myApplication* для *mywatcher,* которое отслеживает смарт-контракт, определяемый строкой JSON.

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

Создайте приложение под названием *myApplication* для *mywatcher,* которое наблюдает смарт-контракт, определенный с помощью файла конфигурации JSON.

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

## <a name="start-instance"></a>Запуск экземпляра

При запуске экземпляр Blockchain Manager отслеживает события блокчейна из определенных входов и отправляет данные в определенные выходы.

``` azurecli
az resource invoke-action \
                          --action start \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/<Watcher name>
```

| Параметр | Описание |
|-----------|-------------|
| action | Используйте **начало** для запуска наблюдателя. |
| ids | Идентификатор ресурса watcher. Замените \<\>идентификатор подписки, \<группу\>ресурсов и \<имя\> Watcher значениями для ресурса наблюдателя.|

### <a name="start-instance-example"></a>Пример примера запуска

Запустите экземпляр Blockchain Data Manager под названием *mywatcher.*

``` azurecli-interactive
az resource invoke-action \
                          --action start \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/mywatcher
```

## <a name="stop-instance"></a>Остановка экземпляра

Остановите экземпляр менеджера блокчейна.

``` azurecli
az resource invoke-action \
                          --action stop \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/<Watcher name>
```

| Параметр | Описание |
|-----------|-------------|
| action | Используйте **стоп,** чтобы остановить наблюдателя. |
| ids | Имя наблюдателя. Замените \<\>идентификатор подписки, \<группу\>ресурсов и \<имя\> Watcher значениями для ресурса наблюдателя. |

### <a name="stop-watcher-example"></a>Пример наблюдателя stop

Остановить экземпляр под названием *mywatcher*.

``` azurecli-interactive
az resource invoke-action \
                          --action stop \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/mywatcher
```

## <a name="delete-instance"></a>Удалить экземпляр

Удалить экземпляр blockchain Data Manager.

``` azurecli
az resource delete \
                   --resource-group <Resource group> \
                   --name <Watcher name> \
                   --resource-type Microsoft.Blockchain/watchers
```

| Параметр | Описание |
|-----------|-------------|
| resource-group | Название группы ресурсов наблюдателя для удаления. |
| name | Имя наблюдателя для удаления. |
| ресурсного типа | Тип ресурса для наблюдателя Blockchain Data Manager — **Microsoft.blockchain/watchers.** |

### <a name="delete-instance-example"></a>Удалить пример экземпляра

Удалите экземпляр под названием *mywatcher* в группе ресурсов *myRG.*

``` azurecli-interactive
az resource delete \
                   --resource-group myRG \
                   --name mywatcher \
                   --resource-type Microsoft.blockchain/watchers
```

## <a name="next-steps"></a>Дальнейшие действия

Попробуйте следующий учебник, создающий исследователь блокчейн-транзакций, используя Blockchain Data Manager и Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Использование диспетчера данных блокчейна для отправки данных в Azure Cosmos DB](data-manager-cosmosdb.md)
