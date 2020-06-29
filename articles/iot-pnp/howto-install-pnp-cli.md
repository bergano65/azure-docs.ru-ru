---
title: Использование расширения Интернета вещей Azure для Azure CLI для взаимодействия с устройствами IoT Plug and Play (предварительная версия) | Документация Майкрософт
description: Установите расширение Интернета вещей Azure для Azure CLI и используйте его для взаимодействия с устройствами IoT Plug and Play, подключенными к центру Интернета вещей.
author: Philmea
ms.author: philmea
ms.date: 12/26/2019
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 1ccb32996cd8f15805a810dd5b5985aeb5f87c26
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81770459"
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
- Использование расширения для управления интерфейсами в репозитории модели.

## <a name="install-azure-iot-extension-for-the-azure-cli"></a>Установка расширения Интернета вещей Azure для Azure CLI

### <a name="step-1---install-the-azure-cli"></a>Шаг 1. Установка Azure CLI

Следуйте [инструкциям по установке](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest), чтобы настроить Azure CLI в своей среде. Чтобы использовать все приведенные ниже команды, версия Azure CLI должна быть не ниже 2.0.73. Для проверки используйте `az -–version`.

### <a name="step-2---install-iot-extension"></a>Шаг 2. Установка расширения Интернета вещей

В [файле сведений расширения Интернета вещей](https://github.com/Azure/azure-iot-cli-extension) описывается несколько способов установки расширения. Проще всего запустить `az extension add --name azure-iot`. После установки можно использовать `az extension list`, чтобы проверить установленные расширения, или `az extension show --name azure-iot` для просмотра сведений о расширении Интернета вещей. Чтобы удалить расширение, можно использовать `az extension remove --name azure-iot`.

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

- Некоторым командам требуется строка подключения для репозитория моделей компании. Репозиторий моделей для вашей компании создается при первом [использовании портала сертификации Azure для Интернета вещей](howto-onboard-portal.md). Третья сторона может поделиться строкой подключения к своему репозиторию моделей, чтобы предоставить вам доступ к своим интерфейсам и моделям.

### <a name="interact-with-a-device"></a>Взаимодействие с устройством

С помощью расширения можно просматривать устройства IoT Plug and Play, подключенные к центру Интернета вещей, и взаимодействовать с ними. Расширение работает с цифровым двойником, представляющим устройство IoT Plug and Play.

#### <a name="list-devices-and-interfaces"></a>Вывод списка устройств и интерфейсов

Вывод списка всех устройств в центре Интернета вещей:

```azurecli
az iot hub device-identity list --hub-name {YourIoTHubName}
```

Вывод списка всех интерфейсов, зарегистрированных на устройстве IoT Plug and Play:

```azurecli
az iot dt list-interfaces --hub-name {YourIoTHubName} --device-id {YourDeviceID}
```

#### <a name="properties"></a>Свойства

Вывод списка всех свойств и значений свойств для интерфейса на устройстве:

```azurecli
az iot dt list-properties --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --source private --repo-login "{YourCompanyModelRepoConnectionString}"
```

Задание значения свойства для чтения и записи:

```azurecli
az iot dt update-property --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface-payload {JSONPayload or FilePath}
```

Пример файла полезных данных для задания свойства **name** в интерфейсе **sensor** для устройства **Contoso** выглядит следующим образом:

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

```azurecli
az iot dt list-commands --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --source private --repo-login {YourCompanyModelRepoConnectionString}
```

Без параметра `--repo-login` эта команда использует общедоступный репозиторий моделей.

Вызов команды:

```azurecli
az iot dt invoke-command --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --cn {CommandName} --command-payload {CommandPayload or FilePath}
```

#### <a name="digital-twin-events"></a>события цифрового двойника

Мониторинг всех событий цифрового двойника IoT Plug and Play с определенного устройства и интерфейса, который переходит в группу потребителей концентратора событий **$Default**:

```azurecli
az iot dt monitor-events --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID}
```

Мониторинг всех событий цифрового двойника IoT Plug and Play с определенного устройства и интерфейса, который переходит в определенную группу потребителей:

```azurecli
az iot dt monitor-events --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --consumer-group {YourConsumerGroup}
```

### <a name="manage-interfaces-in-a-model-repository"></a>Управление интерфейсами в репозитории моделей

Следующие команды используют общедоступный репозиторий моделей IoT Plug and Play. Чтобы использовать репозиторий моделей компании, добавьте аргумент `--login` в строку подключения к репозиторию моделей.

Вывод списка интерфейсов в общедоступном репозитории моделей IoT Plug and Play:

```azurecli
az iot pnp interface list
```

Отображение интерфейса в общедоступном репозитории моделей IoT Plug and Play:

```azurecli
az iot pnp interface show --interface {YourInterfaceId}
```

Создание интерфейса в репозитории моделей IoT Plug and Play компании:

```azurecli
az iot pnp interface create --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

Создать интерфейс напрямую в общедоступном репозитории моделей невозможно.

Обновление интерфейса в репозитории моделей IoT Plug and Play компании:

```azurecli
az iot pnp interface update --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

Обновить интерфейс напрямую в общедоступном репозитории моделей невозможно.

Опубликуйте интерфейс из репозитория IoT Plug and Play компании в общедоступном репозитории моделей. Эта операция делает интерфейс неизменяемым:

```azurecli
az iot pnp interface publish --interface {YourInterfaceID} --login {YourCompanyModelRepoConnectionString}
```

Публиковать интерфейсы в общедоступном репозитории моделей могут только партнеры Майкрософт.

### <a name="manage-device-capability-models-in-a-model-repository"></a>Управление моделями возможностей устройств в репозитории моделей

Следующие команды используют общедоступный репозиторий моделей IoT Plug and Play. Чтобы использовать репозиторий моделей компании, добавьте аргумент `--login` в строку подключения к репозиторию моделей.

Вывод списка моделей возможностей устройств в общедоступном репозитории моделей IoT Plug and Play:

```azurecli
az iot pnp capability-model list
```

Отображение модели возможностей устройства в общедоступном репозитории моделей IoT Plug and Play:

```azurecli
az iot pnp capability-model show --model {YourModelID}
```

Создание модели возможностей устройства в репозитории моделей IoT Plug and Play компании:

```azurecli
az iot pnp capability-model create --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

Создать модель напрямую в общедоступном репозитории моделей невозможно.

Обновление модели возможностей устройства в репозитории моделей IoT Plug and Play компании:

```azurecli
az iot pnp capability-model update --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

Обновить модель напрямую в общедоступном репозитории моделей невозможно.

Опубликуйте модель возможностей устройства из репозитория IoT Plug and Play компании в общедоступном репозитории моделей. Эта операция делает модель неизменяемой:

```azurecli
az iot pnp capability-model publish --model {YourModelID} --login {YourCompanyModelRepoConnectionString}
```

Публиковать модели в общедоступном репозитории моделей могут только партнеры Майкрософт.

## <a name="next-steps"></a>Дальнейшие действия

В этом пошаговом руководстве вы узнали, как установить и использовать расширение Интернета вещей Azure для Azure CLI взаимодействия с устройствами Plug and Play. Далее рекомендуем узнать, [как управлять моделями](./howto-manage-models.md).
