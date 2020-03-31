---
title: Используйте расширение Azure IoT для Azure CLI для взаимодействия с устройствами IoT Plug и Play Preview Документы Майкрософт
description: Установите расширение Azure IoT для Azure CLI и используйте его для взаимодействия с устройствами IoT Plug и Play, подключенными к моему концентратору IoT.
author: ChrisGMsft
ms.author: chrisgre
ms.date: 12/26/2019
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: b5907c0fb127947e90352e68b2726a22f5afea0d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80234685"
---
# <a name="install-and-use-the-azure-iot-extension-for-the-azure-cli"></a>Установка и использование расширения Azure IoT для Azure CLI

[Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) — это кросс-платформенный командный инструмент с открытым исходным кодом для управления ресурсами Azure, такими как IoT Hub. CLI Azure доступен на Windows, Linux и MacOS. Azure CLI также предварительно установлен в [облачной оболочке Azure.](https://shell.azure.com) Azure CLI позволяет управлять ресурсами Azure IoT Hub, экземплярами службы обеспечения устройств и связанными концентраторами без установки расширений.

Расширение Azure IoT для Azure CLI является командным инструментом для взаимодействия и тестирования устройств IoT Plug и Play Preview. Расширение можно использовать для:

- Подключите к устройству.
- Просмотр телеметрии, отправленной устройством.
- Работайте со свойствами устройств.
- Вызов команды устройства.

В этой статье показано, как выполнить следующие действия:

- Установите и настройте расширение Azure IoT для Azure CLI.
- Используйте расширение для взаимодействия с устройствами и тестирования.
- Используйте расширение для управления интерфейсами в репозитории модели.

## <a name="install-azure-iot-extension-for-the-azure-cli"></a>Установка расширения Azure IoT для Azure CLI

### <a name="step-1---install-the-azure-cli"></a>Шаг 1 - Установка Azure CLI

Следуйте [инструкциям по установке](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) для настройки ClI Azure в вашей среде. Чтобы использовать все приведенные ниже команды, версия Azure CLI должна быть версией 2.0.73 или выше. Для проверки используйте `az -–version`.

### <a name="step-2---install-iot-extension"></a>Шаг 2 - Установка расширения IoT

В [файле сведений расширения Интернета вещей](https://github.com/Azure/azure-iot-cli-extension) описывается несколько способов установки расширения. Проще всего запустить `az extension add --name azure-iot`. После установки можно использовать `az extension list`, чтобы проверить установленные расширения, или `az extension show --name azure-iot` для просмотра сведений о расширении Интернета вещей. Чтобы удалить расширение, можно использовать `az extension remove --name azure-iot`.

## <a name="use-azure-iot-extension-for-the-azure-cli"></a>Используйте расширение Azure IoT для Azure CLI

### <a name="prerequisites"></a>Предварительные требования

Чтобы войти в подписку Azure, запустите следующую команду:

```azurecli
az login
```

> [!NOTE]
> Если вы используете облачную оболочку Azure, вам автоматически входят в систему, и вам не нужно запускать предыдущую команду.

Для использования расширения Azure IoT для Azure CLI необходимо:

- Центр интернета вещей Azure. Существует множество способов добавления концентратора IoT в подписку Azure, [например, создать концентратор IoT с помощью Azure CLI.](../iot-hub/iot-hub-create-using-cli.md) Для выполнения команд расширения расширения Azure IoT нужна строка соединения Концентратора IoT. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

- Устройство, зарегистрированное в вашем концентраторе IoT. Вы можете использовать следующую команду Azure CLI для регистрации устройства, не забудьте заменить `{YourIoTHubName}` и `{YourDeviceID}` заполнителей с вашими значениями:

    ```azurecli
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id {YourDeviceID}
    ```

- Некоторым командам нужна строка соединения для репозитория модели компании. Репозиторий модели для вашей компании создается при первом посадке [на портал Azure, сертифицированный для IoT портала.](howto-onboard-portal.md) Третья сторона может поделиться с вами строкой подключения репозитория модели, чтобы предоставить вам доступ к их интерфейсам и моделям.

### <a name="interact-with-a-device"></a>Взаимодействие с устройством

Вы можете использовать расширение для просмотра и взаимодействия с устройствами IoT Plug и Play, которые подключены к концентратору IoT. Расширение работает с цифровым близнецом, который представляет IoT Plug и Play устройства.

#### <a name="list-devices-and-interfaces"></a>Список устройств и интерфейсов

Перечислите все устройства в концентраторе IoT:

```azurecli
az iot hub device-identity list --hub-name {YourIoTHubName}
```

Перечислите все интерфейсы, зарегистрированные устройством IoT Plug and Play:

```azurecli
az iot dt list-interfaces --hub-name {YourIoTHubName} --device-id {YourDeviceID}
```

#### <a name="properties"></a>Свойства

Перечислите все значения свойств и свойств для интерфейса на устройстве:

```azurecli
az iot dt list-properties --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --source private --repo-login "{YourCompanyModelRepoConnectionString}"
```

Установите значение свойства чтения-записи:

```azurecli
az iot dt update-property --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface-payload {JSONPayload or FilePath}
```

Пример файла полезной нагрузки для установки свойства **имени** на **сенсорном** интерфейсе устройства для **Contoso** выглядит следующим образом:

```json
{
  "sensor": {
    "properties": {
      "name": {
        "desired": {
          "value": "Contoso"
        }
      }
    }
  }
}
```

#### <a name="commands"></a>Команды

Перечислите все команды для интерфейса на устройстве:

```azurecli
az iot dt list-commands --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --source private --repo-login {YourCompanyModelRepoConnectionString}
```

Без `--repo-login` параметра эта команда использует репозиторий общедоступной модели.

Вызвать команду:

```azurecli
az iot dt invoke-command --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --cn {CommandName} --command-payload {CommandPayload or FilePath}
```

#### <a name="digital-twin-events"></a>Цифровые события-близнецы

Мониторинг всех событий IoT Plug и Play digital twin с определенного устройства и интерфейса, идущего в **$Default** группу потребителей центра событий:

```azurecli
az iot dt monitor-events --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID}
```

Мониторинг всех событий IoT Plug и Play digital twin с определенного устройства и интерфейса, идущего по определенной группе потребителей:

```azurecli
az iot dt monitor-events --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --consumer-group {YourConsumerGroup}
```

### <a name="manage-interfaces-in-a-model-repository"></a>Управление интерфейсами в репозитории моделей

Следующие команды используют общедоступный репозиторий IoT Plug и Play. Чтобы использовать репозиторий модели `--login` компании, добавьте аргумент со строкой репозитория модели.

Перечислите интерфейсы в общедоступном репозитории модели IoT Plug and Play:

```azurecli
az iot pnp interface list
```

Отображите интерфейс в общедоступном репозитории модели IoT Plug and Play:

```azurecli
az iot pnp interface show --interface {YourInterfaceId}
```

Создайте интерфейс в репозитории модели IoT Plug and Play:

```azurecli
az iot pnp interface create --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

Вы не можете непосредственно создать интерфейс в общедоступном репозитории моделей.

Обновление интерфейса в репозитории модели IoT Plug and Play:

```azurecli
az iot pnp interface update --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

Вы не можете напрямую обновлять интерфейс в общедоступном репозитории моделей.

Опубликовать интерфейс из репозитория модели IoT Plug and Play в общедоступном репозитории моделей. Эта операция делает интерфейс неизменяемым:

```azurecli
az iot pnp interface publish --interface {YourInterfaceID} --login {YourCompanyModelRepoConnectionString}
```

Публиковать интерфейсы в репозитории общедоступных моделей могут только партнеры Майкрософт.

### <a name="manage-device-capability-models-in-a-model-repository"></a>Управление моделями возможностей устройств в репозитории модели

Следующие команды используют общедоступный репозиторий IoT Plug и Play. Чтобы использовать репозиторий модели `--login` компании, добавьте аргумент со строкой репозитория модели.

Перечислите модели возможностей устройств в репозитории моделей IoT Plug и Play:

```azurecli
az iot pnp capability-model list
```

Отображите модель возможностей устройства в репозитории модели IoT Plug and Play:

```azurecli
az iot pnp capability-model show --model {YourModelID}
```

Создайте модель возможностей устройства в репозитории модели IoT Plug and Play:

```azurecli
az iot pnp capability-model create --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

Вы не можете непосредственно создать модель в репозитории общедоступной модели.

Обновление модели возможностей устройства в репозитории модели IoT Plug and Play:

```azurecli
az iot pnp capability-model update --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

Вы не можете напрямую обновить модель в общедоступном репозитории модели.

Публикация модели возможностей устройства из репозитория модели IoT Plug and Play в общедоступном репозитории моделей. Эта операция делает модель неизменной:

```azurecli
az iot pnp capability-model publish --model {YourModelID} --login {YourCompanyModelRepoConnectionString}
```

Публиковать модели в репозитории общедоступных моделей могут только партнеры Майкрософт.

## <a name="next-steps"></a>Дальнейшие действия

В этой статье вы узнали, как установить и использовать расширение Azure IoT для Azure CLI для взаимодействия с устройствами Plug and Play. Следующий шаг предлагается узнать, как [управлять моделями.](./howto-manage-models.md)
