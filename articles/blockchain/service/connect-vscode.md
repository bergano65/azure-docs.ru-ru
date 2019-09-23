---
title: Используйте комплект SDK службы "Блокчейн Azure", чтобы подключится к службе "Блокчейн Azure"
description: Подключение к сети консорциума службы "Блокчейн Azure" с помощью комплекта SDK службы "Azure Блокчейн" для Ethereum в Visual Studio Code
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 09/10/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: chrisseg
manager: femila
ms.openlocfilehash: 82b71a9d837ed3cb1d9461c0de2559178685b4b4
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/12/2019
ms.locfileid: "70935362"
---
# <a name="quickstart-use-visual-studio-code-to-connect-to-an-azure-blockchain-service-consortium-network"></a>Краткое руководство. Подключение к сети консорциума службы "Блокчейн Azure" с помощью Visual Studio Code

В этом кратком руководстве вы научитесь устанавливать и использовать комплект SDK службы "Блокчейн Azure" для Ethereum Visual Studio Code, чтобы присоединиться к консорциуму в службе "Блокчейн Azure". Комплект SDK службы "Блокчейн Azure" упрощает создание, подключение, сборку и развертывание смарт-контрактов в реестре Ethereum. 

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

![Допустимая среда разработки](./media/connect-vscode/valid-environment.png)

 Если требуемое средство отсутствует, отобразится новая вкладка **Azure Blockchain Development Kit - Preview** (Комплект SDK службы "Блокчейн Azure" — предварительная версия) со списком приложений, которые нужно установить, и ссылками для их скачивания.

![Приложения, необходимые для использования комплекта SDK](./media/connect-vscode/required-apps.png)

Чтобы продолжить изучение краткого руководства установите недостающие компоненты.

## <a name="connect-to-consortium-member"></a>Подключение к участнику консорциума

Подключиться к участникам консорциума можно с помощью расширения "Комплект SDK службы "Блокчейн Azure" для VS Code. После подключения к консорциуму вы можете создавать и развертывать смарт-контракты в участнике консорциума службы "Блокчейн Azure".

Если у вас нет доступа к участнику консорциума службы "Блокчейн Azure", выполните предварительные требования, изложенные в [руководстве по созданию участника блокчейна на портале Azure](create-member.md) и [руководство по созданию участника блокчейна службы "Блокчейн Azure" с помощью Azure CLI](create-member-cli.md).

1. В области обозревателя Visual Studio Code (VS Code) разверните расширение **Блокчейн Azure**.
1. Выберите **Connect to Consortium** (Подключиться к консорциуму).

   ![Подключение к консорциуму](./media/connect-vscode/connect-consortium.png)

    При появлении запроса на проверку подлинности Azure следуйте инструкциям, чтобы выполнить аутентификацию с помощью браузера.
1. В раскрывающемся списке палитры команд выберите **Connect to Azure Blockchain Service consortium** (Подключиться к консорциуму службы "Блокчейн Azure").
1. Выберите подписку и группу ресурсов, связанные с участником консорциума службы "Блокчейн Azure".
1. Выберите свой консорциум из списка.

Консорциум и участники блокчейна отображаются на боковой панели проводника Visual Studio.

![Консорциум, отображаемый в проводнике](./media/connect-vscode/consortium-node.png)

## <a name="next-steps"></a>Дополнительная информация

В этом кратком руководстве вы использовали комплект SDK службы "Блокчейн Azure" для Ethereum Visual Studio Code, чтобы присоединиться к консорциуму в службе "Блокчейн Azure". В следующем руководстве описано, как с помощью комплекта SDK службы "Блокчейн Azure" для Ethereum и Truffle создать, собрать, развернуть и выполнить функцию смарт-контракта путем совершения транзакции.

> [!div class="nextstepaction"]
> [Создание и развертывание смарт-контрактов с помощью Visual Studio Code](send-transaction.md)