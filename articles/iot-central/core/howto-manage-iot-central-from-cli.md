---
title: Управление IoT Central из Azure CLI | Документация Майкрософт
description: В этой статье описывается создание приложения IoT Central и управление им с помощью интерфейса командной строки. Вы можете просматривать, изменять и удалять приложения с помощью интерфейса командной строки.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 03/27/2020
ms.topic: how-to
ms.custom: devx-track-azurecli
manager: philmea
ms.openlocfilehash: 6496e944d30724fe9e8db7168f9c9cb1552dcd1b
ms.sourcegitcommit: aeba98c7b85ad435b631d40cbe1f9419727d5884
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2021
ms.locfileid: "98203340"
---
# <a name="manage-iot-central-from-azure-cli"></a>Управление IoT Central из Azure CLI

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

Вместо создания приложений IoT Central и управления ими на веб-сайте [диспетчера приложений Azure IOT Central](https://aka.ms/iotcentral) можно использовать [Azure CLI](/cli/azure/) для управления приложениями.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Если вам нужно выполнить команды интерфейса командной строки в другой подписке Azure, см. статью [Изменение активной подписки](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest#change-the-active-subscription&preserve-view=true).

## <a name="create-an-application"></a>Создание приложения

[!INCLUDE [Warning About Access Required](../../../includes/iot-central-warning-contribitorrequireaccess.md)]

Создайте приложение IoT Central в подписке Azure с помощью команды [AZ IOT Central App Create](/cli/azure/iot/central/app?view=azure-cli-latest#az-iot-central-app-create&preserve-view=true) . Пример:

```azurecli-interactive
# Create a resource group for the IoT Central application
az group create --location "East US" \
    --name "MyIoTCentralResourceGroup"
```

```azurecli-interactive
# Create an IoT Central application
az iot central app create \
  --resource-group "MyIoTCentralResourceGroup" \
  --name "myiotcentralapp" --subdomain "mysubdomain" \
  --sku ST1 --template "iotc-pnp-preview" \
  --display-name "My Custom Display Name"
```

Сначала эти команды создают группу ресурсов в регионе "Восточная часть США" для приложения. В следующей таблице описаны параметры, используемые с командой **AZ IOT центральное приложение Create** .

| Параметр         | Описание |
| ----------------- | ----------- |
| resource-group    | Группа ресурсов, в которой содержится приложение. В подписке уже должна существовать эта группа ресурсов. |
| location          | По умолчанию эта команда использует расположение из группы ресурсов. В настоящее время вы можете создать IoT Centralное приложение в **странах Австралии**, **Азиатско-Тихоокеанский регион**, **Европе**, США **, Великобритании** и **Японии** .  |
| name              | Имя приложения на портале Azure. |
| поддомен         | Поддомен в URL-адресе приложения. В примере URL-адрес приложения — `https://mysubdomain.azureiotcentral.com`. |
| sku               | В настоящее время можно использовать либо **ST1** , либо **ST2**. Ознакомьтесь с разделом [Цены на Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). |
| шаблон          | Шаблон приложения для использования. Дополнительные сведения приведены в таблице ниже. |
| display-name      | Имя приложения, отображаемое на пользовательском интерфейсе. |

[!INCLUDE [iot-central-template-list](../../../includes/iot-central-template-list.md)]

## <a name="view-your-applications"></a>Просмотр приложений

Используйте команду [AZ IOT Central App List](/cli/azure/iot/central/app?view=azure-cli-latest#az-iot-central-app-list&preserve-view=true) , чтобы получить список приложений IOT Central и просматривать метаданные.

## <a name="modify-an-application"></a>Изменение приложения

Чтобы обновить метаданные IoT Central приложения, используйте команду [AZ IOT Центральная App Update](/cli/azure/iot/central/app?view=azure-cli-latest#az-iot-central-app-update&preserve-view=true) . Например, чтобы изменить отображаемое имя приложения:

```azurecli-interactive
az iot central app update --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup \
  --set displayName="My new display name"
```

## <a name="remove-an-application"></a>Удаление приложения

Удалите IoT Central приложение с помощью команды [AZ IOT центральное приложение Delete](/cli/azure/iot/central/app?view=azure-cli-latest#az-iot-central-app-delete&preserve-view=true) . Пример:

```azurecli-interactive
az iot central app delete --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup
```

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы узнали, как управлять приложениями IoT Central Azure из Azure CLI, предлагаем следующий шаг:

> [!div class="nextstepaction"]
> [Администрирование приложения](howto-administer.md)
