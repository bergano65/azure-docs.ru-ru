---
title: Преобразование протоколов Modbus с помощью шлюзов в Azure IoT Edge | Документация Майкрософт
description: Разрешите устройствам, использующим Modbus TCP, взаимодействовать с Центром Интернета, создав устройство шлюза IoT Edge.
author: kgremban
manager: philmea
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: kgremban
ms.openlocfilehash: 23fbbd87230ea0a0147dc9d90c77729f4d531e98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76511150"
---
# <a name="connect-modbus-tcp-devices-through-an-iot-edge-device-gateway"></a>Подключение устройств Modbus TCP через шлюз устройств IoT Edge

Если вы хотите подключить устройства IoT, которые используют протоколы Modbus TCP или RTU, к концентратору Azure IoT, вы можете использовать устройство IoT Edge в качестве шлюза. Устройство шлюза считывает данные с устройства Modbus, а затем передает эти данные в облако с помощью поддерживаемого протокола.

![Устройства Modbus подключаются к IoT Hub через шлюз IoT Edge](./media/deploy-modbus-gateway/diagram.png)

В этой статье объясняется, как создать собственный образ контейнера для модуля Modbus (или можно использовать предварительно подготовленный пример), а затем развернуть его на устройство IoT Edge, которое будет выполнять роль шлюза.

В этой статье предполагается, что вы используете протокол Modbus TCP. Дополнительные сведения о том, как настроить модуль для поддержки Modbus RTU, см. в проекте [модуля Modbus для Azure IoT Edge](https://github.com/Azure/iot-edge-modbus) в GitHub.

## <a name="prerequisites"></a>Предварительные требования

* Устройство Azure IoT Edge. Для получения пошагового листа о том, как настроить его, [Linux](quickstart-linux.md)см. [Deploy Azure IoT Edge on Windows](quickstart.md)
* Строка подключения первичного ключа для устройства IoT Edge.
* Физическое устройство или имитация устройства, которые поддерживают Modbus TCP. Вам нужно будет знать его адрес IPv4.

## <a name="prepare-a-modbus-container"></a>Подготовка контейнера Modbus

Если вы хотите протестировать функции шлюза Modbus, корпорация Майкрософт предлагает использовать пример модуля. Вы можете получить доступ к модулю из Azure Marketplace, [Modbus](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot.edge-modbus?tab=Overview)или с изображением URI, `mcr.microsoft.com/azureiotedge/modbus:1.0`.

Если вы хотите создать собственный модуль и настроить его для своей среды, в проекте GitHub можно найти [модуль Modbus для Azure IoT Edge](https://github.com/Azure/iot-edge-modbus) с открытым кодом. Следуйте инструкциям в этом проекте, чтобы создать образ контейнера. Для создания изображения контейнера обратитесь к [модулям Develop C' в Visual Studio](how-to-visual-studio-develop-csharp-module.md) или [разработать модули в Visual Studio Code.](how-to-vs-code-develop-module.md) В этих статьях публикуются инструкции по созданию новых модулей и публикации изображений контейнеров в реестре.

## <a name="try-the-solution"></a>Попробуйте решение

В этом разделе проходит развертывание модуля Модуса от Microsoft на устройство IoT Edge.

1. Найдите нужный Центр Интернета вещей на [портале Azure](https://portal.azure.com/).

2. Щелкните **IoT Edge** и выберите устройство IoT Edge.

3. Щелкните **Set modules** (Настроить модули).

4. В разделе **Модулей IoT Edge** добавьте модуль Modbus:

   1. Нажмите на выпадение **добавить** и выберите **модуль Marketplace.**
   2. Поиск `Modbus` и выбор **модуля Modbus TCP** от корпорации Майкрософт.
   3. Модуль автоматически настраивается для концентратора IoT и отображается в списке модулей IoT Edge. Маршруты также автоматически настраиваются. Выберите **Review + create** (Просмотреть и создать).
   4. Просмотрите манифест развертывания и выберите **Создать**.

5. Выберите модуль Modbus, `ModbusTCPModule`в списке и выберите вкладку **Настройки модуля.** Необходимый JSON для модуля двойных желаемых свойств автоматически заселен.

6. Ищите свойство **SlaveConnection** в JSON и установите его значение для адреса IPv4 вашего устройства Modbus.

7. Выберите **обновление**.

8. Выберите **Обзор и создайте,** просмотрите развертывание, а затем выберите **Создать**.

9. Вернитесь на страницу сведений об устройстве и выберите **Обновить**. Вы должны увидеть `ModbusTCPModule` новый модуль, работающий вместе с временем выполнения IoT Edge.

## <a name="view-data"></a>Просмотр данных

Просмотр данных, поступающих через модуль Modbus:

```cmd/sh
iotedge logs modbus
```

Вы также можете просмотреть телеметрию, которую устройство отправляет, используя [расширение Azure IoT Hub для Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) (ранее расширение Azure IoT Toolkit).

## <a name="next-steps"></a>Дальнейшие действия

* Чтобы узнать больше о том, как устройства IoT Edge могут выступать в качестве шлюзов, [см.](./how-to-create-transparent-gateway.md)
* Для получения дополнительной информации о работе модулей IoT Edge [см.](iot-edge-modules.md)
