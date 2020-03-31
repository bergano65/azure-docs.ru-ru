---
title: Управление IoT Центральной от Azure CLI (ru) Документы Майкрософт
description: В этой статье описывается, как создавать и управлять приложением IoT Central с помощью CLI. Вы можете просматривать, изменять и удалять приложение с помощью CLI.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 03/27/2020
ms.topic: how-to
manager: philmea
ms.openlocfilehash: df24a2dc6e9bd058a2f8b1355b8760653ed3128a
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365527"
---
# <a name="manage-iot-central-from-azure-cli"></a>Управление IoT Central от Azure CLI

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

Вместо создания и управления приложениями IoT Central на [веб-сайте менеджера приложений Azure IoT,](https://aka.ms/iotcentral) вы можете использовать [Azure CLI](/cli/azure/) для управления приложениями.

## <a name="prerequisites"></a>Предварительные требования

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Если вы предпочитаете запускать Azure CLI на локальной машине, [см.](/cli/azure/install-azure-cli) При локальном запуске Azure CLI используйте команду **входа в Az,** чтобы войти в Azure, прежде чем пытаться использовать команды в этой статье.

> [!TIP]
> Если вам нужно запустить команды CLI в другой подписке Azure, [см.](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest#change-the-active-subscription)

## <a name="install-the-extension"></a>Установка расширения

Команды в этой статье являются частью расширения **CLI лазурного ят.** Выполнить следующую команду для установки расширения:

```azurecli-interactive
az extension add --name azure-iot
```

## <a name="create-an-application"></a>Создание приложения

Используйте [команду создания приложения az iotcentral](/cli/azure/iotcentral/app#az-iotcentral-app-create) для создания центрального приложения IoT в подписке Azure. Пример:

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

Эти команды сначала создают группу ресурсов в восточном регионе США для приложения. В следующей таблице описаны параметры, используемые с помощью команды приложения **az iotcentral:**

| Параметр         | Описание |
| ----------------- | ----------- |
| resource-group    | Группа ресурсов, в которой содержится приложение. В подписке уже должна существовать эта группа ресурсов. |
| location          | По умолчанию эта команда использует местоположение из группы ресурсов. В настоящее время вы можете создать ioT Центральное приложение в **Австралии,** **Азиатско-Тихоокеанском регионе,** **Европе,** **США,** **Великобритании**и **Японии.** |
| name              | Имя приложения на портале Azure. |
| Поддомен         | Поддомен в URL-адресе приложения. В примере URL-адрес приложения — `https://mysubdomain.azureiotcentral.com`. |
| sku               | В настоящее время вы можете использовать либо **ST1** или **ST2**. Ознакомьтесь с разделом [Цены на Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). |
| шаблон          | Шаблон приложения для использования. Дополнительные сведения приведены в таблице ниже. |
| display-name      | Имя приложения, отображаемое на пользовательском интерфейсе. |

[!INCLUDE [iot-central-template-list](../../../includes/iot-central-template-list.md)]

## <a name="view-your-applications"></a>Просмотр приложений

Используйте команду [списка приложений az iotcentral,](/cli/azure/iotcentral/app#az-iotcentral-app-list) чтобы перечислить ваши приложения IoT Central и просматривать метаданные.

## <a name="modify-an-application"></a>Изменение приложения

Используйте команду [обновления приложения az iotcentral](/cli/azure/iotcentral/app#az-iotcentral-app-update) для обновления метаданных центрального приложения IoT. Например, чтобы изменить отображаемое имя приложения:

```azurecli-interactive
az iotcentral app update --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup \
  --set displayName="My new display name"
```

## <a name="remove-an-application"></a>Удаление приложения

Используйте команду [удаления приложения az iotcentral,](/cli/azure/iotcentral/app#az-iotcentral-app-delete) чтобы удалить приложение IoT Central. Пример:

```azurecli-interactive
az iotcentral app delete --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup
```

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы узнали, как управлять приложениями Azure IoT Central из Azure CLI, вот следующий шаг:

> [!div class="nextstepaction"]
> [Администрирование приложения](howto-administer.md)
