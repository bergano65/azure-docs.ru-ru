---
title: Развертывание пользовательского образа симулятора устройств в Azure | Документация Майкрософт
description: Из этого практического руководства вы узнаете, как развернуть пользовательский образ Docker решения "Симулятор устройств" в Azure.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc
ms.date: 11/06/2018
ms.author: dobett
ms.openlocfilehash: c1f321f452b65016c11cb66d08ebab108509cc62
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "61448415"
---
# <a name="deploy-a-custom-device-simulation-docker-image"></a>Развертывание пользовательского образа Docker для симулятора устройств

Решение "Симулятор устройств" можно изменить, добавив пользовательские функции. Например, в статье [Сериализация данных телеметрии с помощью буферов протокола](iot-accelerators-device-simulation-protobuf.md) показано, как добавить пользовательское устройство в решение, в котором данные телеметрии отправляются с помощью буферов протокола (Protobuf). После проверки изменений локально следующим шагом является развертывание изменений в экземпляр симулятора устройств в Azure. Для выполнения этой задачи необходимо создать и развернуть образ Docker, содержащий измененную службу.

В этом практическом руководстве описаны следующие процедуры:

1. Подготовка среды разработки.
1. Создание образа Docker.
1. Настройка симулятора устройства для использования нового образа Docker.
1. Запуск симуляции с помощью нового образа.

## <a name="prerequisites"></a>Технические условия

Для выполнения шагов из этого руководства вам понадобится следующее:

* Развернутый экземпляр [симулятора устройств](quickstart-device-simulation-deploy.md).
* Docker. Загрузите [Docker Community Edition](https://www.docker.com/products/docker-engine#/download) для своей платформы.
* [Учетная запись Docker Hub](https://hub.docker.com/), в которую можно отправлять образы Docker. В учетной записи Docker Hub создайте общедоступный репозиторий с именем **device-simulation**.
* Измененное и протестированное [решение "Симулятор устройств"](https://github.com/Azure/device-simulation-dotnet/archive/master.zip) на локальном компьютере. Например, можно изменить решение, чтобы [сериализовать данные телеметрии с помощью буферов протокола](iot-accelerators-device-simulation-protobuf.md).
* Оболочка, в которой можно запустить SSH. Если установить Git для Windows, можно использовать оболочку **bash**, которая является частью установки. Вы можете также использовать [Azure Cloud Shell](https://shell.azure.com/).

В инструкциях этой статьи предполагается, что вы используете Windows. Если вы используете другую операционную систему, может потребоваться изменить некоторые пути к файлам и команды в соответствии с требованиями вашей среды.

## <a name="create-a-new-docker-image"></a>Создание образа Docker

Чтобы развернуть собственные изменения в службу симулятора устройств, вам потребуется изменить сборку и скрипты развертывания в папке **scripts\docker** для отправки контейнеров в учетную запись docker-hub.

### <a name="modify-the-docker-scripts"></a>Изменение скриптов Docker

Добавьте в скрипты **build.cmd**, **publish.cmd** и **run.cmd** Docker в папке **scripts\docker** сведения о репозитории центра Docker. Предполагается, что вы создали общедоступный репозиторий с именем **device-simulation**:

`DOCKER_IMAGE={your-docker-hub-username}/device-simulation`

Обновите файл **docker-compose.yml** следующим образом:

`image: {your-docker-hub-username}/device-simulation`

### <a name="configure-the-solution-to-include-any-new-files"></a>Настройка решения для включения новых файлов

При добавлении новых файлов модели устройства необходимо явно включить их в решение. Добавьте запись в файл **services\services.csproj** для каждого дополнительного файла, который требуется включить. Например, если вы выполнили инструкции из руководства по [сериализации данных телеметрии с помощью буферов протокола](iot-accelerators-device-simulation-protobuf.md), добавьте следующие записи:

```xml
<None Update="data\devicemodels\assettracker-01.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
<None Update="data\devicemodels\scripts\assettracker-01-state.js">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
```

### <a name="generate-new-docker-images-and-push-to-docker-hub"></a>Создание образов Docker и отправка в Docker Hub

Опубликуйте новый образ Docker в Docker Hub, используя учетную запись docker-hub:

1. Откройте командную строку и перейдите к локальной копии репозитория симулятора устройств.

1. Перейдите в папку **docker**:

    ```cmd
    cd scripts\docker
    ```

1. Чтобы создать образ Docker, выполните следующую команду:

    ```cmd
    build.cmd
    ```

1. Чтобы опубликовать образ Docker в репозитории Docker Hub, выполните следующую команду: Войдите в Docker с помощью учетных данных Docker Hub:

    ```cmd
    docker login
    publish.cmd
    ```

<!-- TODO fix heading levels working include -->

[!INCLUDE [iot-solution-accelerators-access-vm](../../includes/iot-solution-accelerators-access-vm.md)]

## <a name="update-the-service"></a>Обновление службы

Чтобы обновить контейнер симулятора устройств для применения пользовательского образа, выполните следующие действия:

* Подключитесь по SSH к виртуальной машине, в которой размещен экземпляр симулятора устройств. Используйте IP-адрес и пароль, которые вы записали в предыдущем разделе:

    ```sh
    ssh azureuser@{your vm ip address}
    ```

* Перейдите в каталог **/app**.

    ```sh
    cd /app
    ```

* Измените файл **docker-compose.yml**.

    ```sh
    sudo nano docker-compose.yml
    ```

    Измените **образ**, чтобы указать пользовательский образ **device-simulation**, отправленный в репозиторий Docker Hub:

    ```yml
    image: {your-docker-hub-username}/device-simulation
    ```

    Сохраните изменения.

* Выполните следующую команду, чтобы перезапустить микрослужбы:

    ```sh
    sudo start.sh
    ```

## <a name="run-your-simulation"></a>Выполнение симуляции

Теперь симуляцию можно запустить с использованием настроенного решения "Симулятор устройств":

1. Запустите веб-интерфейс имитирования устройств из [акселератора решений Интернета вещей Microsoft Azure](https://www.azureiotsolutions.com/Accelerators#dashboard).

1. Настройте и запустите симуляцию с помощью веб-интерфейса. Если вы ранее выполнили инструкции из руководства по [сериализации данных телеметрии с помощью буферов протокола](iot-accelerators-device-simulation-protobuf.md), вы можете использовать модель пользовательского устройства.

## <a name="next-steps"></a>Дальнейшие действия

Вы узнали, как развернуть пользовательский образ симулятора устройств. Теперь вам будет полезно узнать, как [использовать имеющийся центр Интернета вещей с акселератором решений "Симулятор устройств"](iot-accelerators-device-simulation-choose-hub.md).