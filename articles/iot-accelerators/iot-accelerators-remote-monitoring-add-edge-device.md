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
ms.openlocfilehash: 88d732cc3feaa2c9a528443d4a783227f3d8e641
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/01/2020
ms.locfileid: "96446805"
---
# <a name="add-an-iot-edge-device-to-your-remote-monitoring-solution-accelerator"></a>Добавление устройства IoT Edge в акселератор решения для удаленного мониторинга

Чтобы добавить устройство [IoT Edge](../iot-edge/about-iot-edge.md) в акселератор решений, выполните два шага ниже:

1. Добавьте пограничное устройство на странице **Device Explorer** в веб-интерфейсе акселератора решений для удаленного мониторинга.
1. Установите среду выполнения IoT Edge на устройстве Edge.

## <a name="add-the-iot-edge-device"></a>Добавление устройства IoT Edge

Чтобы добавить устройство IoT Edge в акселератор решения для удаленного мониторинга, перейдите на страницу **Device Explorer** в веб-интерфейсе и щелкните **+ Новое устройство**.

На панели **Новое устройство** выберите **Устройство IoT Edge**. Для остальных параметров можно оставить значения по умолчанию. Нажмите кнопку **Применить**:

![Добавление устройства IoT Edge](media/iot-accelerators-remote-monitoring-add-edge-device/addedgedevice.png)

### <a name="alternative-ways-to-add-an-iot-edge-device"></a>Альтернативные способы добавления устройства IoT Edge

Вы также можете зарегистрировать устройство IoT Edge непосредственно с помощью экземпляра Центра Интернета вещей в акселераторе решений. Вам потребуется имя центра Интернета вещей в акселераторе решений для работы с руководствами, указанными ниже:

- [Регистрация нового устройства Azure IoT Edge на портале Azure](../iot-edge/how-to-manual-provision-symmetric-key.md?tabs=azure-portal%2Cwindows)
- [Регистрация нового устройства Azure IoT Edge с помощью Azure CLI](../iot-edge/how-to-manual-provision-symmetric-key.md?tabs=azure-cli%2Cwindows)
- [Регистрация нового устройства Azure IoT Edge с помощью Visual Studio Code](../iot-edge/how-to-manual-provision-symmetric-key.md?tabs=visual-studio-code%2Cwindows)

При регистрации устройства непосредственно в центре Интернета вещей в акселераторе решений для удаленного мониторинга оно отображается на странице **Device Explorer** в веб-интерфейсе.

## <a name="install-the-iot-edge-runtime"></a>Установка среды выполнения IoT Edge

Перед развертыванием модулей на устройство Edge необходимо установить среду выполнения IoT Edge на реальном устройстве. В указанных ниже руководствах объясняется, как установить среду выполнения на распространенных платформах устройства:

- [Установка среды выполнения Azure IoT Edge в Linux (x64)](../iot-edge/how-to-install-iot-edge.md)
- [Установка среды выполнения Azure IoT Edge в Linux (ARM32v7/armhf)](../iot-edge/how-to-install-iot-edge.md)
- [Установка среды выполнения Azure IoT Edge в Windows для использования с контейнерами Windows](../iot-edge/how-to-install-iot-edge.md)
- [Установка среды выполнения Azure IoT Edge в ОС Windows для использования с контейнерами Linux](../iot-edge/how-to-install-iot-edge.md)
- [Установка среды выполнения Azure IoT Edge в ОС Windows IoT Базовая (предварительная версия)](../iot-edge/how-to-install-iot-edge.md)

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы подготовили устройство IoT Edge, можно развернуть на нем модули. Ознакомьтесь со статьей [Import an IoT Edge package into your Remote Monitoring solution accelerator](iot-accelerators-remote-monitoring-import-edge-package.md) (Импорт пакета IoT Edge в акселератор решения для удаленного мониторинга).