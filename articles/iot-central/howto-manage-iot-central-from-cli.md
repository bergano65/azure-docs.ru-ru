---
title: Управление из командной строки Azure IoT Central | Документация Майкрософт
description: Управление центр Интернета вещей с помощью Azure CLI.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 02/07/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 9e5d842cece316bc9c53e1e8583f40a0f222b91d
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/25/2019
ms.locfileid: "56809400"
---
# <a name="manage-iot-central-from-azure-cli"></a>Управление из командной строки Azure IoT Central

[!INCLUDE [iot-central-selector-manage](../../includes/iot-central-selector-manage.md)]

Вместо создания и управления приложениями IoT Central с IoT Central [Диспетчер приложений](https://aka.ms/iotcentral) странице, можно использовать [Azure CLI](/cli/azure/) для управления приложениями.

## <a name="prerequisites"></a>Технические условия

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если вы хотите запустить Azure CLI на локальном компьютере, см. в разделе [установите Azure CLI](/cli/azure/install-azure-cli). При запуске Azure CLI локально, используйте **az login** команду, чтобы войти в Azure, прежде чем использовать команды в этой статье.

## <a name="create-an-application"></a>Создание приложения

Используйте [создать приложение iotcentral az](/cli/azure/iotcentral/app#az-iotcentral-app-create) команду, чтобы создать приложение центра Интернета вещей в подписке Azure. Например: 

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

Эти команды, сначала создайте группу ресурсов в восточной части США для приложения. В следующей таблице описаны параметры, используемые в **создать приложение iotcentral az** команды:

| Параметр         | ОПИСАНИЕ |
| ----------------- | ----------- |
| resource-group    | Группа ресурсов, в которой содержится приложение. В подписке уже должна существовать эта группа ресурсов. |
| location          | По умолчанию эта команда использует расположение, из группы ресурсов. Сейчас можно создавать приложение IoT Central в регионах **восточной части США**, **западной части США**, **Северной Европы** или **Западной Европы**. |
| name              | Имя приложения на портале Azure. |
| поддомен         | Поддомен в URL-адресе приложения. В примере URL-адрес приложения — https://mysubdomain.azureiotcentral.com. |
| sku               | В настоящее время единственное значение — **S1** (уровень "Стандартный"). Ознакомьтесь с разделом [Цены на Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). |
| шаблон          | Шаблон приложения для использования. Дополнительные сведения приведены в таблице ниже. |
| отображаемое имя      | Имя приложения, отображаемое на пользовательском интерфейсе. |

**Шаблоны приложения**

| Имя шаблона            | ОПИСАНИЕ |
| ------------------------ | ----------- |
| iotc-default@1.0.0       | Создает пустое приложение, в которое необходимо добавить собственные шаблоны устройств и сами устройства. |
| iotc-demo@1.0.0          | Создает приложение, содержащее шаблон устройства, созданный для охлаждаемого торгового автомата. Используйте этот шаблон, чтобы приступить к работе в Azure IoT Central. |
| iotc-devkit-sample@1.0.0 | Создает приложение с готовыми шаблонами устройств, чтобы вы могли подключить устройство MXChip или Raspberry Pi. Используйте этот шаблон, если вы являетесь разработчиком устройства, работающим с одним из этих устройств. |

## <a name="view-your-applications"></a>Просмотр приложений

Используйте [список приложений iotcentral az](/cli/azure/iotcentral/app#az-iotcentral-app-list) команды для списка приложений IoT Central и просмотра метаданных.

## <a name="modify-an-application"></a>Изменение приложения

Используйте [обновления приложения iotcentral az](/cli/azure/iotcentral/app#az-iotcentral-app-update) команду, чтобы обновить метаданные приложения IoT Central. Например, чтобы изменить отображаемое имя приложения:

```azurecli-interactive
az iotcentral app update --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup \
  --set displayName="My new display name"
```

## <a name="remove-an-application"></a>Удаление приложения

Используйте [az iotcentral app delete](/cli/azure/iotcentral/app#az-iotcentral-app-delete) команду, чтобы удалить приложение IoT Central. Например: 

```azurecli-interactive
az iotcentral app delete --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup
```

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы узнали, как управлять приложений центра Интернета вещей Azure с помощью Azure CLI, вот мы предлагаем:

> [!div class="nextstepaction"]
> [Администрирование приложения](howto-administer.md)
