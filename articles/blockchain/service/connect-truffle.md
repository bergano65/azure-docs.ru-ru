---
title: Подключение с помощью Truffle
description: Подключение к сети службы "Блокчейн Azure" с помощью Truffle
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 08/29/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: 822402f548fe0fc866051a9c77adef6e37c3fa75
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70240897"
---
# <a name="quickstart-use-truffle-to-connect-to-a-transaction-node"></a>Краткое руководство. Подключение к узлу транзакций с помощью Truffle

Truffle — это среда разработки блокчейна, с помощью которой можно подключаться к узлу транзакций службы "Блокчейн Azure".

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Предварительные требования

* [Создайте участника службы "Блокчейн Azure".](create-member.md)
* Установите [Truffle](https://github.com/trufflesuite/truffle). Для работы с Truffle нужно установить несколько инструментов, включая [Node.js](https://nodejs.org) и [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
* Установите [Python 2.7.15](https://www.python.org/downloads/release/python-2715/). Python требуется для Web3.

## <a name="create-truffle-project"></a>Создание проекта Truffle

1. Откройте командную строку или оболочку Node.js.
1. Перейдите к каталогу, в котором нужно создать каталог проекта Truffle.
1. Создайте каталог для проекта и укажите путь к новому каталогу. Например,

    ``` bash
    mkdir truffledemo
    cd truffledemo
    ```

1. Инициализируйте проект Truffle.

    ``` bash
    truffle init
    ```

1. Установите web3 (API JavaScript для Ethereum) в папке проекта. Сейчас требуется web3 версии 1.0.0-beta.37.

    ``` bash
    npm install web3@1.0.0-beta.37
    ```

    Во время установки могут появиться предупреждения npm.
    
## <a name="configure-truffle-project"></a>Настройка проекта Truffle

Для настройки проекта Truffle вам потребуется дополнительная информация об узле транзакций с портала Azure.

### <a name="transaction-node-endpoint-addresses"></a>Адреса конечных точек узлов транзакций

1. На портале Azure перейдите к узлу транзакций по умолчанию и выберите **Узлы транзакций > Строки подключения**.
1. Скопируйте и сохраните URL-адрес конечной точки из раздела **HTTPS (ключ доступа 1)** . Вам потребуются адреса конечных точек для файла конфигурации смарт-контракта для дальнейшего использования в этом руководстве.

    ![Адрес конечных точек транзакций](./media/connect-truffle/endpoint.png)

### <a name="edit-configuration-file"></a>Изменение файла конфигурации

Затем вам нужно изменить файл конфигурации Truffle, указав конечную точку узла транзакции.

1. Откройте в редакторе файл конфигурации Truffle `truffle-config.js` в папке проекта **truffledemo**.
1. Замените содержимое файла следующими данными о конфигурации. Добавьте переменную, содержащую адрес конечной точки. Замените фрагменты в угловых скобках значениями, полученными выше.

    ``` javascript
    var defaultnode = "<default transaction node connection string>";   
    var Web3 = require("web3");
    
    module.exports = {
      networks: {
        defaultnode: {
          provider: new Web3.providers.HttpProvider(defaultnode),
          network_id: "*"
        }
      }
    }
    ```

1. Сохраните изменения в `truffle-config.js`.

## <a name="connect-to-transaction-node"></a>Подключение к узлу транзакций

Для подключения к узлу транзакций используйте *Web3*.

1. С помощью консоли Truffle подключитесь к узлу транзакций по умолчанию. В командной строке или оболочке выполните следующую команду:

    ``` bash
    truffle console --network defaultnode
    ```

    Truffle подключается к узлу транзакции по умолчанию и предоставляет интерактивную консоль.

    Вы можете вызывать методы в объекте **web3** для взаимодействия с узлом транзакций.

1. Вызовите метод **getBlockNumber**, чтобы отобразился текущий номер блока.

    ```bash
    web3.eth.getBlockNumber();
    ```

    Выходные данные примера:

    ```bash
    truffle(defaultnode)> web3.eth.getBlockNumber();
    18567
    ```
1. Закройте консоль Truffle.

    ```bash
    .exit
    ```

## <a name="next-steps"></a>Дополнительная информация

При работе с этим кратким руководством вы создали проект Truffle для подключения к узлу транзакций по умолчанию в службе "Блокчейн Azure".

В следующем руководстве описано, как использовать комплект SDK службы "Блокчейн Azure" для Ethereum и Truffle, чтобы выполнять функцию смарт-контракта путем совершения транзакции в сети блокчейн-консорциума.

> [!div class="nextstepaction"]
> [Использование смарт-контрактов в службе "Блокчейн Azure"](send-transaction.md)
