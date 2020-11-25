---
title: Настройка ресурсов IoT, необходимых для Plug and Play IoT | Документация Майкрософт
description: Создание центра Интернета вещей и экземпляра службы подготовки устройств для использования с краткими руководствами по Plug and Play Интернета вещей.
author: dominicbetts
ms.author: dobett
ms.date: 08/11/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 6e6c090c02798103a30cc68f2ca28e8c4f7f2f17
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/23/2020
ms.locfileid: "96004268"
---
# <a name="set-up-your-environment-for-the-iot-plug-and-play-quickstarts-and-tutorials"></a>Настройка среды для использования кратких руководств и учебников по Plug and Play IoT

Перед началом работы с краткими руководствами и учебниками по Plug and Play Интернета вещей необходимо настроить центр Интернета вещей и службу подготовки устройств (DPS) в подписке Azure. Кроме того, вам понадобятся локальные копии файлов модели, используемых в примерах приложений, и средство Azure IoT Explorer.

Если у вас нет подписки Azure, перед началом работы [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Чтобы не устанавливать Azure CLI локально, можно использовать Azure Cloud Shell для настройки облачных служб.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-the-resources"></a>Создание ресурсов

Создайте группу ресурсов Azure для ресурсов:

```azurecli-interactive
az group create --name my-pnp-resourcegroup --location centralus
```

Создайте Центр Интернета вещей. Следующая команда использует имя в `my-pnp-hub` качестве примера для имени создаваемого центра Интернета вещей. Выберите уникальное имя для центра Интернета вещей, которое будет использоваться вместо `my-pnp-hub` :

```azurecli-interactive
az iot hub create --name my-pnp-hub --resource-group my-pnp-resourcegroup --sku F1 --partition-count 2
```

Создайте экземпляр DPS. Следующая команда использует имя в `my-pnp-dps` качестве примера для имени создаваемого экземпляра DP. Выберите уникальное имя для экземпляра DP, которое будет использоваться вместо `my-pnp-dps` :

```azurecli-interactive
az iot dps create --name my-pnp-dps --resource-group my-pnp-resourcegroup
```

Чтобы связать экземпляр DPS с центром Интернета вещей, используйте следующие команды. Замените `my-pnp-dps` и `my-pnp-hub` уникальными именами, которые вы выбрали ранее:

```azurecli-interactive
hubConnectionString=$(az iot hub connection-string show -n my-pnp-hub --key primary --query connectionString -o tsv)
az iot dps linked-hub create --dps-name my-pnp-dps --resource-group my-pnp-resourcegroup --location centralus --connection-string $hubConnectionString
```

## <a name="retrieve-the-settings"></a>Получение параметров

В некоторых кратких руководствах и учебных курсах используется строка подключения для центра Интернета вещей. Кроме того, строка подключения необходима при настройке средства Azure IoT Explorer. Извлеките строку подключения и запишите ее сейчас. Замените `my-pnp-hub` уникальным именем, выбранным для центра Интернета вещей:

```azurecli-interactive
az iot hub connection-string show -n my-pnp-hub --key primary --query connectionString
```

В большинстве кратких руководств и учебников используется *область идентификаторов* конфигурации DPS. Получите область ИДЕНТИФИКАТОРов и запишите ее сейчас. Замените `my-pnp-dps` уникальным именем, выбранным для экземпляра DP:

```azurecli-interactive
az iot dps show --name my-pnp-dps --query properties.idScope
```

Все краткие руководства и учебники используют регистрацию устройств DPS. Используйте следующую команду, чтобы создать `my-pnp-device` *регистрацию отдельного устройства* в экземпляре DPS. Замените `my-pnp-dps` уникальным именем, выбранным для экземпляра DP. Запишите идентификатор регистрации и значения первичного ключа для использования в кратких руководствах и учебниках.

```azurecli-interactive
az iot dps enrollment create --attestation-type symmetrickey --dps-name my-pnp-dps --resource-group my-pnp-resourcegroup --enrollment-id my-pnp-device --device-id my-pnp-device --query '{registrationID:registrationId,primaryKey:attestation.symmetricKey.primaryKey}'
```

## <a name="create-environment-variables"></a>Создание переменных среды

Создайте пять переменных среды, чтобы настроить примеры в кратком руководстве и учебниках, чтобы использовать службу подготовки устройств (DPS) для подключения к центру Интернета вещей:

* **IOTHUB_DEVICE_SECURITY_TYPE**: значение `DPS` .
* **IOTHUB_DEVICE_DPS_ID_SCOPE**. область идентификатора DPS, которую вы записали ранее.
* **IOTHUB_DEVICE_DPS_DEVICE_ID**: значение `my-pnp-device` .
* **IOTHUB_DEVICE_DPS_DEVICE_KEY**. первичный ключ регистрации, который был сделан ранее.
* **IOTHUB_DEVICE_DPS_ENDPOINT**: значение `global.azure-devices-provisioning.net`

Для выборки концентратора и устройства для подключения к примерам службы требуются следующие переменные среды:

* **IOTHUB_CONNECTION_STRING**: строка подключения центра Интернета вещей, которую вы записали ранее.
* **IOTHUB_DEVICE_ID**: `my-pnp-device`.

Например, в оболочке командной системы Linux Bash:

```bash
export IOTHUB_DEVICE_SECURITY_TYPE="DPS"
export IOTHUB_DEVICE_DPS_ID_SCOPE="<Your ID scope>"
export IOTHUB_DEVICE_DPS_DEVICE_ID="my-pnp-device"
export IOTHUB_DEVICE_DPS_DEVICE_KEY="<Your enrolment primary key>"
export IOTHUB_DEVICE_DPS_ENDPOINT="global.azure-devices-provisioning.net"
export IOTHUB_CONNECTION_STRING="<Your IoT hub connection string>"
export IOTHUB_DEVICE_ID="my-pnp-device"
```

Например, в командной строке Windows:

```cmd
set IOTHUB_DEVICE_SECURITY_TYPE=DPS
set IOTHUB_DEVICE_DPS_ID_SCOPE=<Your ID scope>
set IOTHUB_DEVICE_DPS_DEVICE_ID=my-pnp-device
set IOTHUB_DEVICE_DPS_DEVICE_KEY=<Your enrolment primary key>
set IOTHUB_DEVICE_DPS_ENDPOINT=global.azure-devices-provisioning.net
set IOTHUB_CONNECTION_STRING=<Your IoT hub connection string>
set IOTHUB_DEVICE_ID=my-pnp-device
```

## <a name="download-the-model-files"></a>Скачивание файлов модели

В кратком руководстве и учебниках используются файлы образцов моделей для контроллера температуры и устройств термостата. Чтобы скачать образцы файлов модели, выполните следующие действия.

1. Создайте на локальном компьютере папку *models*.

1. Щелкните правой кнопкой мыши [TemperatureController.json](https://raw.githubusercontent.com/Azure/opendigitaltwins-dtdl/master/DTDL/v2/samples/TemperatureController.json) и сохраните этот JSON-файл в папку *models*.

1. Щелкните правой кнопкой мыши [Thermostat.json](https://raw.githubusercontent.com/Azure/opendigitaltwins-dtdl/master/DTDL/v2/samples/Thermostat.json) и сохраните этот JSON-файл в папку *models*.

## <a name="install-the-azure-iot-explorer"></a>Установка обозревателя Azure IoT

В кратком руководстве и учебниках используется средство **Azure IOT Explorer** . Перейдите в раздел [Azure IOT Explorer](https://github.com/Azure/azure-iot-explorer/releases) и разверните список ресурсов для последнего выпуска. Скачайте и установите последнюю версию приложения для операционной системы.

При первом запуске средства вам будет предложено ввести строку подключения для центра Интернета вещей. Используйте строку подключения, которая ранее была заметкой.

Настройте средство для использования файлов модели, загруженных ранее. На домашней странице средства выберите **параметры Plug and Play IOT**, а затем **+ Добавить > локальную папку**. Выберите созданную ранее папку *Models* . Затем нажмите кнопку **сохранить** , чтобы сохранить параметры.

Дополнительные сведения см. в статье [Установка и использование обозревателя Интернета вещей Azure](howto-use-iot-explorer.md).

## <a name="remove-the-resources"></a>Удаление ресурсов

Вы можете использовать центр Интернета вещей и экземпляр DP для всех кратких руководств по Plug and Play Интернета вещей, поэтому вам нужно только выполнить действия, описанные в этой статье. По завершении их можно удалить из подписки с помощью следующей команды:

```azurecli-interactive
az group delete --name my-pnp-resourcegroup
```

## <a name="next-steps"></a>Следующие шаги

Теперь, когда вы настроили среду, вы можете испытать одно из кратких руководств или учебников, таких как:

> [!div class="nextstepaction"]
> [Подключение примера приложения Plug and Play устройства IoT к центру Интернета вещей](quickstart-connect-device.md)
