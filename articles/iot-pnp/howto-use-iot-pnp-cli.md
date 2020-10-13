---
title: Использование расширения Интернета вещей Azure для Azure CLI взаимодействия с устройствами IoT Plug and Play | Документация Майкрософт
description: Установите расширение Интернета вещей Azure для Azure CLI и используйте его для взаимодействия с устройствами IoT Plug and Play, подключенными к центру Интернета вещей.
author: dominicbetts
ms.author: dobett
ms.date: 07/20/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 680cd4ef4f73c63850a2137b344fd0af6b27c673
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91577464"
---
# <a name="install-and-use-the-azure-iot-extension-for-the-azure-cli"></a>Установка и использование расширения Интернета вещей Azure для Azure CLI

[Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest&preserve-view=true) — это кроссплатформенная программа командной строки с открытым кодом для управления ресурсами Azure (например, центр Интернета вещей). Эта Azure CLI доступна в Windows, Linux и macOS. Azure CLI позволяет управлять ресурсами центра Интернета вещей Azure, экземплярами службы подготовки устройств и связанными центрами без установки каких-либо расширений.

Расширение Интернета вещей Azure для Azure CLI — это программа командной строки для взаимодействия с устройствами IoT Plug and Play и их тестирования. Расширение можно использовать для выполнения следующих задач:

- подключение к устройству;
- просмотр данных телеметрии с устройства;
- работа со свойствами устройства;
- вызов команд устройства.

В этой статье показано, как выполнить следующие действия:

- Установка и настройка расширения Интернета вещей Azure для Azure CLI.
- Использование расширения для взаимодействия с устройствами и их тестирования.

## <a name="install-azure-iot-extension-for-the-azure-cli"></a>Установка расширения Интернета вещей Azure для Azure CLI

### <a name="step-1---install-the-azure-cli"></a>Шаг 1. Установка Azure CLI

Следуйте [инструкциям по установке](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true), чтобы настроить Azure CLI в своей среде. Для оптимальной работы Azure CLI версия должна быть версии 2.9.1 или выше. Для проверки используйте `az -–version`.

### <a name="step-2---install-iot-extension"></a>Шаг 2. Установка расширения Интернета вещей

В [файле сведений расширения Интернета вещей](https://github.com/Azure/azure-iot-cli-extension) описывается несколько способов установки расширения. Проще всего запустить `az extension add --name azure-iot`. После установки можно использовать `az extension list`, чтобы проверить установленные расширения, или `az extension show --name azure-iot` для просмотра сведений о расширении Интернета вещей. На момент написания статьи номер версии расширения имеет значение `0.10.0` .

Чтобы удалить расширение, можно использовать `az extension remove --name azure-iot`.

## <a name="use-azure-iot-extension-for-the-azure-cli"></a>Использование расширения Интернета вещей Azure для Azure CLI

### <a name="prerequisites"></a>Предварительные требования

Выполните следующую команду, чтобы войти в свою подписку Azure.

```azurecli
az login
```

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

### <a name="manage-models-in-the-model-repository"></a>Управление моделями в репозитории модели

Для управления моделями в репозитории можно использовать команды репозитория модели Azure CLI.

#### <a name="create-model-repository"></a>Создать репозиторий модели

Создайте новый репозиторий компании IoT Plug and Play для вашего клиента, если вы являетесь первым пользователем в клиенте:

```azurecli
az iot pnp repo create
```

#### <a name="manage-model-repository-tenant-roles"></a>Управление ролями клиента репозитория модели

Создание назначения роли для пользователя или субъекта-службы для определенного ресурса.

Например, предоставьте user@consoso.com роль **моделскреатор** для клиента:

```azurecli
az iot pnp role-assignment create --resource-id {tenant_id} --resource-type Tenant --subject-id {user@contoso.com} --subject-type User --role ModelsCreator
```

Или предоставьте user@consoso.com роль **моделадминистратор** для конкретной модели:

```azurecli
az iot pnp role-assignment create --resource-id {model_id} --resource-type Model --subject-id {user@contoso.com} --subject-type User --role ModelAdministrator
```

#### <a name="create-a-model"></a>Создание модели

Создайте новую модель в репозитории компании:

```azurecli
az iot pnp model create --model {model_json or path_to_file}
```

#### <a name="search-a-model"></a>Поиск в модели

Перечисление моделей, соответствующих заданному ключевому слову:

```azurecli
az iot pnp model list -q {search_keyword}
```

#### <a name="publish-a-model"></a>Публикация модели

Опубликуйте модель устройства, расположенную в репозитории компании, в общедоступном репозитории.

Например, сделайте общедоступной модель с ИДЕНТИФИКАТОРом `dtmi:com:example:ClimateSensor;1` :

```azurecli
az iot pnp model publish --dtmi "dtmi:com:example:ClimateSensor;1"
```

Для публикации модели необходимо выполнить следующие требования.

- Клиент компании или организации должен быть партнером корпорации Майкрософт. 
- Пользователь или субъект-служба должен быть членом роли **издателя** клиента репозитория.

## <a name="next-steps"></a>Дальнейшие шаги

В этой статье вы узнали, как установить и использовать расширение Интернета вещей Azure для Azure CLI взаимодействия с устройствами IoT Plug and Play. Предлагаемый следующий шаг — Узнайте, как использовать [Azure IOT Explorer с вашими устройствами](./howto-use-iot-explorer.md).
