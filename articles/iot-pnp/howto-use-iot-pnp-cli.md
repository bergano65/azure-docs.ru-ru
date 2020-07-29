---
title: Использование расширения Интернета вещей Azure для Azure CLI для взаимодействия с устройствами IoT Plug and Play (предварительная версия) | Документация Майкрософт
description: Установите расширение Интернета вещей Azure для Azure CLI и используйте его для взаимодействия с устройствами IoT Plug and Play, подключенными к центру Интернета вещей.
author: dominicbetts
ms.author: dobett
ms.date: 07/20/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 3699213fe61c64d7677ba026a8df54ccbbfe4b33
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87352361"
---
# <a name="install-and-use-the-azure-iot-extension-for-the-azure-cli"></a>Установка и использование расширения Интернета вещей Azure для Azure CLI

[Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) — это кроссплатформенная программа командной строки с открытым кодом для управления ресурсами Azure (например, центр Интернета вещей). Интерфейс Azure CLI доступен для операционных систем Windows, Linux и MacOS. Azure CLI также предварительно установлен в [Azure Cloud Shell](https://shell.azure.com). Azure CLI позволяет управлять ресурсами центра Интернета вещей Azure, экземплярами службы подготовки устройств и связанными центрами без установки каких-либо расширений.

Расширение Интернета вещей Azure для Azure CLI — это программа командной строки для взаимодействия с устройствами IoT Plug and Play (предварительная версия) и их тестирования. Расширение можно использовать для выполнения следующих задач:

- подключение к устройству;
- просмотр данных телеметрии с устройства;
- работа со свойствами устройства;
- вызов команд устройства.

В этой статье показано, как выполнить следующие действия:

- Установка и настройка расширения Интернета вещей Azure для Azure CLI.
- Использование расширения для взаимодействия с устройствами и их тестирования.

## <a name="install-azure-iot-extension-for-the-azure-cli"></a>Установка расширения Интернета вещей Azure для Azure CLI

### <a name="step-1---install-the-azure-cli"></a>Шаг 1. Установка Azure CLI

Следуйте [инструкциям по установке](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest), чтобы настроить Azure CLI в своей среде. Для оптимальной работы Azure CLI версия должна быть версии 2.9.1 или выше. Для проверки используйте `az -–version`.

### <a name="step-2---install-iot-extension"></a>Шаг 2. Установка расширения Интернета вещей

В [файле сведений расширения Интернета вещей](https://github.com/Azure/azure-iot-cli-extension) описывается несколько способов установки расширения. Проще всего запустить `az extension add --name azure-iot`. После установки можно использовать `az extension list`, чтобы проверить установленные расширения, или `az extension show --name azure-iot` для просмотра сведений о расширении Интернета вещей. На момент написания статьи номер версии расширения имеет значение `0.9.7` .

Чтобы удалить расширение, можно использовать `az extension remove --name azure-iot`.

## <a name="use-azure-iot-extension-for-the-azure-cli"></a>Использование расширения Интернета вещей Azure для Azure CLI

### <a name="prerequisites"></a>Предварительные требования

Выполните следующую команду, чтобы войти в свою подписку Azure.

```azurecli
az login
```

> [!NOTE]
> Если вы используете Azure Cloud Shell, вход выполняется автоматически и вам не нужно выполнять предыдущую команду.

Чтобы использовать расширение Интернета вещей Azure для Azure CLI, вам потребуется следующее:

- Центр интернета вещей Azure. Существует множество способов добавления центра Интернета вещей в подписку Azure, например [создание центра Интернета вещей с помощью Azure CLI](../iot-hub/iot-hub-create-using-cli.md). Для выполнения команд расширения Интернета вещей Azure требуется строка подключения центра Интернета вещей. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

- Устройство, зарегистрированное в центре Интернета вещей. Для регистрации устройства можно использовать следующую команду Azure CLI (не забудьте заменить заполнители `{YourIoTHubName}` и `{YourDeviceID}` своими значениями):

    ```azurecli
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id {YourDeviceID}
    ```

### <a name="interact-with-a-device"></a>Взаимодействие с устройством

С помощью расширения можно просматривать устройства IoT Plug and Play, подключенные к центру Интернета вещей, и взаимодействовать с ними. Расширение работает с цифровым двойником, представляющим устройство IoT Plug and Play.

#### <a name="list-devices"></a>Выведите список устройств.

Вывод списка всех устройств в центре Интернета вещей:

```azurecli
az iot hub device-identity list --hub-name {YourIoTHubName}
```

#### <a name="view-digital-twin"></a>Просмотр цифровых двойника

Просмотр цифрового двойникаа устройства:

```azurecli
az iot pnp twin show -n {iothub_name} -d {device_id}
```

#### <a name="properties"></a>Свойства

Задание значения свойства для чтения и записи:

```azurecli
az iot pnp twin update --hub-name {iothub_name} --device-id {device_id} --json-patch '{"op":"add", "path":"/thermostat1/targetTemperature", "value": 54}'
```

#### <a name="commands"></a>Команды

Вызов команды:

```azurecli
az iot pnp twin invoke-command --cn getMaxMinReport -n {iothub_name} -d {device_id} --component-path thermostat1
```

#### <a name="digital-twin-events"></a>события цифрового двойника

Мониторинг всех событий цифрового двойника IoT Plug and Play с определенного устройства и интерфейса, который переходит в группу потребителей концентратора событий **$Default**:

```azurecli
az iot hub monitor-events -n {iothub_name} -d {device_id} -i {interface_id}
```

## <a name="next-steps"></a>Дальнейшие действия

В этом пошаговом руководстве вы узнали, как установить и использовать расширение Интернета вещей Azure для Azure CLI взаимодействия с устройствами Plug and Play. Предлагаемый следующий шаг — Узнайте, как использовать [Azure IOT Explorer с вашими устройствами](./howto-use-iot-explorer.md).
