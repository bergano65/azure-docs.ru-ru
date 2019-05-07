---
title: Подключение к службе "Блокчейн Azure" с помощью Geth
description: Подключение к сети службы "Блокчейн Azure" с помощью Geth
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: 0716a9326a54ae31d4f355fe5f4c88488339b390
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029982"
---
# <a name="quickstart-use-geth-to-connect-to-a-transaction-node"></a>Краткое руководство. Использование Geth для подключения к узлу транзакций

Geth — это клиент Go Ethereum, который можно использовать для подключения к экземпляру Geth на узле транзакций службы "Блокчейн Azure".

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Предварительные требования

* Установите [Geth](https://github.com/ethereum/go-ethereum/wiki/geth).
* [Создайте участника службы "Блокчейн Azure".](create-member.md)

## <a name="get-the-geth-connection-string"></a>Получите строку подключения Geth.

Строку подключения можно найти на портале Azure.

1. Войдите на [портале Azure](https://portal.azure.com).
1. Перейдите в область участника службы "Блокчейн Azure". Выберите **Transaction nodes** (Узлы транзакций) и щелкните ссылку на узел транзакций по умолчанию.

    ![Выбор узла транзакций по умолчанию](./media/connect-geth/transaction-nodes.png)

1. Выберите элемент **Строки подключения**.
1. Скопируйте строку подключения в разделе **HTTPS (Access key 1)** (HTTPS (ключ доступа 1)). Команда понадобится вам при работе со следующим разделом.

    ![Строка подключения](./media/connect-geth/connection-string.png)

## <a name="connect-to-geth"></a>Подключение к Geth

1. Откройте окно командной строки или оболочку.
1. Используйте подкоманду geth attach для присоединения к запущенному экземпляру Geth на узле транзакций. Вставьте строку подключения в качестве аргумента для подкоманды attach. Например,

    ```
    geth attach <connection string>
    ```

1. После подключения к консоли Ethereum для узла транзакций можно вызвать web3 JavaScript Dapp API или API администратора.

    Например, используйте указанный ниже API-интерфейс, чтобы получить значение chainId.

    ```bash
    admin.nodeInfo.protocols.istanbul.config.chainId
    ```

    В этом примере значением для chainId будет 297.

    ![Параметр службы "Блокчейн Azure"](./media/connect-geth/geth-attach.png)

1. Чтобы отключиться от консоли, введите `exit`.

## <a name="next-steps"></a>Дополнительная информация

При работе с этим кратким руководством вы использовали клиент Geth для подключения к экземпляру Geth на узле транзакций службы "Блокчейн Azure". Ознакомьтесь со следующим руководством, чтобы узнать, как развернуть и отправить транзакцию с помощью Truffle.

> [!div class="nextstepaction"]
> [Отправка транзакций](send-transaction.md)