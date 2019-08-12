---
title: Использование смарт-контрактов в службе "Блокчейн Azure"
description: Руководство по использованию службы "Блокчейн Azure" для развертывания смарт-контракта и выполнения функции путем транзакции.
services: azure-blockchain
author: PatAltimore
ms.author: patricka
ms.date: 07/31/2019
ms.topic: tutorial
ms.service: azure-blockchain
ms.reviewer: chrisseg
ms.openlocfilehash: 1843bd66e11a6686c9ae81fb8e30c7b030e889b7
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705135"
---
# <a name="tutorial-use-smart-contracts-on-azure-blockchain-service"></a>Руководство по Использование смарт-контрактов в службе "Блокчейн Azure"

В этом руководстве описано, как использовать комплект SDK службы "Блокчейн Azure" для Ethereum, чтобы создать и развернуть смарт-контракт, а затем выполнить функцию смарт-контракта путем совершения транзакции в сети блокчейн-консорциума.

С помощью комплекта SDK службы "Блокчейн Azure" для Ethereum выполняются такие задачи:

> [!div class="checklist"]
> * подключение к участнику блокчейн-консорциума службы "Блокчейн Azure";
> * создание смарт-контракта;
> * развертывание смарт-контракта;
> * выполнение функции смарт-контракта путем транзакции;
> * запрос состояния контракта.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Предварительные требования

* См. подробнее об [использовании созданию участника блокчейна на портале Azure](create-member.md) и [руководство по созданию участника блокчейна службы "Блокчейн Azure" с помощью Azure CLI](create-member-cli.md);
* [Visual Studio Code](https://code.visualstudio.com/Download)
* [расширение "Комплект SDK Блокчейна Azure для Ethereum"](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain);
* [Node.js](https://nodejs.org)
* [Git](https://git-scm.com)
* [Python](https://www.python.org/downloads/release/python-2715/). Добавьте в путь файл python.exe. Это необходимо для комплекта SDK службы "Блокчейн Azure".
* [Truffle](https://www.trufflesuite.com/docs/truffle/getting-started/installation);
* [Ganache CLI](https://github.com/trufflesuite/ganache-cli);

### <a name="verify-azure-blockchain-development-kit-environment"></a>Проверка среды комплекта SDK службы "Блокчейн Azure"

Комплект SDK службы "Блокчейн Azure" проверяет, выполняются ли необходимые условия для среды разработки. Чтобы проверить среду разработки, выполните следующие действия:

В палитре команд VS Code выберите **Azure Blockchain: Show Welcome Page** (Блокчейн Azure. Показать страницу приветствия).

Комплект SDK службы "Блокчейн Azure" запускает проверочный скрипт, выполнение которого займет около минуты. Чтобы просмотреть выходные данные, выберите **Терминал > Создать терминал**. В строке меню терминала выберите вкладку **Выходные данные**, а в раскрывающемся списке — **Блокчейн Azure**. При успешном завершении проверки отобразится следующий результат:

![Допустимая среда разработки](./media/send-transaction/valid-environment.png)

 Если требуемое средство отсутствует, отобразится новая вкладка **Azure Blockchain Development Kit - Preview** (Комплект SDK службы "Блокчейн Azure" — предварительная версия) со списком приложений, которые нужно установить, и ссылками для их скачивания.

![Приложения, необходимые для использования комплекта SDK](./media/send-transaction/required-apps.png)

## <a name="connect-to-consortium-member"></a>Подключение к участнику консорциума

Подключиться к участникам консорциума можно с помощью расширения "Комплект SDK службы "Блокчейн Azure" для VS Code. После подключения к консорциуму вы можете создавать и развертывать смарт-контракты в участнике консорциума службы "Блокчейн Azure".

Если у вас нет доступа к участнику консорциума службы "Блокчейн Azure", выполните предварительные требования, изложенные в [руководстве по созданию участника блокчейна на портале Azure](create-member.md) и [руководство по созданию участника блокчейна службы "Блокчейн Azure" с помощью Azure CLI](create-member-cli.md).

1. В области обозревателя Visual Studio Code (VS Code) разверните расширение **Блокчейн Azure**.
1. Выберите **Connect to Consortium** (Подключиться к консорциуму).

   ![Подключение к консорциуму](./media/send-transaction/connect-consortium.png)

    При появлении запроса на проверку подлинности Azure следуйте инструкциям, чтобы выполнить аутентификацию с помощью браузера.
1. В раскрывающемся списке палитры команд выберите **Connect to Azure Blockchain Service consortium** (Подключиться к консорциуму службы "Блокчейн Azure").
1. Выберите подписку и группу ресурсов, связанные с участником консорциума службы "Блокчейн Azure".
1. Выберите свой консорциум из списка.

Консорциум и участники блокчейна отображаются на боковой панели проводника Visual Studio.

![Консорциум, отображаемый в проводнике](./media/send-transaction/consortium-node.png)

## <a name="create-a-smart-contract"></a>Создание смарт-контракта

Комплект SDK службы "Блокчейн Azure" для Ethereum использует шаблоны проектов и средства Truffle для создания, сборки и развертывания контрактов.

1. В палитре команд VS Code выберите **Azure Blockchain: Создать проект Solidity**.
1. Выберите **Create basic project** (Создать базовый проект).
1. Создайте папку с именем `HelloBlockchain` и выберите **Select new project path** (Выбрать путь к новому проекту).

Комплект SDK службы "Блокчейн Azure" создаст и инициализирует проект Solidity. Базовый проект содержит пример смарт-контракта **HelloBlockchain** и все необходимые файлы для сборки и развертывания в участнике консорциума в службе "Блокчейн Azure". Создание проекта может занять несколько минут. Вы можете отслеживать ход выполнения на панели терминала VS Code, выбрав выходные данные для Azure Блокчейн.

Структура проекта выглядит так, как показано в следующем примере.

   ![Проект Solidity](./media/send-transaction/solidity-project.png)

## <a name="build-a-smart-contract"></a>Сборка смарт-контракта

Смарт-контракты находятся в каталоге проекта **contracts**. Нужно выполнить сборку смарт-контрактов перед их развертыванием в блокчейне. Используйте команду **Build Contracts** (Выполнить сборку контрактов), чтобы компилировать все смарт-контракты в проекте.

1. В боковой панели обозревателя VS Code разверните папку **contracts** своего проекта.
1. Щелкните правой кнопкой мыши файл **HelloBlockchain.sol** и в меню выберите пункт **Build Contracts** (Выполнить сборку контрактов).

    ![Сборка контрактов](./media/send-transaction/build-contracts.png)

Комплект SDK службы "Блокчейн Azure" использует Truffle для компиляции смарт-контрактов.

![Выходные данные компиляции](./media/send-transaction/compile-output.png)

## <a name="deploy-a-smart-contract"></a>Развертывание смарт-контракта

Truffle использует скрипты миграции для развертывания контрактов в сети Ethereum. Скрипты миграции — это файлы JavaScript, которые находятся в каталоге проекта **migrations**.

1. Чтобы развернуть смарт-контракт, щелкните правой кнопкой мыши файл **HelloBlockchain.sol** и в меню выберите пункт **Deploy Contracts** (Развернуть контракты).
1. Выберите сеть консорциума службы "Блокчейн Azure"в разделе **From truffle-config.js** (Из truffle-config.js). Сеть блокчейн-консорциума добавлена в файл конфигурации проекта Truffle при создании проекта.
1. Выберите **Generate mnemonic** (Создать мнемонический код). Задайте имя файлу с мнемоническим кодом и сохраните его в папке проекта. Например, `myblockchainmember.env`. Файл с мнемоническим кодом используется для создания закрытого ключа Ethereum для вашего участника блокчейна.

Комплект SDK службы "Блокчейн Azure" использует Truffle, чтобы выполнить скрипт миграции для развертывания контрактов в блокчейне.

![Успешное развертывание контракта](./media/send-transaction/deploy-contract.png)

## <a name="call-a-contract-function"></a>Вызов функции контракта

Функция **SendRequest** контракта **HelloBlockchain** изменяет переменную состояния **RequestMessage**. Изменение состояния сети блокчейна выполняется через транзакцию. Можно создать скрипт для выполнения функции **SendRequest** путем транзакции.

1. В корне проекта Truffle создайте файл с именем `sendrequest.js`. Добавьте в файл следующий код Web3 JavaScript.

    ```javascript
    var HelloBlockchain = artifacts.require("HelloBlockchain");
        
    module.exports = function(done) {
      console.log("Getting the deployed version of the HelloBlockchain smart contract")
      HelloBlockchain.deployed().then(function(instance) {
        console.log("Calling SendRequest function for contract ", instance.address);
        return instance.SendRequest("Hello, blockchain!");
      }).then(function(result) {
        console.log("Transaction hash: ", result.tx);
        console.log("Request complete");
        done();
      }).catch(function(e) {
        console.log(e);
        done();
      });
    };
    ```

1. При создании проекта с помощью комплекта SDK службы "Блокчейн Azure" создается файл конфигурации Truffle со сведениями о конечной точке сети блокчейн-консорциума. Откройте в проекте файл **truffle-config.js**. В этом файле конфигурации указаны две сети — одна с именем development и вторая с именем консорциума.
1. В терминале VS Code используйте Truffle, чтобы выполнить скрипт для своей сети блокчейн-консорциума. В строке меню терминала выберите вкладку **Терминал**, а в раскрывающемся списке — **PowerShell**.

    ```PowerShell
    truffle exec sendrequest.js --network <blockchain network>
    ```

    Замените \<blockchain network\> именем сети блокчейн, указанным в файле **truffle-config.js**.

Truffle выполнит скрипт для сети блокчейн.

![Выходные данные скрипта](./media/send-transaction/execute-transaction.png)

При выполнении функции контракта через транзакцию транзакция не обрабатывается до тех пор, пока не будет создан блок. Функции, предназначенные для выполнения путем транзакции, вместо значения возвращают идентификатор транзакции.

## <a name="query-contract-state"></a>Запрос состояния контракта

Функции смарт-контракта могут возвращать текущее значение переменных состояния. Добавим функцию для возврата значения переменной состояния.

1. В файле **HelloBlockchain.sol** добавьте функцию **getMessage** в смарт-контракт **HelloBlockchain**.

    ``` solidity
    function getMessage() public view returns (string memory)
    {
        if (State == StateType.Request)
            return RequestMessage;
        else
            return ResponseMessage;
    }
    ```

    Функция возвращает сообщение, хранящееся в переменной состояния, о текущем состоянии контракта.

1. Щелкните правой кнопкой мыши файл **HelloBlockchain.sol** и в меню выберите **Build Contracts** (Выполнить сборку контрактов), чтобы внести изменения в смарт-контракт.
1. Чтобы развернуть его, щелкните правой кнопкой мыши файл **HelloBlockchain.sol** и в меню выберите пункт **Deploy Contracts** (Развернуть контракты).
1. Затем создайте скрипт, предназначенный для вызова функции **getMessage**. В корне проекта Truffle создайте файл с именем `getmessage.js`. Добавьте в файл следующий код Web3 JavaScript.

    ```javascript
    var HelloBlockchain = artifacts.require("HelloBlockchain");
    
    module.exports = function(done) {
      console.log("Getting the deployed version of the HelloBlockchain smart contract")
      HelloBlockchain.deployed().then(function(instance) {
        console.log("Calling getMessage function for contract ", instance.address);
        return instance.getMessage();
      }).then(function(result) {
        console.log("Request message value: ", result);
        console.log("Request complete");
        done();
      }).catch(function(e) {
        console.log(e);
        done();
      });
    };
    ```

1. В терминале VS Code используйте Truffle, чтобы выполнить скрипт для своей сети блокчейн. В строке меню терминала выберите вкладку **Терминал**, а в раскрывающемся списке — **PowerShell**.

    ```bash
    truffle exec getmessage.js --network <blockchain network>
    ```

    Замените \<blockchain network\> именем сети блокчейн, указанным в файле **truffle-config.js**.

Скрипт выполняет запрос к смарт-контракту, вызывая функцию getMessage. Возвращается текущее значение переменной состояния **RequestMessage**.

![Выходные данные скрипта](./media/send-transaction/execute-get.png)

Обратите внимание, что возвращается не значение **Hello, blockchain!** , а заполнитель. При изменении и развертывании контракт получает новый адрес контракта, а переменным состояния присваиваются значения в конструкторе смарт-контрактов. Скрипт миграции Truffle **2_deploy_contracts.js** развертывает смарт-контракт и передает значение заполнителя в качестве аргумента. Конструктор задает для переменной состояния **RequestMessage** значение заполнителя, которое и возвращается.

1. Чтобы задать значение для переменной состояния **RequestMessage** и запросить его, выполните еще раз скрипты **sendrequest.js** и **getmessage.js**.

    ![Выходные данные скрипта](./media/send-transaction/execute-set-get.png)

    Скрипт **sendrequest.js** задает переменной состояния **RequestMessage** значение **Hello, blockchain!** , а **getmessage.js** запрашивает значение переменной состояния контракта **RequestMessage** и возвращает **Hello, blockchain!** .

## <a name="clean-up-resources"></a>Очистка ресурсов

Если ресурсы больше не нужны, вы можете удалить их. Для этого удалите группу ресурсов `myResourceGroup`, которую вы создали при выполнении предварительных требований *краткого руководства по созданию участника блокчейна*.

Чтобы удалить группу ресурсов, сделайте следующее:

1. На портале Azure перейдите к **группам ресурсов** в области навигации слева и выберите группу ресурсов, которую необходимо удалить.
1. Выберите **Удалить группу ресурсов**. Подтвердите удаление, введя имя группы ресурсов и выбрав **Удалить**.

## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как создать проект Solidity с помощью комплекта SDK службы "Блокчейн Azure". Вы создали и развернули смарт-контракт, а также вызывали функцию через транзакцию в сети блокчейн-консорциума, размещенной в службе "Блокчейн Azure".

> [!div class="nextstepaction"]
> [Разработка приложений блокчейна с помощью службы "Блокчейн Azure"](develop.md)
