---
title: Сведения об управлении устройствами в среде выполнения — Azure IoT Edge | Документация Майкрософт
description: Learn how the IoT Edge runtime manages modules, security, communication, and reporting on your devices
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/01/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6382159c2a9d73b9db21dd0467be0e68cf4b4c2d
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456594"
---
# <a name="understand-the-azure-iot-edge-runtime-and-its-architecture"></a>Общие сведения о среде выполнения Azure IoT Edge и ее архитектуре

The IoT Edge runtime is a collection of programs that turn a device into an IoT Edge device. Collectively, the IoT Edge runtime components enable IoT Edge devices to receive code to run at the edge and communicate the results. 

The IoT Edge runtime is responsible for the following functions on IoT Edge devices:

* Установка и обновление рабочих нагрузок на устройстве.
* Техническое обслуживание стандартов безопасности Azure IoT Edge на устройстве.
* Ensure that [IoT Edge modules](iot-edge-modules.md) are always running.
* Передача данных о состоянии работоспособности модуля в облако для удаленного мониторинга.
* Manage communication between downstream devices and IoT Edge devices.
* Manage communication between modules on the IoT Edge device.
* Manage communication between the IoT Edge device and the cloud.

![Среда выполнения передает аналитические сведения и информацию о работоспособности модуля в Центр Интернета вещей](./media/iot-edge-runtime/Pipeline.png)

Обязанности среды выполнения IoT Edge делятся на две категории: обмен данными и управление модулями. These two roles are performed by two components that are part of the IoT Edge runtime. The *IoT Edge hub* is responsible for communication, while the *IoT Edge agent* deploys and monitors the modules. 

Агент IoT Edge и центр IoT Edge — это модули, и они, как и любые другие модули, выполняются на устройстве IoT Edge. They're sometimes referred to as the *runtime modules*. 

## <a name="iot-edge-hub"></a>Концентратор IoT Edge

Центр IoT Edge — это один из двух модулей, составляющих среду выполнения Azure IoT Edge. Он действует в качестве локального прокси-сервера для Центра Интернета вещей, предоставляя те же конечные точки протокола, что и Центр Интернета вещей. Эта согласованность означает, что клиенты (устройства или модули) могут подключаться к среде выполнения IoT Edge так же, как к Центру Интернета вещей. 

>[!NOTE]
> IoT Edge hub supports clients that connect using MQTT or AMQP. Он не поддерживает клиенты, использующие HTTP. 

Центр IoT Edge не является полной версией выполняемого локально Центра Интернета вещей. Есть некоторые вещи, которые центр IoT Edge автоматически делегирует Центру Интернета вещей. Например, центр IoT Edge переадресовывает запросы на проверку подлинности в Центр Интернета вещей при первом подключении устройства. После установки первого подключения сведения о безопасности будут кэшированы локально центром IoT Edge. Последующие подключения от этого устройства не проходят проверку подлинности в облаке. 

Чтобы сократить пропускную способность, используемую решением IoT Edge, центр IoT Edge оптимизирует количество фактических подключений к облаку. IoT Edge hub takes logical connections from clients like modules or downstream devices and combines them for a single physical connection to the cloud. Сведения об этом процессе прозрачны для остальной части решения. Клиенты думают, что имеют собственное подключение к облаку, несмотря на то что передача данных выполняется через одно подключение. 

![Центр IoT Edge — это шлюз между физическими устройствами и Центром Интернета вещей](./media/iot-edge-runtime/Gateway.png)

Центр IoT Edge может определить, подключен ли он к Центру Интернета вещей. Если подключение потеряно, центр IoT Edge сохранит сообщения или операции обновления двойников локально. Когда подключение будет снова установлено, он выполнит синхронизацию всех данных. Расположение, используемое для этого временного кэша, определяется свойством двойника модуля в центре IoT Edge. Размер кэша не ограничен и будет увеличиваться, пока у устройства есть емкость хранилища. For more information, see [Offline capabilities](offline-capabilities.md).

### <a name="module-communication"></a>Обмен данными между модулями

Центр IoT Edge облегчает обмен данными между модулями. Благодаря использованию центра IoT Edge в качестве брокера сообщений модули независимы друг от друга. Модулям необходимо указывать только те входы, на которые они принимают сообщения, и выходы, на которые они записывают сообщения. A solution developer can stitch these inputs and outputs together so that the modules process data in the order specific to that solution. 

![Центр IoT Edge облегчает обмен данными между модулями](./media/iot-edge-runtime/module-endpoints.png)

Для отправки данных в центр IoT Edge модуль вызывает метод SendEventAsync. Первый аргумент указывает, в какой выход нужно отправить сообщение. The following pseudocode sends a message on **output1**:

   ```csharp
   ModuleClient client = await ModuleClient.CreateFromEnvironmentAsync(transportSettings); 
   await client.OpenAsync(); 
   await client.SendEventAsync("output1", message); 
   ```

Для получения сообщений зарегистрируйте обратный вызов, который обрабатывает сообщения, приходящие в определенный вход. The following pseudocode registers the function messageProcessor to be used for processing all messages received on **input1**:

   ```csharp
   await client.SetInputMessageHandlerAsync("input1", messageProcessor, userContext);
   ```

For more information about the ModuleClient class and its communication methods, see the API reference for your preferred SDK language: [C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet), [C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h), [Python](https://docs.microsoft.com/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient?view=azure-python), [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable), or [Node.js](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest).

Разработчик решений отвечает за указание правил, определяющих, как центр IoT Edge передает сообщения между модулями. Routing rules are defined in the cloud and pushed down to IoT Edge hub in its module twin. Тот же синтаксис для маршрутов Центра Интернета вещей используется при определении маршрутов между модулями в Azure IoT Edge. For more information, see [Learn how to deploy modules and establish routes in IoT Edge](module-composition.md).   

![Маршруты между модулями проходят через центр IoT Edge](./media/iot-edge-runtime/module-endpoints-with-routes.png)

## <a name="iot-edge-agent"></a>Агент IoT Edge

Агент IoT Edge — это другой модуль, который составляет среду выполнения Azure IoT Edge. Он отвечает за создание экземпляров модулей, обеспечивая их работу и сообщение о состоянии модулей обратно в Центр Интернета вещей. This configuration data is written as a property of the IoT Edge agent module twin. 

[Управляющая программа безопасности IoT Edge](iot-edge-security-manager.md) запускает агент IoT Edge при запуске устройства. Агент получает свой двойник модуля из Центра Интернета вещей и проверяет манифест развертывания. Манифест развертывания — это файл JSON, который объявляет необходимые для запуска модули. 

Каждый элемент манифеста развертывания содержит определенные сведения о модуле и используется агентом IoT Edge для управления жизненным циклом модуля. Ниже приведены некоторые из наиболее интересных свойств: 

* **settings.image**. Образ контейнера, который агент IoT Edge использует для запуска модуля. Агент IoT Edge должен быть настроен с учетными данными для реестра контейнеров, если образ защищен паролем. Учетные данные для реестра контейнеров можно настроить удаленно с помощью манифеста развертывания или на самом устройстве IoT Edge, обновив файл `config.yaml` в папке программы IoT Edge.
* **settings.createOptions** – A string that is passed directly to the Moby container daemon when starting a module’s container. Adding options in this property allows for advanced configurations like port forwarding or mounting volumes into a module’s container.  
* **status**. Состояние, в котором агент IoT Edge помещает модуль. Usually, this value is set to *running* as most people want the IoT Edge agent to immediately start all modules on the device. Однако можно указать начальное состояние модуля, который должен быть остановлен, и дождаться нужного времени, чтобы сообщить агенту IoT Edge о запуске модуля. The IoT Edge agent reports the status of each module back to the cloud in the reported properties. Различие между требуемым свойством и сообщаемым свойством является индикатором некорректно работающего устройства. Ниже перечислены поддерживаемые состояния:
   * Downloading;
   * Выполнение
   * Unhealthy;
   * Failed
   * Остановлена
* **restartPolicy**. То, как агент IoT Edge перезапускает модуль. Возможные значения:
   * `never` – The IoT Edge agent never restarts the module.
   * `on-failure` - If the module crashes, the IoT Edge agent restarts it. Если модуль завершается без сбоев, агент IoT Edge не перезапускает его.
   * `on-unhealthy` - If the module crashes or is considered unhealthy, the IoT Edge agent restarts it.
   * `always` - If the module crashes, is considered unhealthy, or shuts down in any way, the IoT Edge agent restarts it. 
* **imagePullPolicy** - Whether the IoT Edge agent attempts to pull the latest image for a module automatically or not. If you don't specify a value, the default is *onCreate*. Возможные значения: 
   * `on-create` - When starting a module or updating a module based on a new deployment manifest, the IoT Edge agent will attempt to pull the module image from the container registry.
   * `never` - The IoT Edge agent will never attempt to pull the module image from the container registry. The expectation is that the module image is cached on the device, and any module image updates are made manually or managed by a third-party solution. 

Агент IoT Edge отправляет ответ среды выполнения в центр Интернета вещей. Ниже перечислены возможные ответы:
  * 200 – OK
  * 400 — конфигурация развертывания имеет неправильный формат или недопустима.
  * 417 - The device doesn't have a deployment configuration set.
  * 412 — версия схемы в конфигурации развертывания недопустима.
  * 406 — устройство IoT Edge работает в автономном режиме или не отправляет отчеты о состоянии.
  * 500 — в среде выполнения IoT Edge произошла ошибка.

For more information, see [Learn how to deploy modules and establish routes in IoT Edge](module-composition.md).   

### <a name="security"></a>Безопасность

Агент IoT Edge играет важную роль в обеспечении безопасности устройства IoT Edge. Например, он выполняет такие действия, как проверка образа модуля перед запуском. 

For more information about the Azure IoT Edge security framework, read about the [IoT Edge security manager](iot-edge-security-manager.md).

## <a name="next-steps"></a>Дальнейшие действия

[Общие сведения о модулях IoT Edge Azure](iot-edge-modules.md)
