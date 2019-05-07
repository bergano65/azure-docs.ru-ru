---
title: Создание службы "Блокчейн Azure" с помощью Azure CLI
description: Воспользуйтесь возможностями службы "Блокчейн Azure", чтобы создать участника блокчейн-сети с помощью Azure CLI.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: seal
manager: femila
ms.openlocfilehash: e1b7558ea83c8948a8984215e15040e4d929cb1b
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65141381"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-azure-cli"></a>Краткое руководство. Создание участника блокчейн-сети службы "Блокчейн Azure" с помощью Azure CLI

Служба "Блокчейн Azure" — это платформа для блокчейна, на которой можно реализовать бизнес-логику в рамках смарт-контрактов. В этом кратком руководстве объясняется, как создать участника блокчейн-сети с помощью Azure CLI.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="launch-azure-cloud-shell"></a>Запуск Azure Cloud Shell

Azure Cloud Shell — это бесплатная интерактивная оболочка, с помощью которой можно выполнять действия, описанные в этой статье. Она включает предварительно установленные общие инструменты Azure и настроена для использования с вашей учетной записью.

Чтобы открыть Cloud Shell, просто выберите **Попробовать** в правом верхнем углу блока кода. Cloud Shell можно также запустить в отдельной вкладке браузера, перейдя на страницу [https://shell.azure.com/bash](https://shell.azure.com/bash). Нажмите кнопку **Копировать**, чтобы скопировать блоки кода. Вставьте код в Cloud Shell и нажмите клавишу "ВВОД", чтобы выполнить его.

Если вы решили установить и использовать CLI локально, для выполнения инструкций, приведенных в этом руководстве вам потребуется Azure CLI 2.0.51 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте группу ресурсов с помощью команды [az group create](https://docs.microsoft.com/cli/azure/group). Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими. В следующем примере создается группа ресурсов с именем *myResourceGroup* в расположении *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-blockchain-member"></a>Создание участника блокчейн-сети

Создайте участника блокчейн-сети в службе "Блокчейн Azure", которая выполняет протокол реестра Quorum в новом консорциуме.

Для этого вам нужно передать несколько параметров и свойств. Замените указанные ниже параметры своими значениями.

| Параметр | ОПИСАНИЕ |
|---------|-------------|
| **resource-group** | Имя группы ресурсов, в которой создаются ресурсы службы "Блокчейн Azure". Выберите группу ресурсов, созданную при работе с предыдущим разделом.
| **name** | Уникальное имя, идентифицирующее участника блокчейн-сети в службе "Блокчейн Azure". Это имя используется в адресе общедоступной конечной точки. Например, `myblockchainmember.blockchain.azure.com`.
| **расположение** | Регион Azure, в котором создается участник блокчейн-сети. Например, `eastus`. Выберите расположение, наиболее близкое к пользователям или другим приложениям Azure.
| **password** | Пароль учетной записи участника. Пароль учетной записи участника используется для обычной аутентификации в общедоступной конечной точке участника блокчейн-сети.
| **consortium** | Имя консорциума, который создается или к которому присоединяется участник.
| **consortiumManagementAccountPassword** | Пароль управления консорциума. Используется для присоединения к консорциуму.
| **skuName** | Тип уровня. Используйте S0, чтобы выбрать ценовую категорию "Стандартный", или B0, чтобы выбрать ценовую категорию "Базовый".

```azurecli-interactive
az resource create --resource-group myResourceGroup --name myblockchainmember --resource-type Microsoft.Blockchain/blockchainMembers --is-full-object --properties "{ \"location\": \"eastus\", \"properties\": {\"password\": \"strongMemberAccountPassword@1\", \"protocol\": \"Quorum\", \"consortium\": \"myConsortiumName\", \"consortiumManagementAccountPassword\": \"strongConsortiumManagementPassword@1\" }, \"sku\": { \"name\": \"S0\" } }"
```

Для создания участника блокчейн-сети и вспомогательных ресурсов требуется около 10 минут.

В следующем примере выходных данных показано, что операция создания успешно выполнена.

```json
{
  "id": "/subscriptions/<subscriptionId>/resourceGroups/myResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/mymembername",
  "kind": null,
  "location": "eastus",
  "name": "mymembername",
  "properties": {
    "ConsortiumMemberDisplayName": "mymembername",
    "consortium": "myConsortiumName",
    "consortiumManagementAccountAddress": "0xfe5fbb9d1036298abf415282f52397ade5d5beef",
    "consortiumManagementAccountPassword": null,
    "consortiumRole": "ADMIN",
    "dns": "mymembername.blockchain.azure.com",
    "protocol": "Quorum",
    "provisioningState": "Succeeded",
    "userName": "mymembername",
    "validatorNodesSku": {
      "capacity": 2
    }
  },
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "S0",
    "tier": "Standard"
  },
  "type": "Microsoft.Blockchain/blockchainMembers"
}
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Данные о созданном участнике блокчейн-сети можно использовать для работы со следующим кратким руководством или учебником. Если ресурсы больше не нужны, вы можете удалить их. Для этого удалите группу ресурсов `myResourceGroup`, которую создали с помощью службы "Блокчейн Azure".

Выполните следующую команду, чтобы удалить группу ресурсов и все связанные с ней ресурсы.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Дополнительная информация

Теперь, когда вы создали участника блокчейн-сети, ознакомьтесь с одним из кратких руководств по подключению к узлу транзакций для [Geth](connect-geth.md), [MetaMask](connect-metamask.md) или [Truffle](connect-truffle.md).

> [!div class="nextstepaction"]
> [Подключение к сети службы "Блокчейн Azure" с использованием Truffle](connect-truffle.md)
