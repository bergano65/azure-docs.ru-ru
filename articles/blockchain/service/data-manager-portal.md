---
title: Configure Blockchain Data Manager using Azure portal - Azure Blockchain Service
description: Create and manage Blockchain Data Manager for Azure Blockchain Service using the Azure portal.
ms.date: 11/04/2019
ms.topic: article
ms.reviewer: chroyal
ms.openlocfilehash: 9c682f449fbab823134d626870c7dcfe8a8f2847
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455810"
---
# <a name="configure-blockchain-data-manager-using-the-azure-portal"></a>Настройка диспетчера данных блокчейна с помощью портала Azure

Configure Blockchain Data Manager for Azure Blockchain Service to capture blockchain data and send it to an Azure Event Grid Topic.

To configure a Blockchain Data Manager instance, you:

* Create a Blockchain Data Manager instance for an Azure Blockchain Service transaction node
* Add your blockchain applications

## <a name="prerequisites"></a>Технические условия

* Complete [Quickstart: Create a blockchain member using the Azure portal](create-member.md) or [Quickstart: Create an Azure Blockchain Service blockchain member using Azure CLI](create-member-cli.md)
* Создайте [раздел Сетки событий](../../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic).
* Подробные сведения см. в статье об [обработчиках событий в службе "Сетка событий Azure"](../../event-grid/event-handlers.md).

## <a name="create-instance"></a>Создание экземпляра

Каждый экземпляр диспетчера данных блокчейна подключается к узлу транзакций службы "Блокчейн Azure" и отслеживает его. Only users with access to the transaction node can create a connection. Экземпляр регистрирует все необработанные данные о блоках и транзакциях из узла транзакций.

Исходящее подключение отправляет данные блокчейна в службу "Сетка событий Azure". При создании экземпляра вы настраиваете одно исходящее подключение. Диспетчер данных блокчейна для каждого отдельного экземпляра поддерживает несколько исходящих подключений к разделам Сетки событий. Данные блокчейна можно отправлять в одно или несколько назначений. To add another destination, just add additional outbound connections to the instance.

1. Войдите на [портале Azure](https://portal.azure.com).
1. Go to the Azure Blockchain Service member you want to connect to Blockchain Data Manager. Щелкните **Blockchain Data Manager** (Диспетчер данных блокчейна).
1. Выберите **Добавить**.

    ![Добавление диспетчера данных блокчейна](./media/data-manager-portal/add-instance.png)

    Введите следующие сведения:

    Параметр | Описание
    --------|------------
    Name | Введите уникальное имя для подключенного диспетчера данных блокчейна. The Blockchain Data Manager name can contain lower case letters and numbers and has a maximum length of 20 characters.
    Узел транзакции | Choose a transaction node. Only transaction nodes you have read access are listed.
    Имя подключения | Введите уникальное имя исходящего подключения, в которое отправляются данные транзакций блокчейна.
    Конечная точка Сетки событий | Choose an event grid topic in the same subscription as the Blockchain Data Manager instance.

1. Нажмите кнопку **ОК**.

    Создание экземпляра диспетчера данных блокчейна занимает менее минуты. Когда завершается развертывание экземпляра, он запускается автоматически. A running Blockchain Data Manager instance captures blockchain events from the transaction node and sends data to the outbound connections.

    The new instance appears in the list of Blockchain Data Manager instances for the Azure Blockchain Service member.

    ![List of Blockchain Data Member instances](./media/data-manager-portal/instance-list.png)

## <a name="add-blockchain-application"></a>Add blockchain application

If you add a blockchain application, Blockchain Data Manager decodes event and property state for the application. Otherwise, only raw block and raw transaction data is sent. Blockchain Data Manager also discovers contract addresses when the contract is deployed. You can add multiple blockchain applications to a Blockchain Data Manager instance.

> [!IMPORTANT]
> Currently, blockchain applications that declare Solidity [array types](https://solidity.readthedocs.io/en/v0.5.12/types.html#arrays) or [mapping types](https://solidity.readthedocs.io/en/v0.5.12/types.html#mapping-types) are not fully supported. Properties declared as array or mapping types will not be decoded in *ContractPropertiesMsg* or *DecodedContractEventsMsg* messages.

Blockchain Data Manager requires a smart contract ABI and deployed bytecode file to add the application.

### <a name="get-contract-abi-and-bytecode"></a>Get Contract ABI and bytecode

ABI контракта определяет интерфейсы смарт-контракта. В нем описывается взаимодействие со смарт-контрактом. ABI контракта можно скопировать в буфер обмена через [расширение Пакета средств разработки Блокчейна Azure для Ethereum](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain).

1. На панели обозревателя Visual Studio Code разверните папку **build/contracts** проекта Solidity.
1. Правой кнопкой мыши щелкните JSON-файл метаданных контракта. Имя файла — это имя смарт-контракта, за которым следует расширение **.json**.
1. Щелкните **Скопировать ABI контракта**.

    ![Панель Visual Studio Code, где выбрано действие "Скопировать ABI контракта"](./media/data-manager-portal/abi-devkit.png)

    ABI контракта скопируется в буфер обмена.

1. Сохраните массив **abi** в JSON-файл. Например, с именем *abi.json*. Он понадобится вам на одном из следующих шагов.

Диспетчеру данных блокчейна нужен развернутый байт-код для смарт-контракта. Развернутый байт-код отличается от байт-кода смарт-контракта. Развернутый байт-код можно получить из скомпилированного файла метаданных контракта.

1. Откройте файл метаданных контракта, содержащийся в папке **build/contracts** проекта Solidity. Имя файла — это имя смарт-контракта, за которым следует расширение **.json**.
1. Найдите элемент **deployedBytecode** в JSON-файле.
1. Скопируйте шестнадцатеричное значение без кавычек.

    ![Панель Visual Studio Code, где представлен байт-код в составе метаданных](./media/data-manager-portal/bytecode-metadata.png)

1. Сохраните **байт-код** в виде JSON-файла. Например, с именем *bytecode.json*. Он понадобится вам на одном из следующих шагов.

В следующем примере показаны файлы *abi.json* и *bytecode.json*, открытые в редакторе VS Code. Ваши файлы должны выглядеть примерно так же.

![Пример содержимого файлов abi.json и bytecode.json](./media/data-manager-portal/contract-files.png)

### <a name="create-contract-abi-and-bytecode-url"></a>Создание ABI и URL-адреса байт-кода контракта

Диспетчеру данных блокчейна при добавлении приложения нужно, чтобы файлы ABI и байт-кода для контракта были доступны по URL-адресу. Для предоставления частного URL-адреса можно использовать учетную запись хранения Azure.

#### <a name="create-storage-account"></a>Создание учетной записи хранения

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

#### <a name="upload-contract-files"></a>Отправка файлов контракта

1. Создайте новый контейнер для учетной записи хранения. Выберите **Контейнеры > Контейнер**.

    ![Создание контейнера учетной записи хранения](./media/data-manager-portal/create-container.png)

    | Поле | Описание |
    |-------|-------------|
    | Name  | Присвойте контейнеру имя, например *smartcontract*. |
    | Public access level (Уровень общего доступа) | Выберите *Private (no anonymous access)* (Частный (без анонимного доступа)). |

1. Нажмите кнопку **ОК**, чтобы создать контейнер.
1. Выберите контейнер и щелкните **Отправить**.
1. Выберите оба файла JSON, которые вы создали в разделе, посвященном [получению ABI и байт-кода контракта](#get-contract-abi-and-bytecode).

    ![Отправка большого двоичного объекта](./media/data-manager-portal/upload-blobs.png)

    Щелкните **Отправить**.

#### <a name="generate-url"></a>Создание URL-адреса

Создайте подписанный URL-адрес для каждого большого двоичного объекта.

1. Выберите большой двоичный объект с кодом JSON для ABI.
1. Щелкните **Создать SAS**.
1. Задайте требуемый срок действия подписи доступа, и щелкните **Создать маркер SAS BLOB-объекта и URL-адрес**.

    ![Создание маркера SAS](./media/data-manager-portal/generate-sas.png)

1. Создайте **URL-адрес SAS BLOB-объекта** и сохраните его для использования в следующем разделе.
1. Повторите шаги [по созданию URL-адреса](#generate-url) для большого двоичного объекта с кодом JSON для байт-кода.

### <a name="add-application-to-instance"></a>Add application to instance

1. Выберите экземпляр диспетчера данных блокчейна из списка экземпляров.
1. Выберите **Blockchain applications** (Приложения блокчейна).
1. Выберите **Добавить**.

    ![Добавление приложения блокчейна](./media/data-manager-portal/add-application.png)

    Введите имя приложения блокчейна и URL-адреса ABI и байт-кода для смарт-контракта.

    Параметр | Описание
    --------|------------
    Name | Введите уникальное имя приложения блокчейна, которое нужно отслеживать.
    Contract ABI (ABI контракта) | URL-адрес файла ABI контракта. Подробные сведения см. в разделе о [создании ABI и URL-адреса байт-кода контракта](#create-contract-abi-and-bytecode-url).
    Contract Bytecode (Байт-код контракта) | URL-путь к файлу байт-кода. Подробные сведения см. в разделе о [создании ABI и URL-адреса байт-кода контракта](#create-contract-abi-and-bytecode-url).

1. Нажмите кнопку **ОК**.

    Когда приложение будет создано, оно появится в списке приложений блокчейна.

    ![Список приложений блокчейна](./media/data-manager-portal/artifact-list.png)

Вы можете удалить учетную запись хранения Azure или использовать ее для настройки других приложений блокчейна. Если вы хотите удалить учетную запись хранения Azure, можно сразу удалить группу ресурсов. При этом удаляется связанная учетная запись хранения и другие ресурсы, связанные с этой группой ресурсов.

## <a name="stop-instance"></a>Stop instance

Stop the Blockchain Manager instance when you want to stop capturing blockchain events and sending data to the outbound connections. When the instance is stopped, no charges are incurred for Blockchain Data Manager. Дополнительные сведения см. на странице [цен](https://azure.microsoft.com/pricing/details/blockchain-service).

1. Go to **Overview** and select **Stop**.

    ![Stop instance](./media/data-manager-portal/stop-instance.png)

## <a name="next-steps"></a>Дальнейшие действия

Try the next tutorial creating a blockchain transaction message explorer using Blockchain Data Manager and Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Use Blockchain Data Manager to send data to Azure Cosmos DB](data-manager-cosmosdb.md)