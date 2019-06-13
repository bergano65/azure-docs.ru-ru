---
title: Подключение с помощью Truffle
description: Подключение к сети службы "Блокчейн Azure" с помощью Truffle
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/29/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: 8b1a701beac867c5f331ffa1ee1dee615961c6b3
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/30/2019
ms.locfileid: "66416304"
---
# <a name="quickstart-use-truffle-to-connect-to-an-azure-blockchain-service-network"></a>Краткое руководство. Подключение к сети службы "Блокчейн Azure" с использованием Truffle

Truffle — это среда разработки блокчейна, с помощью которой можно подключиться к узлу службы "Блокчейн Azure".

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

1. Запустите интерактивную консоль разработки Truffle.

    ``` bash
    truffle develop
    ```

    Truffle создает блокчейн для локальной разработки и предоставляет интерактивную консоль.

## <a name="connect-to-transaction-node"></a>Подключение к узлу транзакций

Для подключения к узлу транзакций используйте *Web3*. Вы можете получить строку подключения *Web3* на портале Azure.

1. Войдите на [портале Azure](https://portal.azure.com).
1. Перейдите в область участника службы "Блокчейн Azure". Выберите **Transaction nodes** (Узлы транзакций) и щелкните ссылку на узел транзакций по умолчанию.

    ![Выбор узла транзакций по умолчанию](./media/connect-truffle/transaction-nodes.png)

1. Выберите **Sample Code > Web3** (Пример кода > Web3).
1. Скопируйте код JavaScript в разделе **HTTPS (Access key 1)** (HTTPS (ключ доступа 1)). Этот код понадобится для интерактивной консоли разработки Truffle.

    ![Код Web3](./media/connect-truffle/web3-code.png)

1. Вставьте код JavaScript из предыдущего шага в интерактивную консоль разработки Truffle. С помощью кода создается объект web3, подключенный к узлу транзакций службы "Блокчейн Azure".

    Выходные данные примера:

    ```bash
    truffle(develop)> var Web3 = require("Web3");
    truffle(develop)> var provider = new Web3.providers.HttpProvider("https://myblockchainmember.blockchain.azure.com:3200/hy5FMu5TaPR0Zg8GxiPwned");
    truffle(develop)> var web3 = new Web3(provider);
    ```

    Вы можете вызывать методы в объекте **web3** для взаимодействия с узлом транзакций.

1. Вызовите метод **getBlockNumber**, чтобы отобразился текущий номер блока.

    ```bash
    web3.eth.getBlockNumber();
    ```

    Выходные данные примера:

    ```bash
    truffle(develop)> web3.eth.getBlockNumber();
    18567
    ```
1. Выйдите из консоли разработки Truffle.

    ```bash
    .exit
    ```

## <a name="next-steps"></a>Дополнительная информация

При работе с этим кратким руководством вы создали проект Truffle для подключения к узлу транзакций по умолчанию в службе "Блокчейн Azure".

Ознакомьтесь со следующим руководством, чтобы узнать, как использовать Truffle для отправки транзакции в вашу сеть блокчейна консорциума.

> [!div class="nextstepaction"]
> [Отправка транзакций](send-transaction.md)