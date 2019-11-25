---
title: Автономная работа устройств — Azure IoT Edge | Документация Майкрософт
description: Узнайте, как устройства и модули IoT Edge могут работать в течение долгого периода времени без подключения к Интернету и как с помощью IoT Edge можно обеспечить автономную работу обычных устройств Интернета вещей.
author: kgremban
ms.author: kgremban
ms.date: 08/04/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: b16a8d8ddd4ac23a59db8e7fed48f1c39752d130
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456890"
---
# <a name="understand-extended-offline-capabilities-for-iot-edge-devices-modules-and-child-devices"></a>Understand extended offline capabilities for IoT Edge devices, modules, and child devices

Azure IoT Edge supports extended offline operations on your IoT Edge devices, and enables offline operations on non-IoT Edge child devices too. Пока у устройства IoT Edge есть возможность подключиться к центру Интернета вещей, оно и его дочерние устройства могут продолжать работу с нестабильным подключением к Интернету или даже без него. 


## <a name="how-it-works"></a>Принципы работы

Когда устройство IoT Edge переходит в автономный режим, центр IoT Edge выполняет три роли. Во-первых, он хранит все сообщения, которые необходимо отправить, до тех пор, пока соединение с устройством не будет восстановлено. Во-вторых, он выступает от имени центра Интернета вещей и проверяет подлинность модулей и дочерних устройств, чтобы они продолжали работу. В-третьих, он обеспечивает связь между дочерними устройствами, которая обычно проходит через центр Интернета вещей. 

В следующем примере показано, как IoT Edge работает в автономном режиме:

1. **Configure devices**

   Функция автономной работы включена на устройствах IoT Edge автоматически. Чтобы дать эту возможность другим устройствам Интернета вещей, необходимо объявить отношение "родитель-дочерний элемент" между устройствами в центре Интернета вещей. Then, you configure the child devices to trust their assigned parent device and route the device-to-cloud communications through the parent as a gateway. 

2. **Sync with IoT Hub**

   Хотя бы один раз после установки среды выполнения IoT Edge необходимо подключить устройство IoT Edge к сети для синхронизации с центром Интернета вещей. Во время синхронизации устройство IoT Edge получает сведения о назначенных ему дочерних устройствах. Устройство IoT Edge безопасно обновляет свой локальный кэш, чтобы можно было продолжать работу автономно, и извлекает параметры для локального хранения сообщений телеметрии. 

3. **Go offline**

   При отключении от центра Интернета вещей устройств IoT Edge его развернутые модули и любые дочерние устройства Интернета вещей могут работать бесконечно. Модули и дочерние устройства могут запускаться и перезапускаться с прохождением аутентификации в центре IoT Edge в автономном режиме. Восходящий поток телеметрии в центр Интернета вещей хранится локально. Обмен данными между модулями или дочерними устройствами Интернета вещей поддерживается через прямые методы или сообщения. 

4. **Reconnect and resync with IoT Hub**

   После восстановления связи с центром Интернета вещей устройство IoT Edge будет снова синхронизировано. Локально хранимые сообщения доставляются в том же порядке, в котором они были сохранены. Все различия между требуемыми и сообщаемыми свойствами модулей и устройств будут согласованы. Устройство IoT Edge передает все изменения в свои назначенные дочерние устройства.

## <a name="restrictions-and-limits"></a>Ограничения

The extended offline capabilities described in this article are available in [IoT Edge version 1.0.7 or higher](https://github.com/Azure/azure-iotedge/releases). Более ранние версии имеют подмножество автономных функций. Существующие устройства IoT Edge без расширенных возможностей автономной работы невозможно обновить, изменив версию среды выполнения. Необходимо изменить их конфигурацию с использованием нового удостоверения устройства IoT Edge, чтобы использовать эти функции. 

Расширенная поддержка автономной работы доступна во всех регионах, где есть Центр Интернета вещей, **за исключением** региона "Восточная часть США".

Only non-IoT Edge devices can be added as child devices. 

IoT Edge devices and their assigned child devices can function indefinitely offline after the initial, one-time sync. However, storage of messages depends on the time to live (TTL) setting and the available disk space for storing the messages. 

## <a name="set-up-parent-and-child-devices"></a>Set up parent and child devices

For an IoT Edge device to extend its extended offline capabilities to child IoT devices, you need to complete two steps. First, declare the parent-child relationships in the Azure portal. Second, create a trust relationship between the parent device and any child devices, then configure device-to-cloud communications to go through the parent as a gateway. 

### <a name="assign-child-devices"></a>Назначение дочерних устройств

Child devices can be any non-IoT Edge device registered to the same IoT Hub. Parent devices can have multiple child devices, but a child device only has one parent. There are three options to set child devices to an edge device: through the Azure portal, using the Azure CLI, or using the IoT Hub service SDK. 

The following sections provide examples of how you can declare the parent/child relationship in IoT Hub for existing IoT devices. If you're creating new device identities for your child devices, see [Authenticate a downstream device to Azure IoT Hub](how-to-authenticate-downstream-device.md) for more information.

#### <a name="option-1-iot-hub-portal"></a>Option 1: IoT Hub Portal

You can declare the parent-child relationship when creating a new device. Or for existing devices, you can declare the relationship from the device details page of either the parent IoT Edge device or the child IoT device. 

   ![Управление дочерними устройствами на странице сведений об устройстве IoT Edge](./media/offline-capabilities/manage-child-devices.png)


#### <a name="option-2-use-the-az-command-line-tool"></a>Option 2: Use the `az` command-line tool

Using the [Azure command-line interface](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) with [IoT extension](https://github.com/azure/azure-iot-cli-extension) (v0.7.0 or newer), you can manage parent child relationships with the [device-identity](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) subcommands. The example below uses a query to assign all non-IoT Edge devices in the hub to be child devices of an IoT Edge device. 

```shell
# Set IoT Edge parent device
egde_device="edge-device1"

# Get All IoT Devices
device_list=$(az iot hub query \
        --hub-name replace-with-hub-name \
        --subscription replace-with-sub-name \
        --resource-group replace-with-rg-name \
        -q "SELECT * FROM devices WHERE capabilities.iotEdge = false" \
        --query 'join(`, `, [].deviceId)' -o tsv)

# Add all IoT devices to IoT Edge (as child)
az iot hub device-identity add-children \
  --device-id $egde_device \
  --child-list $device_list \
  --hub-name replace-with-hub-name \
  --resource-group replace-with-rg-name \
  --subscription replace-with-sub-name 
```

You can modify the [query](../iot-hub/iot-hub-devguide-query-language.md) to select a different subset of devices. The command may take several seconds if you specify a large set of devices.

#### <a name="option-3-use-iot-hub-service-sdk"></a>Option 3: Use IoT Hub Service SDK 

Finally, you can manage parent child relationships programmatically using either C#, Java or Node.js IoT Hub Service SDK. Here is an [example of assigning a child device](https://aka.ms/set-child-iot-device-c-sharp) using the C# SDK.

### <a name="set-up-the-parent-device-as-a-gateway"></a>Set up the parent device as a gateway

You can think of a parent/child relationship as a transparent gateway, where the child device has its own identity in IoT Hub but communicates through the cloud via its parent. For secure communication, the child device needs to be able to verify that the parent device comes from a trusted source. Otherwise, third-parties could set up malicious devices to impersonate parents and intercept communications. 

One way to create this trust relationship is described in detail in the following articles: 
* [Настройка устройства IoT Edge в качестве прозрачного шлюза](how-to-create-transparent-gateway.md).
* [Connect a downstream (child) device to an Azure IoT Edge gateway](how-to-connect-downstream-device.md)

## <a name="specify-dns-servers"></a>Указание DNS-серверов 

To improve robustness, it is highly recommended you specify the DNS server addresses used in your environment. To set your DNS server for IoT Edge, see the resolution for [Edge Agent module continually reports 'empty config file' and no modules start on device](troubleshoot.md#edge-agent-module-continually-reports-empty-config-file-and-no-modules-start-on-the-device) in the troubleshooting article.

## <a name="optional-offline-settings"></a>Дополнительные параметры автономной работы

If your devices go offline, the IoT Edge parent device stores all device-to-cloud messages until the connection is reestablished. The IoT Edge hub module manages the storage and forwarding of offline messages. For devices that may go offline for extended periods of time, optimize performance by configuring two IoT Edge hub settings. 

First, increase the time to live setting so that the IoT Edge hub will keep messages long enough for your device to reconnect. Затем добавьте дополнительное место на диске для хранения сообщений. 

### <a name="time-to-live"></a>Срок жизни

Срок жизни — это период времени (в секундах), в течение которого сообщение ожидает доставки, прежде чем срок его действия истечет. По умолчанию это 7200 секунд (два часа). The maximum value is only limited by the maximum value of an integer variable, which is around 2 billion. 

Этот параметр является требуемым свойством центра IoT Edge, которое хранится в двойнике модуля. You can configure it in the Azure portal or directly in the deployment manifest. 

```json
"$edgeHub": {
    "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {},
        "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
        }
    }
}
```

### <a name="host-storage-for-system-modules"></a>Host storage for system modules

Messages and module state information are stored in the IoT Edge hub's local container filesystem by default. For improved reliability, especially when operating offline, you can also dedicate storage on the host IoT Edge device. For more information, see [Give modules access to a device's local storage](how-to-access-host-storage-from-module.md)

## <a name="next-steps"></a>Дальнейшие действия

Learn more about how to set up a transparent gateway for your parent/child device connections: 

* [Настройка устройства IoT Edge в качестве прозрачного шлюза](how-to-create-transparent-gateway.md).
* [Аутентификация подчиненного устройства в Центре Интернета вещей](how-to-authenticate-downstream-device.md)
* [Connect a downstream device to an Azure IoT Edge gateway](how-to-connect-downstream-device.md) (Подключение подчиненного устройства к шлюзу Azure IoT Edge)
