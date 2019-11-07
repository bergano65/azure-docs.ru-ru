---
title: Использование расширения Интернета вещей Azure для Azure CLI для взаимодействия с Интернетом вещей Plug and Play предварительной версии устройства | Документация Майкрософт
description: Установите расширение Интернета вещей Azure для Azure CLI и используйте его для взаимодействия с устройствами IoT Plug and Play, подключенными к центру Интернета вещей.
author: ChrisGMsft
ms.author: chrisgre
ms.date: 09/08/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: eb4f607672c39d45b7791ccaeeb6f7cff9393cb9
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73571021"
---
# <a name="install-and-use-the-azure-iot-extension-for-the-azure-cli"></a>Установка и использование расширения Интернета вещей Azure для Azure CLI

[Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) — это программа командной строки с открытым исходным кодом для управления ресурсами Azure, такими как центр Интернета вещей. Эта Azure CLI доступна в Windows, Linux и MacOS. Azure CLI также предварительно установлен в [Azure Cloud Shell](https://shell.azure.com). Azure CLI позволяет управлять ресурсами центра Интернета вещей Azure, экземплярами службы подготовки устройств и связанными концентраторами без установки каких-либо расширений.

Расширение Интернета вещей Azure для Azure CLI — это программа командной строки для взаимодействия с и тестирования центра Интернета вещей Plug and Play Preview Devices. Расширение можно использовать для:

- Подключитесь к устройству.
- Просмотр данных телеметрии, отправляемых устройством.
- Работа со свойствами устройства.
- Вызов команд устройства.

В этой статье описаны следующие процессы.

- Установите и настройте расширение Интернета вещей Azure для Azure CLI.
- Используйте расширение, чтобы взаимодействовать с устройствами и тестировать их.
- Используйте расширение для управления интерфейсами в репозитории модели.

## <a name="install-azure-iot-extension-for-the-azure-cli"></a>Установка расширения Azure IoT для Azure CLI

### <a name="step-1---install-the-azure-cli"></a>Шаг 1. Установка Azure CLI

Следуйте [инструкциям по установке](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) , чтобы настроить Azure CLI в вашей среде. Чтобы использовать все приведенные ниже команды, версия Azure CLI должна иметь версию 2.0.73 или выше. Для проверки используйте `az -–version`.

### <a name="step-2---install-iot-extension"></a>Шаг 2. Установка расширения IoT

В [файле сведений расширения Интернета вещей](https://github.com/Azure/azure-iot-cli-extension) описывается несколько способов установки расширения. Проще всего запустить `az extension add --name azure-cli-iot-ext`. После установки можно использовать `az extension list`, чтобы проверить установленные расширения, или `az extension show --name azure-cli-iot-ext` для просмотра сведений о расширении Интернета вещей. Чтобы удалить расширение, можно использовать `az extension remove --name azure-cli-iot-ext`.

## <a name="use-azure-iot-extension-for-the-azure-cli"></a>Использование расширения Azure IoT для Azure CLI

### <a name="prerequisites"></a>Предварительные требования

Чтобы войти в подписку Azure, выполните следующую команду:

```cmd/sh
az login
```

> [!NOTE]
> Если вы используете Azure Cloud Shell, вы автоматически входите в нее и не должны выполнять предыдущую команду.

Чтобы использовать расширение Интернета вещей Azure для Azure CLI, вам потребуется:

- Центр интернета вещей Azure. Существует много способов добавить центр Интернета вещей в подписку Azure, например [создать центр Интернета вещей с помощью Azure CLI](../iot-hub/iot-hub-create-using-cli.md). Для выполнения команд расширения Azure IoT требуется строка подключения центра Интернета вещей. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

    > [!NOTE]
    > Во время общедоступной предварительной версии функции Интернета вещей Plug and Play доступны только в **центрах**Интернета вещей, созданных в центральной части США, **Северной Европе**и в **восточной части Японии** .

- Устройство, зарегистрированное в центре Интернета вещей. Для регистрации устройства можно использовать следующую команду Azure CLI. не забудьте заменить заполнители `{YourIoTHubName}` и `{YourDeviceID}` своими значениями:

    ```cmd/sh
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id {YourDeviceID}
    ```

- Некоторым командам требуется строка подключения для репозитория модели компании. Репозиторий модели для вашей компании создается при первом подключении [к порталу сертификации Azure для Интернета вещей](howto-onboard-portal.md). Третья сторона может совместно использовать свою строку подключения к хранилищу модели, чтобы предоставить вам доступ к их интерфейсам и моделям.

### <a name="interact-with-a-device"></a>Взаимодействие с устройством

С помощью расширения можно просматривать устройства IoT Plug and Play, подключенные к центру Интернета вещей, и взаимодействовать с ними. Расширение работает с цифровым двойника, представляющим устройство IoT Plug and Play.

#### <a name="list-devices-and-interfaces"></a>Вывод списка устройств и интерфейсов

Вывод списка всех устройств в центре Интернета вещей:

```cmd/sh
az iot hub device-identity list --hub-name {YourIoTHubName}
```

Вывод списка всех интерфейсов, зарегистрированных на устройстве Plug and Play IoT:

```cmd/sh
az iot dt list-interfaces --hub-name {YourIoTHubName} --device-id {YourDeviceID}
```

#### <a name="properties"></a>Свойства

Перечислить все свойства и значения свойств для интерфейса на устройстве:

```cmd/sh
az iot dt list-properties --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --source private --repo-login "{YourCompanyModelRepoConnectionString}"
```

Установите значение свойства для чтения и записи:

```cmd/sh
az iot dt update-property --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface-payload {JSONPayload or FilePath}
```

Пример файла полезных данных для задания свойства **Name** в интерфейсе **датчика** устройства на **contoso** выглядит следующим образом:

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

Вывод списка всех команд для интерфейса на устройстве:

```cmd/sh
az iot dt list-commands --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --source private --repo-login {YourCompanyModelRepoConnectionString}
```

Без параметра `--repo-login` эта команда использует репозиторий общедоступной модели.

Вызов команды:

```cmd/sh
az iot dt invoke-command --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --cn {CommandName} --command-payload {CommandPayload or FilePath}
```

#### <a name="digital-twin-events"></a>События цифровых двойника

Мониторинг всех событий Интернета вещей Plug and Play Digital двойника с определенного устройства и интерфейса, который переведет в **$Default** группу потребителей концентратора событий:

```cmd/sh
az iot dt monitor-events --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID}
```

Мониторинг всех событий Интернета вещей Plug and Play Digital двойника с определенного устройства и интерфейса, поступающий в определенную группу потребителей:

```cmd/sh
az iot dt monitor-events --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --consumer-group {YourConsumerGroup}
```

### <a name="manage-interfaces-in-a-model-repository"></a>Управление интерфейсами в репозитории модели

Следующие команды используют общедоступный репозиторий модели Интернета вещей Plug and Play. Чтобы использовать репозиторий модели компании, добавьте аргумент `--login` в строку подключения к репозиторию модели.

Вывод списка интерфейсов в общедоступном репозитории моделей Plug and Play IoT:

```cmd/sh
az iot pnp interface list
```

Отображение интерфейса в общедоступном репозитории модели Plug and Play IoT:

```cmd/sh
az iot pnp interface show --interface {YourInterfaceId}
```

Создайте интерфейс в репозитории модели компании IoT Plug and Play.

```cmd/sh
az iot pnp interface create --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

Вы не можете напрямую создать интерфейс в репозитории общедоступной модели.

Обновите интерфейс в репозитории модели компании Интернета вещей Plug and Play:

```cmd/sh
az iot pnp interface update --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

Невозможно напрямую обновить интерфейс в репозитории общедоступной модели.

Опубликуйте интерфейс из репозитория Plug and Play компании IoT в общедоступном репозитории моделей. Эта операция делает интерфейс неизменяемым:

```cmd/sh
az iot pnp interface publish --interface {YourInterfaceID} --login {YourCompanyModelRepoConnectionString}
```

Только партнеры Майкрософт могут публиковать интерфейсы в хранилище общедоступных моделей.

### <a name="manage-device-capability-models-in-a-model-repository"></a>Управление моделями возможностей устройств в репозитории моделей

Следующие команды используют общедоступный репозиторий модели Интернета вещей Plug and Play. Чтобы использовать репозиторий модели компании, добавьте аргумент `--login` в строку подключения к репозиторию модели.

Список моделей возможностей устройств в репозитории общедоступных моделей Plug and Play IoT:

```cmd/sh
az iot pnp capability-model list
```

Отображение модели возможностей устройства в репозитории общедоступной модели Plug and Play IoT:

```cmd/sh
az iot pnp capability-model show --model {YourModelID}
```

Создание модели возможностей устройства в репозитории корпоративных моделей Plug and Play IoT:

```cmd/sh
az iot pnp capability-model create --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

Нельзя напрямую создать модель в репозитории общедоступной модели.

Обновите модель возможностей устройства в репозитории корпоративных моделей Plug and Play IoT:

```cmd/sh
az iot pnp capability-model update --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

Вы не можете напрямую обновить модель в репозитории общедоступной модели.

Опубликуйте модель возможностей устройства из репозитория Plug and Play компании IoT в общедоступном репозитории моделей. Эта операция делает модель неизменяемой:

```cmd/sh
az iot pnp capability-model publish --model {YourModelID} --login {YourCompanyModelRepoConnectionString}
```

Только партнеры Майкрософт могут публиковать модели в общедоступном репозитории моделей.

## <a name="next-steps"></a>Дальнейшие действия

В этом пошаговом руководстве вы узнали, как установить и использовать расширение Интернета вещей Azure для Azure CLI взаимодействия с устройствами Plug and Play. Рекомендуемый следующий шаг — научиться [управлять моделями](./howto-manage-models.md).
