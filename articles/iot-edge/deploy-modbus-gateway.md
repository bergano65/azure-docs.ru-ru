---
title: Преобразование протоколов Modbus с помощью шлюзов в Azure IoT Edge | Документация Майкрософт
description: Разрешите устройствам, использующим Modbus TCP, взаимодействовать с Центром Интернета, создав устройство шлюза IoT Edge.
author: kgremban
manager: philmea
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: kgremban
ms.openlocfilehash: 649c7f620b83464d1bb56cf4b8191b0747105f01
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457215"
---
# <a name="connect-modbus-tcp-devices-through-an-iot-edge-device-gateway"></a>Подключение устройств Modbus TCP через шлюз устройств IoT Edge

Чтобы подключить устройства IoT, которые используют протокол Modbus TCP или Modbus RTU, к центру Azure IoT, используйте устройство IoT Edge в качестве шлюза. Устройство шлюза считывает данные с устройства Modbus, а затем передает эти данные в облако с помощью поддерживаемого протокола.

![Устройства MODBUS подключаются к центру Интернета вещей через шлюз IoT Edge](./media/deploy-modbus-gateway/diagram.png)

В этой статье объясняется, как создать собственный образ контейнера для модуля Modbus (или можно использовать предварительно подготовленный пример), а затем развернуть его на устройство IoT Edge, которое будет выполнять роль шлюза.

В этой статье предполагается, что вы используете протокол Modbus TCP. Дополнительные сведения о том, как настроить модуль для поддержки Modbus RTU, см. в проекте [модуля Modbus для Azure IoT Edge](https://github.com/Azure/iot-edge-modbus) в GitHub.

## <a name="prerequisites"></a>предварительным требованиям
* Устройство Azure IoT Edge. Пошаговое руководство по настройке одного из них см. в статье [развертывание Azure IOT EDGE в Windows](quickstart.md) или [Linux](quickstart-linux.md).
* Строка подключения первичного ключа для устройства IoT Edge.
* Физическое устройство или имитация устройства, которые поддерживают Modbus TCP.

## <a name="prepare-a-modbus-container"></a>Подготовка контейнера Modbus

Если вы хотите протестировать функции шлюза Modbus, корпорация Майкрософт предлагает использовать пример модуля. Вы можете получить доступ к модулю из Azure Marketplace, [Modbus](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft_iot.edge-modbus?tab=Overview)или с помощью URI образа **MCR.Microsoft.com/azureiotedge/Modbus:1.0**.

Если вы хотите создать собственный модуль и настроить его для своей среды, в проекте GitHub можно найти [модуль Modbus для Azure IoT Edge](https://github.com/Azure/iot-edge-modbus) с открытым кодом. Следуйте инструкциям в этом проекте, чтобы создать образ контейнера. Чтобы создать образ контейнера, см. статью [Разработка C# модулей в Visual Studio](how-to-visual-studio-develop-csharp-module.md) или [Разработка модулей в Visual Studio Code](how-to-vs-code-develop-module.md). В этих статьях содержатся инструкции по созданию новых модулей и публикации образов контейнеров в реестре.

## <a name="try-the-solution"></a>Попробуйте решение

В этом разделе описывается развертывание примера модуля Modbus Майкрософт на устройстве IoT Edge.

1. Найдите нужный Центр Интернета вещей на [портале Azure](https://portal.azure.com/).

2. Щелкните **IoT Edge** и выберите устройство IoT Edge.

3. Щелкните **Set Modules** (Настроить модули).

4. Добавьте модуль Modbus:

   1. Щелкните **Добавить** и выберите **Модуль IoT Edge**.

   2. В поле **Имя** введите modbus.

   3. В поле **Изображение** введите URI образа в примере контейнера: `mcr.microsoft.com/azureiotedge/modbus:1.0`.

   4. Установите флажок **Включить**, чтобы обновить требуемые свойства двойника модуля.

   5. Скопируйте следующий JSON в текстовое поле. Замените значение **SlaveConnection** IPv4-адресом устройства Modbus.

      ```JSON
      {
        "properties.desired":{
          "PublishInterval":"2000",
          "SlaveConfigs":{
            "Slave01":{
              "SlaveConnection":"<IPV4 address>","HwId":"PowerMeter-0a:01:01:01:01:01",
              "Operations":{
                "Op01":{
                  "PollingInterval": "1000",
                  "UnitId":"1",
                  "StartAddress":"40001",
                  "Count":"2",
                  "DisplayName":"Voltage"
                }
              }
            }
          }
        }
      }
      ```

   6. Щелкните **Сохранить**.

5. Вернитесь к окну **добавления модулей** и нажмите кнопку **Далее**.

7. На шаге **указания маршрутов** скопируйте следующий код JSON в текстовое поле. Этот маршрут отправляет все сообщения, собранные модулем Modbus, в Центр Интернета вещей. В этом маршруте **модбусаутпут** — это конечная точка, которую Модуль Modbus использует для вывода данных, а **$upstream** — специальное назначение, которое сообщает IOT Edge концентратору о необходимости отправки сообщений в центр Интернета вещей.

   ```JSON
   {
     "routes": {
       "modbusToIoTHub":"FROM /messages/modules/modbus/outputs/modbusOutput INTO $upstream"
     }
   }
   ```

8. Нажмите кнопку **Далее**.

9. В окне **Review Deployment** (Проверка развертывания) выберите **Отправить**.

10. Вернитесь на страницу сведений об устройстве и выберите **Обновить**. Вы должны увидеть новый модуль **modbus** который работает вместе со средой выполнения IoT Edge.

## <a name="view-data"></a>Просмотр данных
Просмотр данных, поступающих через модуль modbus:
```cmd/sh
iotedge logs modbus
```

Данные телеметрии, которые передает устройство, можно также просматривать, используя [расширение "Набор средств Центра Интернета вещей Azure" для Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) (ранее называлось расширением "Набор средств для Интернета вещей Azure").

## <a name="next-steps"></a>Дополнительная информация

- Дополнительные сведения о том, как IoT Edge устройства могут выступать в качестве шлюзов, см. в разделе [Создание устройства IOT EDGE, работающего в качестве прозрачного шлюза](./how-to-create-transparent-gateway.md).
- Дополнительные сведения о работе модулей IoT Edge см. в разделе [Знакомство с Azure IOT Edgeными модулями](iot-edge-modules.md).
