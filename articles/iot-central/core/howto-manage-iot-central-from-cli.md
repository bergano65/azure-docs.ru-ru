---
title: Управление IoT Central из Azure CLI | Документация Майкрософт
description: В этой статье описывается создание приложения IoT Central и управление им с помощью интерфейса командной строки. Вы можете просматривать, изменять и удалять приложения с помощью интерфейса командной строки.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 02/11/2020
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: c44b7cd045547d01d1a31f949a42087e78e88b21
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198843"
---
# <a name="manage-iot-central-from-azure-cli"></a>Управление IoT Central из Azure CLI

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

Вместо создания приложений IoT Central и управления ими на веб-сайте [диспетчера приложений Azure IOT Central](https://aka.ms/iotcentral) можно использовать [Azure CLI](/cli/azure/) для управления приложениями.

## <a name="prerequisites"></a>Предварительные требования

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Если вы предпочитаете запускать Azure CLI на локальном компьютере, см. статью [установка Azure CLI](/cli/azure/install-azure-cli). При запуске Azure CLI локально используйте команду **AZ login** для входа в Azure перед выполнением команд, описанных в этой статье.

> [!TIP]
> Если вам нужно выполнить команды интерфейса командной строки в другой подписке Azure, см. статью [Изменение активной подписки](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest#change-the-active-subscription).

## <a name="create-an-application"></a>Создание приложения

Используйте команду [AZ иотцентрал App Create](/cli/azure/iotcentral/app#az-iotcentral-app-create) , чтобы создать приложение IOT Central в подписке Azure. Например:

```azurecli-interactive
# Create a resource group for the IoT Central application
az group create --location "East US" \
    --name "MyIoTCentralResourceGroup"
```

```azurecli-interactive
# Create an IoT Central application
az iotcentral app create \
  --resource-group "MyIoTCentralResourceGroup" \
  --name "myiotcentralapp" --subdomain "mysubdomain" \
  --sku ST1 --template "iotc-pnp-preview@1.0.0" \
  --display-name "My Custom Display Name"
```

Сначала эти команды создают группу ресурсов в регионе "Восточная часть США" для приложения. В следующей таблице описаны параметры, используемые командой **AZ иотцентрал App Create** .

| Параметр         | Описание |
| ----------------- | ----------- |
| resource-group    | Группа ресурсов, в которой содержится приложение. В подписке уже должна существовать эта группа ресурсов. |
| расположение          | По умолчанию эта команда использует расположение из группы ресурсов. В настоящее время можно создать IoT Centralное приложение в **Австралии**, **Азиатско-Тихоокеанский регион**, **Европе**или **США** географических регионах. |
| name              | Имя приложения на портале Azure. |
| поддомен         | Поддомен в URL-адресе приложения. В примере URL-адрес приложения — https://mysubdomain.azureiotcentral.com. |
| sku               | В настоящее время можно использовать либо **ST1** , либо **ST2**. Ознакомьтесь с разделом [Цены на Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). |
| template (шаблон)          | Шаблон приложения для использования. Дополнительные сведения приведены в таблице ниже. |
| display-name      | Имя приложения, отображаемое на пользовательском интерфейсе. |

[!INCLUDE [iot-central-template-list](../../../includes/iot-central-template-list.md)]

## <a name="view-your-applications"></a>Просмотр приложений

Используйте команду [AZ иотцентрал App List](/cli/azure/iotcentral/app#az-iotcentral-app-list) , чтобы получить список приложений IOT Central и просматривать метаданные.

## <a name="modify-an-application"></a>Изменение приложения

Используйте команду [AZ иотцентрал App Update](/cli/azure/iotcentral/app#az-iotcentral-app-update) , чтобы обновить метаданные приложения IOT Central. Например, чтобы изменить отображаемое имя приложения:

```azurecli-interactive
az iotcentral app update --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup \
  --set displayName="My new display name"
```

## <a name="remove-an-application"></a>Удаление приложения

Чтобы удалить IoT Central приложение, используйте команду [AZ иотцентрал App Delete](/cli/azure/iotcentral/app#az-iotcentral-app-delete) . Например:

```azurecli-interactive
az iotcentral app delete --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup
```

## <a name="next-steps"></a>Следующие шаги

Теперь, когда вы узнали, как управлять приложениями IoT Central Azure из Azure CLI, предлагаем следующий шаг:

> [!div class="nextstepaction"]
> [Администрирование приложения](howto-administer.md)
