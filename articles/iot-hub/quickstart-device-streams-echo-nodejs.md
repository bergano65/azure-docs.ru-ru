---
title: Потоки устройств Центра Интернета вещей в Node.js (предварительная версия) | Документация Майкрософт
description: В этом кратком руководстве будет выполняться приложение Node.js на стороне службы, которое обменивается данными с устройством IoT через поток устройств.
author: rezasherafat
manager: briz
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: mvc
ms.date: 01/15/2019
ms.author: rezas
ms.openlocfilehash: 590faaf727345dcfe8ab61a1860ca46d78256b22
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55219011"
---
# <a name="quickstart-communicate-to-a-device-application-in-nodejs-via-iot-hub-device-streams-preview"></a>Краткое руководство. Взаимодействие с приложением устройства в Node.js с помощью потоков устройств Центра Интернета вещей (предварительная версия)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

[Потоки устройств Центра Интернета вещей](./iot-hub-device-streams-overview.md) позволяют службам и приложениям устройств безопасным и подходящим методом обмениваться данными с брандмауэром. На этапе предварительной версии пакет SDK для Node.js поддерживает только потоки устройств на стороне службы. В результате это краткое руководство охватывает только инструкции по запуску приложения на стороне службы. Вам следует запустить сопутствующее приложение на стороне устройства, которое доступно в кратких руководствах [по C](./quickstart-device-streams-echo-c.md) и [C#](./quickstart-device-streams-echo-csharp.md).

Приложение Node.js на стороне службы, используемое в этом кратком руководстве, имеет следующие функции:

* создание потока устройств к устройству IoT;

* считывание введенной в командную строку информации и отправка в приложение устройства, которое отправит ее обратно.

Код продемонстрирует процесс инициализации потока устройств, а также его использование для отправки и получения данных.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.


## <a name="prerequisites"></a>Предварительные требования

Для запуска приложения на стороне службы в этом руководстве вам понадобится Node.js версии 4.x.x или более поздней версии на компьютере разработчика.

Node.js, предназначенный для нескольких платформ, можно скачать здесь: [Node.js.org](https://nodejs.org).

Текущую версию Node.js на компьютере, на котором ведется разработка, можно проверить, используя следующую команду:

```
node --version
```

Если вы еще не сделали это, скачайте пример проекта Node.js по адресу https://github.com/Azure-Samples/azure-iot-samples-node/archive/streams-preview.zip и извлеките ZIP-архив.


## <a name="create-an-iot-hub"></a>Создание Центра Интернета вещей

Если вы закончили работу с предыдущим руководством по [ отправке данных телеметрии с устройства в Центр Интернета вещей](quickstart-send-telemetry-node.md), можете пропустить этот шаг.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub-device-streams.md)]


## <a name="register-a-device"></a>Регистрация устройства

Если вы закончили работу с предыдущим руководством по [ отправке данных телеметрии с устройства в Центр Интернета вещей](quickstart-send-telemetry-node.md), можете пропустить этот шаг.

Устройство должно быть зарегистрировано в Центре Интернета вещей, прежде чем оно сможет подключиться. В этом кратком руководстве для регистрации имитируемого устройства используется Azure Cloud Shell.

1. Выполните приведенные ниже команды в Azure Cloud Shell, чтобы добавить расширение CLI Центра Интернета вещей и создать удостоверение устройства. 

   **YourIoTHubName**. Замените этот заполнитель именем вашего центра Интернета вещей.

   **MyDevice**. Это имя, присвоенное зарегистрированному устройству. Используйте имя MyDevice, как показано в примере. Если вы выбрали другое имя для устройства, используйте его при работе с этим руководством и обновите имя устройства в примерах приложений перед их запуском.

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyDevice
    ```

2. Чтобы разрешить внутреннему приложению подключаться к Центру Интернета вещей и получать сообщения, вам необходима _строка подключения к службе_. Следующая команда извлекает строку подключения службы для Центра Интернета вещей:

    **YourIoTHubName**. Замените этот заполнитель именем вашего центра Интернета вещей.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --hub-name YourIoTHubName
    ```

    Запомните или запишите возвращаемое значение, которое выглядит следующим образом:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`


## <a name="communicate-between-device-and-service-via-device-streams"></a>Обмен данными между устройством и службой через потоки устройств

### <a name="run-the-device-side-application"></a>Запуск приложения на стороне устройства

Как упоминалось ранее, пакет SDK для Node.js Центра Интернета вещей поддерживает только потоки устройств на стороне службы. Для приложения на стороне устройства используйте сопутствующую программу устройства, которая доступна в кратких руководствах [по C](./quickstart-device-streams-echo-c.md) и [C#](./quickstart-device-streams-echo-csharp.md). Прежде чем перейти к следующему шагу, убедитесь, что приложение на стороне устройства запущено.


### <a name="run-the-service-side-application"></a>Запуск приложения на стороне службы

Если приложение на стороне устройства запущено, выполните следующие действия, чтобы запустить приложение на стороне службы в Node.js.

- Укажите свои учетные данные службы и идентификатор устройства в качестве переменных среды.
```
  # In Linux
  export IOTHUB_CONNECTION_STRING="<provide_your_service_connection_string>"
  export STREAMING_TARGET_DEVICE="MyDevice"

  # In Windows
  SET IOTHUB_CONNECTION_STRING=<provide_your_service_connection_string>
  SET STREAMING_TARGET_DEVICE=MyDevice
```
Измените имя `MyDevice` на идентификатор выбранного вами устройства.

- Перейдите к папке `Quickstarts/device-streams-service` в распакованной папке проекта и запустите образец, используя узел.
```
  cd azure-iot-samples-node-streams-preview/iot-hub/Quickstarts/device-streams-service
  
  # Install the preview service SDK, and other dependencies
  npm install azure-iothub@streams-preview
  npm install

  node echo.js
```

После последнего шага программа на стороне службы инициирует поток к устройству и после установления соединения отправит службе через поток строковый буфер. В этом примере программа на стороне службы просто считывает данные из потока STDIN, введенные в окне терминала, и отправляет на устройство, которое затем возвращает их обратно. Это демонстрирует успешную двунаправленную связь между приложениями.

Выходные данные консоли на стороне службы: ![замещающий текст](./media/quickstart-device-streams-echo-nodejs/service-console-output.PNG "Выходные данные консоли на стороне службы")


Теперь можно завершить выполнение программы, повторно нажав клавишу ВВОД.


## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]


## <a name="next-steps"></a>Дополнительная информация

В этом кратком руководстве вы настроили Центр Интернета вещей, зарегистрировали устройство, установили поток устройств между приложением на стороне устройства и службы и использовали поток для обмена данными между приложениями.

Используйте приведенные ниже ссылки для получения дополнительных сведений о потоках устройства:

> [!div class="nextstepaction"]
> [IoT Hub Device Streams (preview)](./iot-hub-device-streams-overview.md) (Потоки устройств (предварительная версия))
