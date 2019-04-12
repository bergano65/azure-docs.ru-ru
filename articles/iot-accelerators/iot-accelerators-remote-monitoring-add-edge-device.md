---
title: Добавление устройства Edge в решение для удаленного мониторинга Azure | Документация Майкрософт
description: В этой статье описывается, как добавить устройство IoT Edge в акселератор решения для удаленного мониторинга.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/09/2018
ms.topic: conceptual
ms.openlocfilehash: d34ac159a216c5c77214b4c8b799a233c3671235
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/24/2019
ms.locfileid: "56749551"
---
# <a name="add-an-iot-edge-device-to-your-remote-monitoring-solution-accelerator"></a>Добавление устройства IoT Edge в акселератор решения для удаленного мониторинга

Чтобы добавить устройство [IoT Edge](../iot-edge/about-iot-edge.md) в акселератор решений, выполните два шага ниже:

1. Добавить устройство Edge на **Device Explorer** страницы в удаленный мониторинг решения accelerator веб-интерфейса.
1. Установите среду выполнения IoT Edge на устройстве Edge.

## <a name="add-the-iot-edge-device"></a>Добавление устройства IoT Edge

Чтобы добавить устройство IoT Edge в акселератор решения для удаленного мониторинга, перейдите на страницу **Device Explorer** в веб-интерфейсе и щелкните **+ Новое устройство**.

На панели **Новое устройство** выберите **Устройство IoT Edge**. Для остальных параметров можно оставить значения по умолчанию. Нажмите кнопку **Применить**:

![Добавление устройства IoT Edge](media/iot-accelerators-remote-monitoring-add-edge-device/addedgedevice.png)

### <a name="alternative-ways-to-add-an-iot-edge-device"></a>Альтернативные способы добавления устройства IoT Edge

Вы также можете зарегистрировать устройство IoT Edge непосредственно с помощью экземпляра Центра Интернета вещей в акселераторе решений. Вам потребуется имя центра Интернета вещей в акселераторе решений для работы с руководствами, указанными ниже:

- [Регистрация нового устройства Azure IoT Edge на портале Azure](../iot-edge/how-to-register-device-portal.md)
- [Регистрация нового устройства Azure IoT Edge с помощью Azure CLI](../iot-edge/how-to-register-device-cli.md)
- [Регистрация нового устройства Azure IoT Edge с помощью Visual Studio Code](../iot-edge/how-to-register-device-vscode.md)

При регистрации устройства непосредственно с центром Интернета вещей в решение удаленного мониторинга, он отображается на **Device Explorer** страницы в веб-Интерфейсе.

## <a name="install-the-iot-edge-runtime"></a>Установка среды выполнения IoT Edge

Перед развертыванием модулей на устройство Edge необходимо установить среду выполнения IoT Edge на реальном устройстве. В указанных ниже руководствах объясняется, как установить среду выполнения на распространенных платформах устройства:

- [Установка среды выполнения Azure IoT Edge в Linux (x64)](../iot-edge/how-to-install-iot-edge-linux.md)
- [Установка среды выполнения Azure IoT Edge в Linux (ARM32v7/armhf)](../iot-edge/how-to-install-iot-edge-linux-arm.md)
- [Установка среды выполнения Azure IoT Edge в Windows для использования с контейнерами Windows](../iot-edge/how-to-install-iot-edge-windows-with-windows.md)
- [Установка среды выполнения Azure IoT Edge в ОС Windows для использования с контейнерами Linux](../iot-edge/how-to-install-iot-edge-windows-with-linux.md)
- [Установка среды выполнения Azure IoT Edge в ОС Windows IoT Базовая (предварительная версия)](../iot-edge/how-to-install-iot-core.md)

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы подготовили устройство IoT Edge, можно развернуть на нем модули. Ознакомьтесь со статьей [Import an IoT Edge package into your Remote Monitoring solution accelerator](iot-accelerators-remote-monitoring-import-edge-package.md) (Импорт пакета IoT Edge в акселератор решения для удаленного мониторинга).
