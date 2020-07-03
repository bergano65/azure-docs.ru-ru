---
title: Управление IoT Central из Azure CLI | Документация Майкрософт
description: В этой статье описывается создание приложения IoT Central и управление им с помощью интерфейса командной строки. Вы можете просматривать, изменять и удалять приложения с помощью интерфейса командной строки.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 03/27/2020
ms.topic: how-to
manager: philmea
ms.openlocfilehash: c3e0beb9a35b7888787d018b5e3b9c4ceea7e1c9
ms.sourcegitcommit: 3beb067d5dc3d8895971b1bc18304e004b8a19b3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82744538"
---
# <a name="manage-iot-central-from-azure-cli"></a>Управление IoT Central из Azure CLI

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

Вместо создания приложений IoT Central и управления ими на веб-сайте [диспетчера приложений Azure IOT Central](https://aka.ms/iotcentral) можно использовать [Azure CLI](/cli/azure/) для управления приложениями.

## <a name="prerequisites"></a>Предварительные требования

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Если вы предпочитаете запускать Azure CLI на локальном компьютере, см. статью [установка Azure CLI](/cli/azure/install-azure-cli). При запуске Azure CLI локально используйте команду **AZ login** для входа в Azure перед выполнением команд, описанных в этой статье.

> [!TIP]
> Если вам нужно выполнить команды интерфейса командной строки в другой подписке Azure, см. статью [Изменение активной подписки](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest#change-the-active-subscription).

## <a name="install-the-extension"></a>Установка расширения

Команды, приведенные в этой статье, входят в состав расширения **Azure-IOT** CLI. Выполните следующую команду, чтобы установить расширение:

```azurecli-interactive
az extension add --name azure-iot
```

## <a name="create-an-application"></a>Создание приложения

Создайте приложение IoT Central в подписке Azure с помощью команды [AZ IOT Central App Create](/cli/azure/iot/central/app?view=azure-cli-latest#az-iot-central-app-create) . Пример:

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
| location          | По умолчанию эта команда использует расположение из группы ресурсов. В настоящее время вы можете создать IoT Centralное приложение в **странах Австралии**, **Азиатско-Тихоокеанский регион**, **Европе**, США **, Великобритании**и **Японии** . **United States** |
| name              | Имя приложения на портале Azure. |
| поддомен         | Поддомен в URL-адресе приложения. В примере URL-адрес приложения — `https://mysubdomain.azureiotcentral.com`. |
| sku               | В настоящее время можно использовать либо **ST1** , либо **ST2**. Ознакомьтесь с разделом [Цены на Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). |
| шаблон          | Шаблон приложения для использования. Дополнительные сведения приведены в таблице ниже. |
| display-name      | Имя приложения, отображаемое на пользовательском интерфейсе. |

[!INCLUDE [iot-central-template-list](../../../includes/iot-central-template-list.md)]

## <a name="view-your-applications"></a>Просмотр приложений

Используйте команду [AZ IOT Central App List](/cli/azure/iot/central/app?view=azure-cli-latest#az-iot-central-app-list) , чтобы получить список приложений IOT Central и просматривать метаданные.

## <a name="modify-an-application"></a>Изменение приложения

Чтобы обновить метаданные IoT Central приложения, используйте команду [AZ IOT Центральная App Update](/cli/azure/iot/central/app?view=azure-cli-latest#az-iot-central-app-update) . Например, чтобы изменить отображаемое имя приложения:

```azurecli-interactive
az iot central app update --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup \
  --set displayName="My new display name"
```

## <a name="remove-an-application"></a>Удаление приложения

Удалите IoT Central приложение с помощью команды [AZ IOT центральное приложение Delete](/cli/azure/iot/central/app?view=azure-cli-latest#az-iot-central-app-delete) . Пример:

```azurecli-interactive
az iot central app delete --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup
```

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы узнали, как управлять приложениями IoT Central Azure из Azure CLI, предлагаем следующий шаг:

> [!div class="nextstepaction"]
> [Администрирование приложения](howto-administer.md)
