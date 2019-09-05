---
title: Управление IoT Central из Azure CLI | Документация Майкрософт
description: Управление IoT Central из Azure CLI.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 421d41f95eff0ba0fdbca02c588d4a9a0c461a84
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/05/2019
ms.locfileid: "70381062"
---
# <a name="manage-iot-central-from-azure-cli"></a>Управление IoT Central из Azure CLI

[!INCLUDE [iot-central-selector-manage](../../includes/iot-central-selector-manage.md)]

Вместо создания приложений IoT Central и управления ими на веб-сайте [диспетчера приложений Azure IOT Central](https://aka.ms/iotcentral) можно использовать [Azure CLI](/cli/azure/) для управления приложениями.

## <a name="prerequisites"></a>Предварительные требования

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если вы предпочитаете запускать Azure CLI на локальном компьютере, см. статью [установка Azure CLI](/cli/azure/install-azure-cli). При запуске Azure CLI локально используйте команду **AZ login** для входа в Azure перед выполнением команд, описанных в этой статье.

## <a name="create-an-application"></a>Создание приложения

Используйте команду [AZ иотцентрал App Create](/cli/azure/iotcentral/app#az-iotcentral-app-create) , чтобы создать приложение IOT Central в подписке Azure. Пример:

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
  --sku S1 --template "iotc-demo@1.0.0" \
  --display-name "My Custom Display Name"
```

Сначала эти команды создают группу ресурсов в регионе "Восточная часть США" для приложения. В следующей таблице описаны параметры, используемые командой **AZ иотцентрал App Create** .

| Параметр         | Описание |
| ----------------- | ----------- |
| resource-group    | Группа ресурсов, в которой содержится приложение. В подписке уже должна существовать эта группа ресурсов. |
| расположение          | По умолчанию эта команда использует расположение из группы ресурсов. Сейчас можно создавать приложение IoT Central в регионах **восточной части США**, **западной части США**, **Северной Европы** или **Западной Европы**. |
| name              | Имя приложения на портале Azure. |
| поддомен         | Поддомен в URL-адресе приложения. В примере URL-адрес приложения — https://mysubdomain.azureiotcentral.com. |
| sku               | В настоящее время единственное значение — **S1** (уровень "Стандартный"). Ознакомьтесь с разделом [Цены на Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). |
| шаблон          | Шаблон приложения для использования. Дополнительные сведения приведены в таблице ниже. |
| display-name      | Имя приложения, отображаемое на пользовательском интерфейсе. |

**Шаблоны приложения**

| Имя шаблона            | Описание |
| ------------------------ | ----------- |
| iotc-default@1.0.0       | Создает пустое приложение, в которое необходимо добавить собственные шаблоны устройств и сами устройства. |
| iotc-demo@1.0.0          | Создает приложение, содержащее шаблон устройства, созданный для охлаждаемого торгового автомата. Используйте этот шаблон, чтобы приступить к работе в Azure IoT Central. |
| iotc-devkit-sample@1.0.0 | Создает приложение с готовыми шаблонами устройств, чтобы вы могли подключить устройство MXChip или Raspberry Pi. Используйте этот шаблон, если вы являетесь разработчиком устройства, работающим с одним из этих устройств. |

> [!NOTE]
> В настоящее время шаблон **предварительной версии приложения** доступен только в **Северной Европе** и в **центральных регионах США** .

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

Чтобы удалить IoT Central приложение, используйте команду [AZ иотцентрал App Delete](/cli/azure/iotcentral/app#az-iotcentral-app-delete) . Пример:

```azurecli-interactive
az iotcentral app delete --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup
```

## <a name="next-steps"></a>Следующие шаги

Теперь, когда вы узнали, как управлять приложениями IoT Central Azure из Azure CLI, предлагаем следующий шаг:

> [!div class="nextstepaction"]
> [Администрирование приложения](howto-administer.md)
