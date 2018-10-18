---
title: Краткое руководство. Создание учетной записи Служб мультимедиа Azure с помощью Azure CLI | Документация Майкрософт
description: В этом кратком руководстве описано, как создать учетную запись Служб мультимедиа Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: quickstart
ms.custom: mvc
ms.date: 10/15/2018
ms.author: juliako
ms.openlocfilehash: de54571308b737b9160a39ee4ba5d4b2d9f15775
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49376539"
---
# <a name="quickstart-create-an-azure-media-services-account"></a>Краткое руководство по созданию учетной записи Служб мультимедиа Azure

Как разработчикам, так и создателям содержимого мультимедиа для хранения, шифрования, кодирования, контроля и потоковой передачи данных мультимедиа в Azure нужно создать учетную запись Служб мультимедиа. При создании учетной записи Служб мультимедиа необходимо предоставить идентификатор ресурса учетной записи хранения Azure. Указанная учетная запись хранения присоединена к учетной записи Служб мультимедиа. Этот ресурс учетной записи хранения должен находиться в том же географическом регионе, что и учетная запись Служб мультимедиа.  

В этом кратком руководстве описано, как создать учетную запись Служб мультимедиа Azure с помощью Azure CLI.  

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-azure"></a>Вход в Azure

Войдите на [портал Azure](http://portal.azure.com) и запустите **CloudShell**, чтобы выполнить команды CLI, приведенные на следующих шагах.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Если вы решили установить и использовать CLI локально, для работы с этим руководством вам понадобится Azure CLI 2.0 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI]( /cli/azure/install-azure-cli). 

## <a name="set-the-azure-subscription"></a>Настройка подписки Azure

В приведенной ниже команде укажите идентификатор подписки Azure, который необходимо использовать в учетной записи Служб мультимедиа. Чтобы просмотреть список доступных подписок, перейдите на страницу [Подписки](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

```azurecli-interactive
az account set --subscription <mySubscriptionId>
```

## <a name="create-an-azure-resource-group"></a>Создание группы ресурсов Azure

С помощью следующей команды можно создать группу ресурсов, в которой должны быть учетная запись службы хранилища и Служб мультимедиа. Замените заполнитель *myresourcegroup* именем, которое будет использоваться для группы ресурсов.

```azurecli-interactive
az group create -n <myresourcegroup> -l westus2
```

## <a name="create-an-azure-storage-account"></a>Создание учетной записи хранения Azure

При создании учетной записи Служб мультимедиа необходимо предоставить идентификатор ресурса учетной записи хранения Azure. Указанная учетная запись хранения присоединена к учетной записи Служб мультимедиа. 

Необходимо иметь **основную** учетную запись хранения. У вас может быть любое количество **вторичных** учетных записей хранения, связанных с учетной записью Служб мультимедиа. Службы мультимедиа поддерживают учетные записи **общего назначения версии 2** или **общего назначения версии 1**. Учетные записи для больших двоичных объектов невозможно использовать в качестве **основных**. Дополнительные сведения об учетных записях хранения см. в статье [Общие сведения об учетной записи хранения](../../storage/common/storage-account-overview.md). 

Следующая команда создает учетную запись хранения, которая будет связана с учетной записью Служб мультимедиа (первичной). В приведенном ниже сценарии замените заполнитель *storageaccountforams*. Заполнитель account_name должен состоять максимум из 24 знаков.

```azurecli-interactive
az storage account create -n <storageaccountforams> -g <myresourcegroup>
```

## <a name="create-an-azure-media-services-account"></a>Создание учетной записи служб мультимедиа Azure

Далее представлены команды Azure CLI, с помощью которых можно создать учетную запись Служб мультимедиа. Вам необходимо только заменить следующие выделенные значения:

* *myamsaccountname*;
* *myresourcegroup*
* *storageaccountforams*.

```azurecli-interactive
az ams create -n <myamsaccountname> -g <myresourcegroup> --storage-account <storageaccountforams>
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вам больше не нужны ресурсы в группе ресурсов, включая учетную запись Служб мультимедиа, созданную в рамках этого краткого руководства, удалите группу ресурсов.

В **CloudShell** выполните следующую команду:

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Краткое руководство по потоковой передаче видеофайлов — .NET](stream-files-dotnet-quickstart.md)
