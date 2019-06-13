---
title: Отправка транзакций с помощью службы "Блокчейн Azure"
description: Узнайте, как использовать службу "Блокчейн Azure" для развертывания смарт-контрактов и отправки частных транзакций.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/29/2019
ms.topic: tutorial
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: 9037c7b5498a5e0a37b05e5ee09891bf8066393d
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/30/2019
ms.locfileid: "66417490"
---
# <a name="tutorial-send-transactions-using-azure-blockchain-service"></a>Руководство по Отправка транзакций с помощью службы "Блокчейн Azure"

В этом руководстве описано, как создать узлы транзакций для тестирования конфиденциальности контракта и транзакций.  Вы воспользуетесь решением Truffle для создания локальной среды разработки, развертывания смарт-контракта и отправки частной транзакции.

Вы узнаете, как:

> [!div class="checklist"]
> * добавить узлы транзакций;
> * использовать Truffle для развертывания смарт-контрактов;
> * отправлять транзакции;
> * проверять конфиденциальность транзакций.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Предварительные требования

* См. подробнее о [создании участника блокчейна на портале Azure](create-member.md).
* См. подробнее об [использовании Truffle для подключения к сети консорциума](connect-truffle.md).
* Установите [Truffle](https://github.com/trufflesuite/truffle). Для работы с Truffle нужно установить несколько инструментов, включая [Node.js](https://nodejs.org) и [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
* Установите [Python 2.7.15](https://www.python.org/downloads/release/python-2715/). Python требуется для Web3.
* Установите [Visual Studio Code](https://code.visualstudio.com/Download).
* Установите [расширение Solidity для Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=JuanBlanco.solidity).

## <a name="create-transaction-nodes"></a>Создание узлов транзакций

По умолчанию вам доступен один узел транзакций. Мы добавим еще два. Один из улов участвует в частной транзакции, а второй не будет включен в нее.

1. Войдите на [портале Azure](https://portal.azure.com).
1. Перейдите к участнику Блокчейна Azure и выберите **Узлы транзакций > Добавить**.
1. Настройте параметры для нового узла транзакций с именем `alpha`.

    ![Создание узла транзакций](./media/send-transaction/create-node.png)

    | Параметр | Значение | ОПИСАНИЕ |
    |---------|-------|-------------|
    | ИМЯ | `alpha` | Имя узла транзакций. Имя, используемое при создании адреса DNS для конечной точки узла транзакций. Например, `alpha-mymanagedledger.blockchain.azure.com`. |
    | Пароль | Надежный пароль | Пароль, используемый для доступа к конечной точке узла транзакций с обычной аутентификацией.

1. Нажмите кнопку **Создать**.

    Подготовка нового узла транзакций занимает около 10 минут.

1. Повторите шаги 2–4, чтобы добавить узел транзакций с именем `beta`.

Во время подготовки узлов вы можете продолжить работу с этим руководством. После завершения подготовки станет доступно три узла транзакций.

## <a name="open-truffle-console"></a>Открытие консоли Truffle

1. Откройте командную строку или оболочку Node.js.
1. Измените путь к каталогу проекта Truffle, как описано в [кратком руководстве по использованию Truffle для подключения к сети консорциума](connect-truffle.md). Например,

    ```bash
    cd truffledemo
    ```

1. Запустите интерактивную консоль разработки Truffle.

    ``` bash
    truffle develop
    ```

    Truffle создает блокчейн для локальной разработки и предоставляет интерактивную консоль.

## <a name="create-ethereum-account"></a>Создание учетной записи Ethereum

С помощью Web3 подключитесь к узлу транзакций по умолчанию и создайте учетную запись Ethereum. Вы можете получить строку подключения Web3 на портале Azure.

1. На портале Azure перейдите к узлу транзакций по умолчанию и выберите **Узлы транзакций > Пример кода > Web3**.
1. Скопируйте код JavaScript из раздела **HTTPS (ключ доступа 1)** ![Пример кода Web3](./media/send-transaction/web3-code.png)

1. Вставьте код JavaScript Web3 для узла транзакций по умолчанию в интерактивной консоли разработки Truffle. С помощью кода создается объект web3, подключенный к узлу транзакций службы "Блокчейн Azure".

    ```bash
    truffle(develop)> var Web3 = require("Web3");
    truffle(develop)> var provider = new Web3.providers.HttpProvider("https://myblockchainmember.blockchain.azure.com:3200/hy5FMu5TaPR0Zg8GxiPwned");
    truffle(develop)> var web3 = new Web3(provider);
    ```

    Вы можете вызывать методы в объекте Web3 для взаимодействия с узлом транзакций.

1. Создайте новую учетную запись на узле транзакций по умолчанию. Замените пароль надежным паролем.

    ```bash
    web3.eth.personal.newAccount("1@myStrongPassword");
    ```

    Сохраните полученный адрес учетной записи и пароль. Адрес и пароль учетной записи Ethereum понадобятся в следующем разделе.

1. Закройте среду разработки Truffle.

    ```bash
    .exit
    ```

## <a name="configure-truffle-project"></a>Настройка проекта Truffle

Для настройки проекта Truffle вам потребуется дополнительная информация об узле транзакций с портала Azure.

### <a name="transaction-node-public-key"></a>Открытый ключ узла транзакций

Каждый узел транзакций имеет открытый ключ. Открытый ключ позволяет отправлять частные транзакции на узел. Чтобы отправить транзакцию из узла транзакций по умолчанию на узел транзакций *alpha*, вам потребуется открытый ключ узла транзакций *alpha*.

Вы можете получить открытый ключ из списка узлов транзакций. Скопируйте открытый ключ для узла alpha и сохраните значение для дальнейшего использования в этом руководстве.

![Список узлов транзакций](./media/send-transaction/node-list.png)

### <a name="transaction-node-endpoint-addresses"></a>Адреса конечных точек узлов транзакций

1. На портале Azure перейдите к каждому узлу транзакций и выберите **Узлы транзакций > Строки подключений**.
1. Скопируйте и сохраните URL-адрес конечной точки из раздела **HTTPS (ключ доступа 1)** для каждого узла транзакций. Вам потребуются адреса конечных точек для файла конфигурации смарт-контракта для дальнейшего использования в этом руководстве.

    ![Адрес конечных точек транзакций](./media/send-transaction/endpoint.png)

### <a name="edit-configuration-file"></a>Изменение файла конфигурации

1. Запустите Visual Studio Code и откройте папку каталога проекта Truffle в меню **Файл > Открыть папку**.
1. Откройте файл конфигурации Truffle `truffle-config.js`.
1. Замените содержимое файла следующими данными о конфигурации. Добавьте переменные, содержащие адреса конечных точек и сведения об учетной записи. Замените фрагменты в угловых скобках значениями, которые были получены в предыдущих разделах.

    ``` javascript
    var defaultnode = "<default transaction node connection string>";
    var alpha = "<alpha transaction node connection string>";
    var beta = "<beta transaction node connection string>";
    
    var myAccount = "<Ethereum account address>";
    var myPassword = "<Ethereum account password>";
    
    var Web3 = require("web3");
    
    module.exports = {
      networks: {
        defaultnode: {
          provider:(() =>  {
          const AzureBlockchainProvider = new Web3.providers.HttpProvider(defaultnode);
    
          const web3 = new Web3(AzureBlockchainProvider);
          web3.eth.personal.unlockAccount(myAccount, myPassword);
    
          return AzureBlockchainProvider;
          })(),
    
          network_id: "*",
          gas: 0,
          gasPrice: 0,
          from: myAccount
        },
        alpha: {
          provider: new Web3.providers.HttpProvider(alpha),
          network_id: "*",
          gas: 0,
          gasPrice: 0
        },
        beta: {
          provider: new Web3.providers.HttpProvider(beta),
          network_id: "*",
          gas: 0,
          gasPrice: 0
        }
      }
    }
    ```

1. Сохраните изменения в `truffle-config.js`.

## <a name="create-smart-contract"></a>Создание смарт-контракта

1. В папке **contracts** создайте файл с именем `SimpleStorage.sol`. Добавьте следующий код.

    ```solidity
    pragma solidity >=0.4.21 <0.6.0;
    
    contract SimpleStorage {
        string public storedData;
    
        constructor(string memory initVal) public {
            storedData = initVal;
        }
    
        function set(string memory x) public {
            storedData = x;
        }
    
        function get() view public returns (string memory retVal) {
            return storedData;
        }
    }
    ```
    
1. В папке **migrations** создайте файл с именем `2_deploy_simplestorage.js`. Добавьте следующий код.

    ```solidity
    var SimpleStorage = artifacts.require("SimpleStorage.sol");
    
    module.exports = function(deployer) {
    
      // Pass 42 to the contract as the first constructor parameter
      deployer.deploy(SimpleStorage, "42", {privateFor: ["<alpha node public key>"], from:"<Ethereum account address>"})  
    };
    ```

1. Замените значения в угловых скобках.

    | Значение | ОПИСАНИЕ
    |-------|-------------
    | \<Открытый ключ узла alpha\> | Открытый ключ узла alpha
    | \<Адрес учетной записи Ethereum\> | Адрес учетной записи Ethereum, созданной в узле транзакций по умолчанию.

    В этом примере начальное значение переменной **storeData** равно 42.

    Переменная **privateFor** определяет узлы, которым доступен контракт. В этом примере учетная запись узла транзакций по умолчанию может передавать частную транзакцию узлу **alpha**. Вы добавляете открытые ключи для всех участников частной транзакции. Если не включить переменные **privateFor:** и **from:** , транзакции смарт-контракта станут общедоступными, и их смогут просматривать все участники консорциума.

1. Сохраните все файлы, выбрав **Файл > Сохранить все**.

## <a name="deploy-smart-contract"></a>Развертывание смарт-контракта

С помощью Truffle разверните `SimpleStorage.sol` в сети узлов транзакций по умолчанию.

```bash
truffle migrate --network defaultnode
```

Truffle сначала компилирует, а затем развертывает смарт-контракт **SimpleStorage**.

Выходные данные примера:

```
admin@desktop:/mnt/c/truffledemo$ truffle migrate --network defaultnode

2_deploy_simplestorage.js
=========================

   Deploying 'SimpleStorage'
   -------------------------
   > transaction hash:    0x3f695ff225e7d11a0239ffcaaab0d5f72adb545912693a77fbfc11c0dbe7ba72
   > Blocks: 2            Seconds: 12
   > contract address:    0x0b15c15C739c1F3C1e041ef70E0011e641C9D763
   > account:             0x1a0B9683B449A8FcAd294A01E881c90c734735C3
   > balance:             0
   > gas used:            0
   > gas price:           0 gwei
   > value sent:          0 ETH
   > total cost:          0 ETH


   > Saving migration to chain.
   > Saving artifacts
   -------------------------------------
   > Total cost:                   0 ETH


Summary
=======
> Total deployments:   2
> Final cost:          0 ETH
```

## <a name="validate-contract-privacy"></a>Проверка конфиденциальности контракта

Из-за конфиденциальной природы контракта его значения можно получить с помощью запросов только с узлов, которые объявлены в переменной **privateFor**. В этом примере мы можем отправить запрос узлу транзакций по умолчанию, так как учетная запись существует в таком узле. 

1. С помощью консоли Truffle подключитесь к узлу транзакций по умолчанию.

    ```bash
    truffle console --network defaultnode
    ```

1. В консоли Truffle выполните код, который возвращает значение экземпляра контракта.

    ```bash
    SimpleStorage.deployed().then(function(instance){return instance.get();})
    ```

    При успешном выполнении запроса узла транзакций по умолчанию будет возвращено значение 42. Например:

    ```
    admin@desktop:/mnt/c/truffledemo$ truffle console --network defaultnode
    truffle(defaultnode)> SimpleStorage.deployed().then(function(instance){return instance.get();})
    '42'
    ```

1. Закройте консоль Truffle.

    ```bash
    .exit
    ```

Так как мы объявили открытый ключ узла **alpha** в переменной **privateFor**, мы можем отправить запрос узлу **alpha**.

1. С помощью консоли Truffle подключитесь к узлу **alpha**.

    ```bash
    truffle console --network alpha
    ```

1. В консоли Truffle выполните код, который возвращает значение экземпляра контракта.

    ```bash
    SimpleStorage.deployed().then(function(instance){return instance.get();})
    ```

    При успешном выполнении запроса к узлу **alpha** будет возвращено значение 42. Например:

    ```
    admin@desktop:/mnt/c/truffledemo$ truffle console --network alpha
    truffle(alpha)> SimpleStorage.deployed().then(function(instance){return instance.get();})
    '42'
    ```

1. Закройте консоль Truffle.

    ```bash
    .exit
    ```

Так как мы не объявили открытый ключ узла **beta** в переменной **privateFor**, мы не сможем отправить запрос узлу **beta** из-за конфиденциальности контракта.

1. С помощью консоли Truffle подключитесь к узлу **beta**.

    ```bash
    truffle console --network beta
    ```

1. Выполните код, который возвращает значение экземпляра контракта.

    ```bash
    SimpleStorage.deployed().then(function(instance){return instance.get();})
    ```

1. Выполнение запроса к узлу **beta** завершится ошибкой, так как контракт является частным. Например:

    ```
    admin@desktop:/mnt/c/truffledemo$ truffle console --network beta
    truffle(beta)> SimpleStorage.deployed().then(function(instance){return instance.get();})
    Thrown:
    Error: Returned values aren't valid, did it run Out of Gas?
        at XMLHttpRequest._onHttpResponseEnd (/mnt/c/truffledemo/node_modules/xhr2-cookies/xml-http-request.ts:345:8)
        at XMLHttpRequest._setReadyState (/mnt/c/truffledemo/node_modules/xhr2-cookies/xml-http-request.ts:219:8)
        at XMLHttpRequestEventTarget.dispatchEvent (/mnt/c/truffledemo/node_modules/xhr2-cookies/xml-http-request-event-target.ts:44:13)
        at XMLHttpRequest.request.onreadystatechange (/mnt/c/truffledemo/node_modules/web3-providers-http/src/index.js:96:13)
    ```

1. Закройте консоль Truffle.

    ```bash
    .exit
    ```
    
## <a name="send-a-transaction"></a>Отправка транзакции

1. Создайте файл с именем `sampletx.js`. Сохраните его в корневом каталоге проекта.
1. Следующий скрипт задает для переменной **storedData** значение 65. Добавьте код в новый файл.

    ```javascript
    var SimpleStorage = artifacts.require("SimpleStorage");
    
    module.exports = function(done) {
      console.log("Getting deployed version of SimpleStorage...")
      SimpleStorage.deployed().then(function(instance) {
        console.log("Setting value to 65...");
        return instance.set("65", {privateFor: ["<alpha node public key>"], from:"<Ethereum account address>"});
      }).then(function(result) {
        console.log("Transaction:", result.tx);
        console.log("Finished!");
        done();
      }).catch(function(e) {
        console.log(e);
        done();
      });
    };
    ```

    Замените значения в угловых скобках, а затем сохраните файл.

    | Значение | ОПИСАНИЕ
    |-------|-------------
    | \<Открытый ключ узла alpha\> | Открытый ключ узла alpha
    | \<Адрес учетной записи Ethereum\> | Адрес учетной записи Ethereum, созданной в узле транзакций по умолчанию.

    Переменная **privateFor** определяет узлы, которым будет доступна транзакция. В этом примере учетная запись узла транзакций по умолчанию может передавать частную транзакцию узлу **alpha**. Вам нужно добавить открытые ключи для всех участников частной транзакции.

1. С помощью Truffle выполните скрипт для узла транзакций по умолчанию.

    ```bash
    truffle exec sampletx.js --network defaultnode
    ```

1. В консоли Truffle выполните код, который возвращает значение экземпляра контракта.

    ```bash
    SimpleStorage.deployed().then(function(instance){return instance.get();})
    ```

    При успешном выполнении транзакции будет возвращено значение 65. Например:
    
    ```
    Getting deployed version of SimpleStorage...
    Setting value to 65...
    Transaction: 0x864e67744c2502ce75ef6e5e09d1bfeb5cdfb7b880428fceca84bc8fd44e6ce0
    Finished!
    ```

1. Закройте консоль Truffle.

    ```bash
    .exit
    ```
    
## <a name="validate-transaction-privacy"></a>Проверка конфиденциальности транзакции

Из-за конфиденциальности транзакций их можно выполнять только на тех узлах, которые были объявлены в переменной **privateFor**. В этом примере мы можем выполнить транзакции, так как мы объявили открытый ключ узла **alpha** в переменной **privateFor**. 

1. С помощью Truffle выполните транзакцию на узле **alpha**.

    ```bash
    truffle exec sampletx.js --network alpha
    ```
    
1. Выполните код, который возвращает значение экземпляра контракта.

    ```bash
    SimpleStorage.deployed().then(function(instance){return instance.get();})
    ```
    
    При успешном выполнении транзакции будет возвращено значение 65. Например:

    ```
    Getting deployed version of SimpleStorage...
    Setting value to 65...
    Transaction: 0x864e67744c2502ce75ef6e5e09d1bfeb5cdfb7b880428fceca84bc8fd44e6ce0
    Finished!
    ```
    
1. Закройте консоль Truffle.

    ```bash
    .exit
    ```

## <a name="clean-up-resources"></a>Очистка ресурсов

Если ресурсы больше не нужны, вы можете удалить их. Для этого удалите группу ресурсов `myResourceGroup`, которую создали с помощью службы "Блокчейн Azure".

Чтобы удалить группу ресурсов, сделайте следующее:

1. На портале Azure перейдите к **группам ресурсов** в области навигации слева и выберите группу ресурсов, которую необходимо удалить.
1. Выберите **Удалить группу ресурсов**. Подтвердите удаление, введя имя группы ресурсов и выбрав **Удалить**.

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве описано, как добавить два узла транзакций для демонстрации конфиденциальности контракта и транзакций. Вы использовали узел по умолчанию для развертывания частного смарт-контракта. Вы протестировали конфиденциальность, получив значения контракта с помощью запросов и выполнив транзакции в блокчейне.

> [!div class="nextstepaction"]
> [Разработка приложений блокчейна с помощью службы "Блокчейн Azure"](develop.md)
